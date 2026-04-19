## 1. App shell (CDN links)

- [ ] 1.1 In `AspireApp.Web/Components/App.razor`, remove the `@Assets["lib/bootstrap/dist/css/bootstrap.min.css"]` link. Add CDN `<link>` tags in `<head>` for Bootstrap 5.3.8 CSS, Bootstrap Icons, Devicons, and Google Fonts (preconnect + `css2` families), matching URLs and `integrity` / `crossorigin` from `UdemyBlazorBeginnerToPro/NowPlayingApp/wwwroot/index.html`.
- [ ] 1.2 In `App.razor` `<body>`, before the Blazor script, add the Bootstrap 5.3.8 `bootstrap.bundle.min.js` script tag from jsDelivr with the same integrity/crossorigin as the reference app.

## 2. Typography

- [ ] 2.1 Update `AspireApp.Web/wwwroot/app.css`: define font CSS variables (e.g. `--cf-body-font: 'Montserrat'`, `--cf-title-font: 'Bebas Neue'`) and apply Montserrat to `html, body` and Bebas Neue to `h1–h6` and `.h1–.h6`, following `NowPlayingApp/wwwroot/css/app.css`. Replace the existing Helvetica-based `html, body` rule without dropping unrelated rules (validation, error boundary, form-floating, etc.).

## 3. Remove local Bootstrap

- [ ] 3.1 Delete the directory `AspireApp.Web/wwwroot/lib/bootstrap` (all vendored CSS/JS). Remove empty `wwwroot/lib` if nothing else remains.

## 4. Validation

- [ ] 4.1 Run `dotnet build` on `AspireApp.Web` or `AspireApp.AppHost` and fix any static asset or analyzer issues.
- [ ] 4.2 Run `dotnet test` on `AspireApp.Tests` if present to ensure no regression.
