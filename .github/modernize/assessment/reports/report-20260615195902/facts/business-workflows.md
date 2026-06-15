# Core Business Workflows

ZavaLoanPortal is a customer-facing loan application portal that allows authenticated users to browse loan products, submit multi-step loan applications, and review their application history.

## Domain Entities

| Entity | Service / Bounded Context | Description | Key Relationships |
|--------|--------------------------|-------------|------------------|
| LoanProduct | Loan Catalog | A type of loan product available to customers (e.g., personal loan, home loan) | Referenced by LoanApplication |
| LoanApplication | Loan Origination | A customer's request to borrow money, including amount, term, and purpose | Belongs to a LoanProduct; linked to a Customer by ID |
| Customer | External (referenced by ID) | The borrowing party; identity managed by an external system | Referenced in LoanApplication as CustomerID |

## Service-to-Domain Mapping

| Service | Domain Context | Owned Entities | External Dependencies |
|---------|---------------|---------------|----------------------|
| ZavaLoanPortal | Loan Application & Origination | LoanApplication (write/read), LoanProduct (read-only) | ZavaAuthGateway (authentication), Loan Origination API (downstream processing), External Customer System (CustomerID) |
| ZavaAuthGateway | Identity & Access | User identity (external) | None visible from this codebase |
| Loan Origination API | Downstream Loan Processing | Unknown (external service) | Receives XML payload from ZavaLoanPortal |

## Primary Workflows

### Workflow 1: User Authentication

A customer who is not authenticated is automatically redirected to the external ZavaAuthGateway service. Once authenticated, the portal redirects the user back to the originally requested page. Logout also delegates to the external gateway.

**Steps:**
1. User requests `Default.aspx` (or any protected page)
2. Portal checks Forms Authentication cookie — if absent, redirects to `Login.aspx`
3. `Login.aspx` constructs the AuthGateway login URL with the original page as `ReturnUrl`
4. Browser is redirected to AuthGateway; the gateway handles credential collection
5. On success, the gateway issues the `.ZAVAAUTH` cookie and redirects back to the portal
6. Portal validates the cookie and serves the requested page
7. On logout, `Logout.aspx` signs the user out locally and redirects to the AuthGateway logout URL

### Workflow 2: Loan Application Submission (Multi-Step Wizard)

The primary business workflow: a customer completes a 5-step wizard to submit a loan application. On completion, the application is recorded in the database and forwarded to the Loan Origination API.

**Steps:**
1. **Step 1 — Personal Info**: Customer enters CustomerID, first name, last name, and email
2. **Step 2 — Employment**: Customer enters employer, job title, years employed, and annual income
3. **Step 3 — Loan Details**: Customer selects an active loan product, enters requested amount, term (months), and purpose
4. **Validation (Step 3 → Step 4)**: Application verifies that requested amount is a valid decimal and term is a valid integer; if invalid, navigation is cancelled and an error message is shown
5. **Step 4 — Review**: A summary is displayed: customer name, amount, and term for confirmation
6. **Step 5 — Submit**: Customer clicks "Finish"
7. Portal inserts the application into `LoanApplications` with status `Submitted`, current date, and the authenticated user's name as `AssignedOfficer`
8. Portal sends an XML payload to the Loan Origination API via HTTP POST (fire-and-forget; errors are silently ignored)
9. Loan history grid is refreshed to reflect the new application

**Employment and personal details collected in Steps 1–2 (name, email, employer, income) are NOT persisted to the database.**

### Workflow 3: Loan History Viewing

On every page load and after each wizard submission, the portal retrieves and displays the 25 most recent loan applications ordered by date descending. Users can click a "Review" button on any row, which sets a status label — no further business action is currently implemented for this button.

## Cross-Service Data Flows

ZavaLoanPortal does not aggregate data from multiple services at query time. Data flows are outbound and one-directional:

- **Loan Origination API**: On wizard completion, the portal pushes a minimal XML document containing `customerId`, `loanProductId`, `requestedAmount`, and `termMonths`. The portal does not read a response — errors are swallowed. The Loan Origination API is the downstream system of record for origination processing.
- **ZavaAuthGateway**: Authentication is entirely redirect-based. No data is exchanged programmatically; the portal only reads the resulting Forms Authentication cookie.
- **Customer data**: The portal stores only `CustomerID` (an integer), relying on an external customer system as the source of truth. Customer personal details entered in the wizard are transient (form fields only).

