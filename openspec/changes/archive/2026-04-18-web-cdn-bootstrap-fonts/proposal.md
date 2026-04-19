## Why

`AspireApp.Web` ships a full Bootstrap 5.3 tree under `wwwroot/lib/bootstrap`, which duplicates what a CDN already provides, increases repo size, and drifts from a single version line. Loading Bootstrap, icon fonts, and Google Fonts from CDNs (following the same pattern as `UdemyBlazorBeginnerToPro/NowPlayingApp`) keeps the Blazor host lean while enabling Bootstrap Icons, Devicons, and Montserrat / Bebas Neue typography.

## What Changes

- Replace the local Bootstrap CSS link in `Components/App.razor` with jsDelivr Bootstrap 5.x CSS using **subresource integrity** and `crossorigin`, aligned with `UdemyBlazorBeginnerToPro/NowPlayingApp/wwwroot/index.html` for Bootstrap, Icons, Devicons, and Google Fonts.
- Add a **Bootstrap Icons** stylesheet link and **Devicon** stylesheet link in `App.razor` `<head>`.
- Add **Google Fonts** (`Bebas Neue`, `Montserrat`) via `fonts.googleapis.com` / `fonts.gstatic.com` preconnect and the combined CSS2 family link used in NowPlayingApp.
- Add **Bootstrap JavaScript** (`bootstrap.bundle.min.js`) from the CDN before the Blazor script in `App.razor` so interactive Bootstrap components remain available if used later.
- Update `wwwroot/app.css` so `html, body` use **Montserrat** and `h1`–`h6` plus `.h1`–`.h6` use **Bebas Neue** (same typography split as NowPlayingApp `wwwroot/css/app.css`).
- **Delete** the entire `wwwroot/lib/bootstrap` directory (all vendored Bootstrap CSS/JS).

## Capabilities

### New Capabilities

- `web-cdn-assets-and-typography`: Requirements for how `AspireApp.Web` loads Bootstrap, icon packs, Devicons, fonts from CDNs, removes local Bootstrap, and applies Montserrat / Bebas Neue.

### Modified Capabilities

<!-- None. Existing openspec/specs/ only contains nuget-central-package-management. -->

## Impact

- **AspireApp.Web** only: `Components/App.razor`, `wwwroot/app.css`, removal of `wwwroot/lib/bootstrap/**`.
- **AspireApp.ApiService**, AppHost, tests: unchanged.
- **Offline / air-gapped dev:** pages depend on CDN reachability (document as trade-off in design).
