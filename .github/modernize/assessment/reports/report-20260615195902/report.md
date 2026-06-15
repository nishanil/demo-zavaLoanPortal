# ZavaLoanPortal

## Summary

| Metric | Value |
|--------|-------|
| Total Issues | 12 |
| Mandatory Blockers | 5 |
| Potential Issues | 4 |

## Component Information

| Property | Value |
|----------|-------|
| Language | C# |
| Frameworks | .NETFramework,Version=v4.8 |
| Build tools | MSBuild |

## Cloud Readiness Issues

| Issue Name | Criticality | Story Points | Occurrences |
|------------|-------------|--------------|-------------|
| Access to external resources via HTTP is detected | Potential | 3 | [2](#Access_to_external_resources_via_HTTP_is_detected) |
| Hardcoded URLs detected | Potential | 1 | [2](#Hardcoded_URLs_detected) |
| SQL database connection detected | Potential | 3 | [1](#SQL_database_connection_detected) |
| System.Data.SqlClient dependency detected | Optional | 3 | [17](#System_Data_SqlClient_dependency_detected) |
| Connection strings without configuration builders detected | Optional | 3 | [2](#Connection_strings_without_configuration_builders_detected) |
| Synchronous API usage detected | Optional | 1 | [1](#Synchronous_API_usage_detected) |

### Issue Details

<details id="Access_to_external_resources_via_HTTP_is_detected">
<summary><b>Access to external resources via HTTP is detected</b> — affected files</summary>

- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`

</details>

<details id="Hardcoded_URLs_detected">
<summary><b>Hardcoded URLs detected</b> — affected files</summary>

- `Login.aspx.cs (line 0)`
- `Logout.aspx.cs (line 0)`

</details>

<details id="SQL_database_connection_detected">
<summary><b>SQL database connection detected</b> — affected files</summary>

- `web.config`

</details>

<details id="System_Data_SqlClient_dependency_detected">
<summary><b>System.Data.SqlClient dependency detected</b> — affected files</summary>

- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `Default.aspx.cs (line 0)`
- `web.config`

</details>

<details id="Connection_strings_without_configuration_builders_detected">
<summary><b>Connection strings without configuration builders detected</b> — affected files</summary>

- `web.config`
- `web.config`

</details>

<details id="Synchronous_API_usage_detected">
<summary><b>Synchronous API usage detected</b> — affected files</summary>

- `Default.aspx.cs (line 0)`

</details>

## DotNET Upgrade Issues [View Details](scenarios/dotnet-version-upgrade/assessment.md)

| Issue Category | Criticality | Story Points | Occurrences |
|----------------|-------------|--------------|-------------|
| Binary incompatible for selected .NET version | Mandatory | 1 | [131](#Binary_incompatible_for_selected_NET_version) |
| Project file needs to be converted to SDK-style | Mandatory | 1 | [1](#Project_file_needs_to_be_converted_to_SDK-style) |
| Project's target framework(s) needs to be changed | Mandatory | 1 | [1](#Project_s_target_framework_s_needs_to_be_changed) |
| Legacy Configuration System | Mandatory | 2 | 0 |
| ASP.NET Framework (System.Web) | Mandatory | 4 | 0 |
| Source incompatible for selected .NET version | Potential | 1 | [107](#Source_incompatible_for_selected_NET_version) |

### Issue Details

<details id="Binary_incompatible_for_selected_NET_version">
<summary><b>Binary incompatible for selected .NET version</b> — affected files</summary>

- `Site.Master.designer.cs (line 0, col 426)`
- `Site.Master.designer.cs (line 0, col 349)`
- `Site.Master.designer.cs (line 0, col 289)`
- `Site.Master.designer.cs (line 0, col 226)`
- `Site.Master.designer.cs (line 0, col 164)`
- `Site.Master.designer.cs (line 0, col 103)`
- `Site.Master.cs (line 0, col 368)`
- `Site.Master.cs (line 0, col 333)`
- `Site.Master.cs (line 0, col 304)`
- `Site.Master.cs (line 0, col 244)`
- `Site.Master.cs (line 0, col 153)`
- `Site.Master.cs (line 0, col 90)`
- `Logout.aspx.cs (line 0, col 345)`
- `Logout.aspx.cs (line 0, col 226)`
- `Logout.aspx.cs (line 0, col 196)`
- `Logout.aspx.cs (line 0, col 139)`
- `Login.aspx.designer.cs (line 0, col 167)`
- `Login.aspx.designer.cs (line 0, col 102)`
- `Login.aspx.cs (line 0, col 593)`
- `Login.aspx.cs (line 0, col 496)`
- `Login.aspx.cs (line 0, col 341)`
- `Login.aspx.cs (line 0, col 288)`
- `Login.aspx.cs (line 0, col 260)`
- `Login.aspx.cs (line 0, col 186)`
- `Login.aspx.cs (line 0, col 129)`
- `Default.aspx.designer.cs (line 0, col 1236)`
- `Default.aspx.designer.cs (line 0, col 1163)`
- `Default.aspx.designer.cs (line 0, col 1092)`
- `Default.aspx.designer.cs (line 0, col 1025)`
- `Default.aspx.designer.cs (line 0, col 964)`
- `Default.aspx.designer.cs (line 0, col 898)`
- `Default.aspx.designer.cs (line 0, col 827)`
- `Default.aspx.designer.cs (line 0, col 760)`
- `Default.aspx.designer.cs (line 0, col 687)`
- `Default.aspx.designer.cs (line 0, col 618)`
- `Default.aspx.designer.cs (line 0, col 554)`
- `Default.aspx.designer.cs (line 0, col 490)`
- `Default.aspx.designer.cs (line 0, col 429)`
- `Default.aspx.designer.cs (line 0, col 365)`
- `Default.aspx.designer.cs (line 0, col 300)`
- `Default.aspx.designer.cs (line 0, col 234)`
- `Default.aspx.designer.cs (line 0, col 163)`
- `Default.aspx.designer.cs (line 0, col 98)`
- `Default.aspx.cs (line 0, col 3702)`
- `Default.aspx.cs (line 0, col 3675)`
- `Default.aspx.cs (line 0, col 3256)`
- `Default.aspx.cs (line 0, col 3210)`
- `Default.aspx.cs (line 0, col 3167)`
- `Default.aspx.cs (line 0, col 3139)`
- `Default.aspx.cs (line 0, col 2570)`
- `Default.aspx.cs (line 0, col 2740)`
- `Default.aspx.cs (line 0, col 2652)`
- `Default.aspx.cs (line 0, col 1273)`
- `Default.aspx.cs (line 0, col 2532)`
- `Default.aspx.cs (line 0, col 2464)`
- `Default.aspx.cs (line 0, col 2379)`
- `Default.aspx.cs (line 0, col 2315)`
- `Default.aspx.cs (line 0, col 1521)`
- `Default.aspx.cs (line 0, col 1467)`
- `Default.aspx.cs (line 0, col 1413)`
- `Default.aspx.cs (line 0, col 1368)`
- `Default.aspx.cs (line 0, col 936)`
- `Default.aspx.cs (line 0, col 1210)`
- `Default.aspx.cs (line 0, col 1196)`
- `Default.aspx.cs (line 0, col 1053)`
- `Default.aspx.cs (line 0, col 690)`
- `Default.aspx.cs (line 0, col 648)`
- `Default.aspx.cs (line 0, col 543)`
- `Default.aspx.cs (line 0, col 520)`
- `Default.aspx.cs (line 0, col 467)`
- `Default.aspx.cs (line 0, col 413)`
- `Default.aspx.cs (line 0, col 329)`
- `Default.aspx.cs (line 0, col 300)`
- `Default.aspx.cs (line 0, col 243)`

</details>

<details id="Project_file_needs_to_be_converted_to_SDK-style">
<summary><b>Project file needs to be converted to SDK-style</b> — affected files</summary>

- `ZavaLoanPortal.csproj`

</details>

<details id="Project_s_target_framework_s_needs_to_be_changed">
<summary><b>Project's target framework(s) needs to be changed</b> — affected files</summary>

- `ZavaLoanPortal.csproj`

</details>

<details id="Source_incompatible_for_selected_NET_version">
<summary><b>Source incompatible for selected .NET version</b> — affected files</summary>

- `Site.Master.cs (line 0, col 244)`
- `Site.Master.cs (line 0, col 153)`
- `Logout.aspx.cs (line 0, col 345)`
- `Logout.aspx.cs (line 0, col 226)`
- `Login.aspx.cs (line 0, col 395)`
- `Login.aspx.cs (line 0, col 341)`
- `Login.aspx.cs (line 0, col 288)`
- `Login.aspx.cs (line 0, col 260)`
- `Login.aspx.cs (line 0, col 186)`
- `Default.aspx.cs (line 0, col 3865)`
- `Default.aspx.cs (line 0, col 3767)`
- `Default.aspx.cs (line 0, col 3655)`
- `Default.aspx.cs (line 0, col 3618)`
- `Default.aspx.cs (line 0, col 3437)`
- `Default.aspx.cs (line 0, col 3336)`
- `Default.aspx.cs (line 0, col 3119)`
- `Default.aspx.cs (line 0, col 3082)`
- `Default.aspx.cs (line 0, col 2957)`
- `Default.aspx.cs (line 0, col 2856)`
- `Default.aspx.cs (line 0, col 2441)`
- `Default.aspx.cs (line 0, col 2432)`
- `Default.aspx.cs (line 0, col 2379)`
- `Default.aspx.cs (line 0, col 2315)`
- `Default.aspx.cs (line 0, col 2276)`
- `Default.aspx.cs (line 0, col 2235)`
- `Default.aspx.cs (line 0, col 2191)`
- `Default.aspx.cs (line 0, col 2146)`
- `Default.aspx.cs (line 0, col 1886)`
- `Default.aspx.cs (line 0, col 1785)`
- `Default.aspx.cs (line 0, col 690)`
- `Default.aspx.cs (line 0, col 413)`
- `Default.aspx.cs (line 0, col 329)`
- `Default.aspx.cs (line 0, col 300)`

</details>

---

## Codebase Insights

> **Note:** These documents are generated by AI and may contain inaccuracies or incomplete information. Please review carefully.

1. **[Architecture Diagram](facts/architecture-diagram.md)** — Understand the big picture: system layers and component relationships
2. **[Dependency Map](facts/dependency-map.md)** — Know what the project depends on and where the risks are
3. **[API & Service Contracts](facts/api-service-contracts.md)** — See how services communicate and what contracts they expose
4. **[Data Architecture](facts/data-architecture.md)** — Explore data models, storage, and data flow patterns
5. **[Configuration Inventory](facts/configuration-inventory.md)** — Review how the application is configured across environments
6. **[Business Workflows](facts/business-workflows.md)** — Trace end-to-end business processes and domain logic

[Share feedback](https://aka.ms/ghcp-appmod/feedback)
