## 1. Central package manifest

- [x] 1.1 Add `Directory.Packages.props` at the repository root with `<ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>` and `<PackageVersion />` items for every package ID listed in `design.md` (Aspire 13.1.0 stack, OpenTelemetry 1.14.0, ASP.NET OpenApi 10.0.1, resilience/service discovery 10.1.0, xUnit v3 / test SDK / coverlet as today).

## 2. Adopt CPM in project files

- [x] 2.1 Remove `Version` attributes from all `<PackageReference />` elements in `AspireApp/AspireApp.ServiceDefaults/AspireApp.ServiceDefaults.csproj`.
- [x] 2.2 Remove `Version` attributes from all `<PackageReference />` elements in `AspireApp/AspireApp.ApiService/AspireApp.ApiService.csproj`.
- [x] 2.3 Remove `Version` attributes from all `<PackageReference />` elements in `AspireApp/AspireApp.Web/AspireApp.Web.csproj`.
- [x] 2.4 Remove `Version` attributes from all `<PackageReference />` elements in `AspireApp/AspireApp.AppHost/AspireApp.AppHost.csproj` (keep `Sdk="Aspire.AppHost.Sdk/13.1.0"` on the `Project` element unchanged).
- [x] 2.5 Remove `Version` attributes from all `<PackageReference />` elements in `AspireApp/AspireApp.Tests/AspireApp.Tests.csproj`.

## 3. Validation

- [x] 3.1 Run `dotnet restore` and `dotnet build` from the repository root targeting `AspireApp/AspireApp.AppHost/AspireApp.AppHost.csproj` (or the solution if one exists) and confirm success.
- [x] 3.2 Run `dotnet test` on `AspireApp/AspireApp.Tests/AspireApp.Tests.csproj` to confirm no regression from the dependency graph change.
