# .NET 10.0 Upgrade Plan

## Execution Steps

Execute steps below sequentially one by one in the order they are listed.

1. Validate that an .NET 10.0 SDK required for this upgrade is installed on the machine and if not, help to get it installed.
2. Ensure that the SDK version specified in global.json files is compatible with the .NET 10.0 upgrade.
3. Upgrade src/ServiceDefaults/ServiceDefaults.csproj
4. Upgrade src/Basket/Basket.csproj
5. Upgrade src/WebApp/WebApp.csproj
6. Upgrade src/Catalog/Catalog.csproj
7. Upgrade src/AppHost/AppHost.csproj

## Settings

### Excluded projects

| Project name | Description |
|:-----------------------------------------------|:---------------------------:|

### Aggregate NuGet packages modifications across all projects

| Package Name                                 | Current Version                        | New Version                                 | Description                         |
|:---------------------------------------------|:---------------------------------------:|:-------------------------------------------:|:------------------------------------|
| Aspire.Hosting.AppHost                       | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Aspire.Hosting.Keycloak                      | 9.1.0-preview.1.25121.10               | 9.5.1-preview.1.25502.11                   | Recommended for .NET 10.0           |
| Aspire.Hosting.PostgreSQL                    | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Aspire.Hosting.RabbitMQ                      | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Aspire.Hosting.Redis                         | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Aspire.Keycloak.Authentication               | 9.1.0-preview.1.25121.10               | 9.5.1-preview.1.25502.11                   | Recommended for .NET 10.0           |
| Aspire.Npgsql.EntityFrameworkCore.PostgreSQL | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Aspire.StackExchange.Redis.DistributedCaching| 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Aspire.StackExchange.Redis.OutputCaching     | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| Microsoft.EntityFrameworkCore.Tools          | 9.0.2                                  | 10.0.0-rc.1.25451.107                      | Recommended for .NET 10.0           |
| Microsoft.Extensions.Http.Resilience         | 9.2.0                                  | 9.9.0                                      | Recommended for .NET 10.0           |
| Microsoft.Extensions.ServiceDiscovery        | 9.1.0                                  | 9.5.1                                      | Out of support, upgrade required    |
| OpenTelemetry.Instrumentation.AspNetCore     | 1.11.0                                 | 1.12.0                                     | Recommended for .NET 10.0           |
| OpenTelemetry.Instrumentation.Http           | 1.11.0                                 | 1.12.0                                     | Recommended for .NET 10.0           |

### Project upgrade details

#### src/ServiceDefaults/ServiceDefaults.csproj modifications

Project properties changes:
  - Target framework should be changed from `net9.0` to `net10.0`

NuGet packages changes:
  - Microsoft.Extensions.Http.Resilience should be updated from `9.2.0` to `9.9.0` (recommended for .NET 10.0)
  - Microsoft.Extensions.ServiceDiscovery should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)
  - OpenTelemetry.Instrumentation.AspNetCore should be updated from `1.11.0` to `1.12.0` (recommended for .NET 10.0)
  - OpenTelemetry.Instrumentation.Http should be updated from `1.11.0` to `1.12.0` (recommended for .NET 10.0)

#### src/Basket/Basket.csproj modifications

Project properties changes:
  - Target framework should be changed from `net9.0` to `net10.0`

NuGet packages changes:
  - Aspire.Keycloak.Authentication should be updated from `9.1.0-preview.1.25121.10` to `9.5.1-preview.1.25502.11` (recommended for .NET 10.0)
  - Aspire.StackExchange.Redis.DistributedCaching should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)

#### src/WebApp/WebApp.csproj modifications

Project properties changes:
  - Target framework should be changed from `net9.0` to `net10.0`

NuGet packages changes:
  - Aspire.StackExchange.Redis.OutputCaching should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)

#### src/Catalog/Catalog.csproj modifications

Project properties changes:
  - Target framework should be changed from `net9.0` to `net10.0`

NuGet packages changes:
  - Aspire.Npgsql.EntityFrameworkCore.PostgreSQL should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)
  - Microsoft.EntityFrameworkCore.Tools should be updated from `9.0.2` to `10.0.0-rc.1.25451.107` (recommended for .NET 10.0)

#### src/AppHost/AppHost.csproj modifications

Project properties changes:
  - Target framework should be changed from `net9.0` to `net10.0`

NuGet packages changes:
  - Aspire.Hosting.AppHost should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)
  - Aspire.Hosting.Keycloak should be updated from `9.1.0-preview.1.25121.10` to `9.5.1-preview.1.25502.11` (recommended for .NET 10.0)
  - Aspire.Hosting.PostgreSQL should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)
  - Aspire.Hosting.RabbitMQ should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)
  - Aspire.Hosting.Redis should be updated from `9.1.0` to `9.5.1` (out of support, upgrade required)
