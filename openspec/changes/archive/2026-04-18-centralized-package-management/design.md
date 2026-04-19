## Context

All NuGet package versions for `AspireApp` projects today live inline on each `<PackageReference />`. The repository root has no `Directory.Packages.props` or `Directory.Build.props`. Microsoft documents CPM in [Central Package Management](https://learn.microsoft.com/en-us/nuget/consume-packages/central-package-management): enable `ManagePackageVersionsCentrally`, declare `<PackageVersion />` centrally, and omit `Version` on `<PackageReference />` in projects.

Current package IDs and versions to centralize (one `<PackageVersion />` per package ID):

| Package ID | Version (current) |
|------------|-------------------|
| Aspire.Hosting.Redis | 13.1.0 |
| Aspire.Hosting.Testing | 13.1.0 |
| Aspire.StackExchange.Redis.OutputCaching | 13.1.0 |
| coverlet.collector | 6.0.2 |
| Microsoft.AspNetCore.OpenApi | 10.0.1 |
| Microsoft.Extensions.Http.Resilience | 10.1.0 |
| Microsoft.Extensions.ServiceDiscovery | 10.1.0 |
| Microsoft.NET.Test.Sdk | 17.14.1 |
| OpenTelemetry.Exporter.OpenTelemetryProtocol | 1.14.0 |
| OpenTelemetry.Extensions.Hosting | 1.14.0 |
| OpenTelemetry.Instrumentation.AspNetCore | 1.14.0 |
| OpenTelemetry.Instrumentation.Http | 1.14.0 |
| OpenTelemetry.Instrumentation.Runtime | 1.14.0 |
| xunit.runner.visualstudio | 3.1.4 |
| xunit.v3 | 3.0.1 |

`AspireApp.AppHost.csproj` uses `<Project Sdk="Aspire.AppHost.Sdk/13.1.0">`; that is **not** a `PackageReference` and stays on the project element per SDK-style projects. CPM does not replace SDK version attributes.

## Goals / Non-Goals

**Goals:**

- Single source of truth for NuGet package versions used by `AspireApp` projects.
- Successful `dotnet restore` and `dotnet build` for the solution after migration.
- Align with official CPM rules (central file, no `Version` on `PackageReference` unless using `VersionOverride` deliberately later).

**Non-Goals:**

- Changing application code, Aspire topology, or effective dependency versions (purely structural NuGet authoring change unless a duplicate ID forces one row).
- Enabling `CentralPackageTransitivePinningEnabled` or `GlobalPackageReference` unless a follow-up explicitly needs them.
- Introducing `Directory.Build.props` for other MSBuild concerns.
- Pinning or upgrading packages beyond the versions already in use.

## Decisions

1. **File location:** Add `Directory.Packages.props` at the **repository root** (`c:\src\my\aspire-openspec\Directory.Packages.props`). MSBuild discovers it from any nested project under `AspireApp/` by walking parent directories ([CPM location rules](https://learn.microsoft.com/en-us/nuget/consume-packages/central-package-management)).

2. **Property:** Set `<ManagePackageVersionsCentrally>true</ManagePackageVersionsCentrally>` in that file’s `PropertyGroup`.

3. **Project edits:** Strip `Version="..."` from every `<PackageReference />` in the five projects listed in the proposal; keep `Include` and other metadata (e.g. `PrivateAssets` if any) unchanged.

4. **No per-project opt-out:** All listed projects participate in CPM; no `ManagePackageVersionsCentrally` false unless a tooling conflict appears (none anticipated).

5. **Optional `dotnet new packagesprops`:** Equivalent to manually creating the file; either is fine; content must match the inventory above.

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| NU1507 when multiple package sources exist without mapping | If restore warns, add [package source mapping](https://learn.microsoft.com/nuget/consume-packages/package-source-mapping) or consolidate feeds per docs. |
| Missing `PackageVersion` for a referenced package | Restore/build fails with clear NU1010-style errors; verify grep of all `PackageReference` after edit. |
| IDE older than documented CPM support | Use current VS / SDK; this repo already targets net10.0. |

## Migration Plan

1. Add `Directory.Packages.props` with all `PackageVersion` rows.
2. Update each `.csproj` to remove inline versions.
3. Run `dotnet restore` and `dotnet build` from repo root on the AspireApp solution or multi-project build (user may invoke `dotnet build AspireApp/AspireApp.AppHost/AspireApp.AppHost.csproj` or a solution file if present).

Rollback: revert the props file and restore `Version` attributes on `PackageReference` items.

## Open Questions

- None for baseline CPM; if the team later adds a root `.sln`, ensure CI runs restore from a path that still picks up `Directory.Packages.props`.
