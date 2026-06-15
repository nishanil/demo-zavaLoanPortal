# Configuration & Externalized Settings Inventory

ZavaLoanPortal relies on a single `web.config` file as its only configuration source, with no environment-specific overrides, external configuration servers, secret stores, or feature flag frameworks.

## Configuration Sources

| Source | Type | Path/Location | Notes |
|--------|------|--------------|-------|
| web.config | XML configuration file | `/web.config` | Primary and only configuration source; contains connection strings, app settings, Forms Auth, and ASP.NET runtime config |
| ZavaLoanPortal.csproj | MSBuild project file | `/ZavaLoanPortal.csproj` | Defines build targets (Debug/Release), framework version (4.8), and assembly references |
| Dockerfile | Container build file | `/Dockerfile` | Single-stage Mono 6.12 container; no runtime environment variable injection |

No Spring Cloud Config, Azure App Configuration, AWS AppConfig, HashiCorp Vault, or Kubernetes ConfigMaps/Secrets are configured.

## Build Profiles

| Profile | Activation | Purpose | Key Dependencies/Plugins |
|---------|-----------|---------|--------------------------|
| Debug | Default / manual `Configuration=Debug` | Development build with debug symbols | `OutputPath: bin\` |
| Release | Manual `Configuration=Release` | Production/optimized build | `OutputPath: bin\` |

Both profiles share the same `OutputPath`. No conditional compilation symbols or MSBuild property differences are configured beyond the default Debug/Release distinction. In the Dockerfile, the application is built at container build time using the Mono C# compiler (`mcs`) directly — no MSBuild or NuGet restore is executed.

## Runtime Profiles

| Profile | Activation Method | Config Files | Key Overrides |
|---------|-----------------|-------------|--------------|
| Single (no profiles) | N/A — no profile mechanism configured | web.config only | N/A |

No `appsettings.{Environment}.json`, `web.{Environment}.config` transforms, or `ASPNETCORE_ENVIRONMENT`/`APP_ENV` environment variable handling are present. All environments (development, staging, production) share the same `web.config` with hardcoded values.

## Properties Inventory

### ZavaLoanPortal — web.config

| Property Key | Default Value | Profiles | Source |
|-------------|--------------|---------|--------|
| `connectionStrings/ZavaBankDb` | `Server=sqlserver,1433;Database=ZavaBankDB;User Id=sa;******;TrustServerCertificate=true;` | All | web.config |
| `appSettings/AuthGatewayLoginUrl` | `http://localhost/auth/Login.aspx` | All | web.config |
| `appSettings/AuthGatewayLogoutUrl` | `http://localhost/auth/Logout.aspx` | All | web.config |
| `appSettings/LoanOriginationApiUrl` | `http://zava-loan-origination-api:8080/api/loanapplications` | All | web.config |
| `system.web/compilation[@debug]` | `true` | All | web.config |
| `system.web/compilation[@targetFramework]` | `4.8` | All | web.config |
| `system.web/httpRuntime[@targetFramework]` | `4.8` | All | web.config |
| `system.web/customErrors[@mode]` | `Off` | All | web.config |
| `system.web/authentication[@mode]` | `Forms` | All | web.config |
| `system.web/forms[@loginUrl]` | `~/Login.aspx` | All | web.config |
| `system.web/forms[@timeout]` | `30` (minutes) | All | web.config |
| `system.web/forms[@name]` | `.ZAVAAUTH` | All | web.config |
| `system.web/forms[@protection]` | `All` | All | web.config |
| `system.web/forms[@slidingExpiration]` | `true` | All | web.config |
| `system.web/machineKey[@validation]` | `SHA1` | All | web.config |
| `system.web/machineKey[@decryption]` | `AES` | All | web.config |
| `system.web/authorization` | `<deny users="?" />` (global deny for anonymous) | All | web.config |

## Startup Parameters & Resource Requirements

| Service | Runtime Options | Memory | CPU | Instance Count |
|---------|---------------|--------|-----|---------------|
| ZavaLoanPortal (Docker/Mono) | `xsp4 --port 8080 --address 0.0.0.0 --nonstop` | Not specified | Not specified | 1 |
| ZavaLoanPortal (IIS, non-Docker) | Standard IIS worker process | Not specified | Not specified | 1 |

No JVM heap settings (not applicable). No `-D` system properties, Docker `mem_limit`, or Kubernetes resource quotas are defined.

## Startup Dependency Chain

ZavaLoanPortal has no automated startup dependency chain or wait mechanism:

1. **ZavaLoanPortal** starts (xsp4 / IIS)
2. On first page request, the application attempts to open SQL Server connections to `sqlserver:1433` — no readiness probe or retry mechanism is configured; failed connections will surface as unhandled exceptions.
3. External services (ZavaAuthGateway at `http://localhost/auth/`, Loan Origination API at `http://zava-loan-origination-api:8080`) are not health-checked or awaited at startup.

No `dockerize`, `wait-for-it`, Docker Compose `depends_on`/`healthcheck`, or Kubernetes readiness probes are configured.

## Secrets & Sensitive Configuration

| Secret Reference | Type | Stored In | Notes |
|-----------------|------|----------|-------|
| `ZavaBankDb` connection string password | Database credential | web.config (plaintext) | Uses SA account — highest-privilege SQL Server login |
| `machineKey/validationKey` | Forms Auth signing key | web.config (plaintext static value) | Static key means cookie forgery if key is leaked |
| `machineKey/decryptionKey` | Forms Auth encryption key | web.config (plaintext static value) | Static AES key; no key rotation mechanism |

**All secrets are stored as plaintext in `web.config`.** No Azure Key Vault, AWS Secrets Manager, HashiCorp Vault, DPAPI encryption, or environment variable substitution is used.

### Secrets Provisioning Workflow

There is no secrets provisioning workflow. All sensitive values (database SA password, machine key pair) are hardcoded in `web.config` and committed to source control. To reach a production-ready secrets management posture, the following changes are recommended:

- Move connection strings and API keys to environment variables or a secret store (Azure Key Vault, AWS Secrets Manager)
- Replace the static `machineKey` with environment-variable-based or managed-identity-based key derivation
- Rotate the SA database account to a least-privilege application account
- Exclude `web.config` secrets from source control via `.gitignore` or config transforms

## Feature Flags

No feature flag framework is configured. No `@ConditionalOnProperty`, .NET `IFeatureManager`, LaunchDarkly, Unleash, or custom toggle mechanisms are present.

| Flag Name | Default | Controlled By |
|-----------|---------|--------------|
| (none) | N/A | N/A |

## Framework & Runtime Versions

| Component | Version | Source |
|-----------|---------|--------|
| .NET Framework | 4.8 | ZavaLoanPortal.csproj, web.config |
| ASP.NET Web Forms | 4.8 (System.Web) | ZavaLoanPortal.csproj |
| C# Language | 7.3 (default for .NET 4.8 toolchain) | Implied by .NET 4.8 |
| Mono (Docker runtime) | 6.12 | Dockerfile `FROM mono:6.12` |
| XSP4 (Mono web server) | Bundled with Mono 6.12 | Dockerfile `apt-get install mono-xsp4` |
| Mono C# Compiler (mcs) | Bundled with Mono 6.12 | Dockerfile build step |
| System.Data.SqlClient | .NET 4.8 BCL | ZavaLoanPortal.csproj `<Reference Include="System.Data" />` |
| System.Web.Extensions | .NET 4.8 BCL | ZavaLoanPortal.csproj `<Reference Include="System.Web.Extensions" />` |