If the Loan Origination API is unavailable, the application is still written to the database and the user sees "Application submitted." — there is no retry, compensating action, or user notification of the API failure.

## Business Workflow Sequence

```mermaid
sequenceDiagram
    participant Customer as "Customer (Browser)"
    participant Portal as "ZavaLoanPortal"
    participant AuthGW as "ZavaAuthGateway"
    participant DB as "SQL Server (ZavaBankDB)"
    participant LoanAPI as "Loan Origination API"

    Customer->>Portal: GET /Default.aspx (no auth cookie)
    Portal-->>Customer: Redirect to Login.aspx
    Customer->>Portal: GET /Login.aspx
    Portal-->>Customer: Redirect to AuthGateway login URL
    Customer->>AuthGW: Complete login
    AuthGW-->>Customer: Redirect with .ZAVAAUTH cookie

    Customer->>Portal: GET /Default.aspx (authenticated)
    Portal->>DB: Load active loan products
    DB-->>Portal: LoanProduct list
    Portal->>DB: Load last 25 loan applications
    DB-->>Portal: LoanApplication history
    Portal-->>Customer: Render wizard + history grid

    Note over Customer,Portal: Customer completes wizard steps 1-3

    Customer->>Portal: POST - Wizard Step 3 Next
    alt Invalid amount or term
        Portal-->>Customer: Validation error, stay on Step 3
    else Valid input
        Portal-->>Customer: Advance to Review step
    end

    Customer->>Portal: POST - Wizard Finish (Step 5)
    Portal->>DB: INSERT LoanApplication (Status=Submitted, AssignedOfficer=username)
    DB-->>Portal: Insert confirmed
    Portal->>LoanAPI: POST XML loanApplication payload
    alt Loan API available
        LoanAPI-->>Portal: HTTP 2xx (response discarded)
    else Loan API unavailable or error
        Note over Portal: Exception silently swallowed; no retry or user notification
    end
    Portal->>DB: Reload last 25 loan applications
    DB-->>Portal: Updated history
    Portal-->>Customer: Submission confirmed + refreshed history grid
```

## Business Rules & Decision Logic

### Validation Rules

- **Loan amount**: Must be a valid `decimal` — validated at wizard Step 3 → Step 4 transition. Invalid values cancel navigation and display "Enter valid loan amount and term."
- **Term months**: Must be a valid `int` — validated at the same Step 3 → Step 4 transition, checked jointly with loan amount.
- **Loan product selection**: Only active loan products (`IsActive=1`) are presented in the dropdown; no further validation is performed on the submitted product ID.
- **Authentication**: All pages except `Login.aspx` and `Logout.aspx` deny anonymous users (`<deny users="?" />`).

### Decision Logic

- **Authentication gate**: On `Default.aspx` `Page_Load`, if `Request.IsAuthenticated` is false, the user is immediately redirected to `Login.aspx` with the original URL as `ReturnUrl` (defense-in-depth on top of the web.config authorization rule).
- **Wizard step review summary**: On transition to the Review step (index 3), a HTML summary is dynamically built from form field values and rendered via `Server.HtmlEncode`.
- **Review button**: The `GridView` row command `ReviewApplication` sets a status label but performs no data mutation — the business action is not fully implemented.

### State Transitions

| Entity | States | Trigger |
|--------|--------|---------|
| LoanApplication | `Submitted` (initial and only state recorded by this application) | Wizard Finish button click |

No further state transitions (e.g., Approved, Rejected, Under Review) are implemented within this codebase — downstream state changes are expected to be managed by the Loan Origination API or a separate workflow system.

### Cross-Cutting Concerns

- **Transactions**: No explicit transaction management (`TransactionScope` or similar) is used. The database insert and the API call are not atomic — a successful DB insert followed by an API failure leaves the application in an inconsistent state between the two systems.
- **Error handling**: The `SubmitApi` method wraps the HTTP call in an empty `catch {}` block — all API errors are silently suppressed with no logging, alerting, or user feedback.
- **Audit trail**: `ApplicationDate`, `CreatedDate`, and `ModifiedDate` are set at insert time. `AssignedOfficer` is set to the authenticated username, providing a basic attribution trail. No update history or change-log table is maintained.
- **Authorization**: Authorization is binary (authenticated vs anonymous) — no role-based access control (RBAC) or resource-ownership checks are implemented. Any authenticated user can view all 25 most recent loan applications regardless of whether they belong to that user.
