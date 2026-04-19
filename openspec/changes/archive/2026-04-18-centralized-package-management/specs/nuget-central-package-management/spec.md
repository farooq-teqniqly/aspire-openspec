## ADDED Requirements

### Requirement: Central package versions file

The repository MUST define a single `Directory.Packages.props` at the repository root with `ManagePackageVersionsCentrally` set to `true` and MUST declare a `<PackageVersion />` entry for every NuGet package ID that any `AspireApp` project references via `<PackageReference />`, using the versions that project files used prior to this change (unless a deliberate upgrade is recorded in the change tasks).

#### Scenario: Restore resolves versions from central file

- **WHEN** a developer runs `dotnet restore` on any `AspireApp` project that participates in central management
- **THEN** NuGet resolves each `<PackageReference />` without an inline `Version` to the version declared in `Directory.Packages.props` for that package ID

### Requirement: Projects use versionless PackageReference

Every `AspireApp` project that declares `<PackageReference />` for centrally managed packages MUST omit the `Version` attribute on those items; versions MUST come only from `Directory.Packages.props` (except where MSBuild explicitly allows `VersionOverride` for a documented exception, which this change does not introduce).

#### Scenario: No duplicate version literals in csproj

- **WHEN** an engineer inspects `AspireApp/**/*.csproj` after the change
- **THEN** no `<PackageReference>` for a centrally managed package includes a `Version` attribute

### Requirement: SDK host project unchanged for SDK attribute

The AppHost project MAY keep `Aspire.AppHost.Sdk` with a version on the `Project` element’s `Sdk` attribute; that declaration MUST NOT be mistaken for a `PackageReference` and MUST NOT be moved into `PackageVersion` items.

#### Scenario: AppHost SDK line remains valid

- **WHEN** the solution is built after adopting CPM
- **THEN** `AspireApp.AppHost` continues to use `Aspire.AppHost.Sdk` with its SDK-style version and builds successfully

### Requirement: Build succeeds

After adopting CPM, `dotnet restore` followed by `dotnet build` for the Aspire application projects MUST succeed without new package-resolution errors attributable to missing central versions.

#### Scenario: CI-local build validation

- **WHEN** `dotnet build` is executed from the repository root against the AppHost or all Aspire projects
- **THEN** the build completes successfully with the same effective package versions as before the migration (no unintended version drift)
