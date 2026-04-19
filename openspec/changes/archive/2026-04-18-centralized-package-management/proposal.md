## Why

Package versions are duplicated across five MSBuild projects (`AspireApp.AppHost`, `AspireApp.ApiService`, `AspireApp.Web`, `AspireApp.ServiceDefaults`, `AspireApp.Tests`), which makes coordinated upgrades error-prone and obscures the effective dependency graph. Adopting [NuGet Central Package Management (CPM)](https://learn.microsoft.com/en-us/nuget/consume-packages/central-package-management) centralizes versions in one file so the Aspire stack stays aligned and bumps stay intentional.

## What Changes

- Add `Directory.Packages.props` at the repo root (alongside `AspireApp/`) with `ManagePackageVersionsCentrally` enabled and `<PackageVersion />` entries for every centrally managed package currently referenced across projects.
- Remove `Version` attributes from `<PackageReference />` items in all affected `.csproj` files; versions resolve from `Directory.Packages.props` per Microsoft guidance.
- **Note:** `AspireApp.AppHost` uses `Aspire.AppHost.Sdk` with a `Version` on the `Project` element’s SDK attribute—that is SDK-style, not a `<PackageReference />`; leave SDK versioning as documented unless the team later standardizes it separately (CPM applies to `PackageReference`-based dependencies).

## Capabilities

### New Capabilities

- `nuget-central-package-management`: Requirements for how this repository declares and resolves NuGet package versions using CPM (`Directory.Packages.props`, project `PackageReference` shape, and restore behavior expectations).

### Modified Capabilities

<!-- No existing openspec/specs/ capabilities in this repository. -->

## Impact

- **Projects:** All projects under `AspireApp/` that declare `<PackageReference>` (ApiService, Web, ServiceDefaults, Tests, AppHost packages only—AppHost SDK line unchanged).
- **Tooling:** `dotnet restore` / `dotnet build` must succeed; IDE package UI continues to work with CPM.
- **Runtime / Aspire:** No application behavior change; dependency versions remain the same unless we intentionally consolidate to a single row per package ID (shared versions across projects).
