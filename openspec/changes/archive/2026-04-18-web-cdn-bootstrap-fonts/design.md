## Context

`AspireApp.Web` is a Blazor **Web App** with interactive server components. The root document is `Components/App.razor` (not `wwwroot/index.html`). Today it loads Bootstrap from static web assets:

```8:8:c:\src\my\aspire-openspec\AspireApp\AspireApp.Web\Components\App.razor
    <link rel="stylesheet" href="@Assets["lib/bootstrap/dist/css/bootstrap.min.css"]" />
```

Vendored files live under `wwwroot/lib/bootstrap/dist/`. The reference app **NowPlayingApp** is a WebAssembly template but its `wwwroot/index.html` shows the desired **CDN stack** and order:

- Bootstrap 5.3.8 CSS + bundle JS (jsDelivr, integrity + `crossorigin="anonymous"`).
- Bootstrap Icons 1.13.1 (`bootstrap-icons.min.css`).
- Devicons (`devicon.min.css` from jsDelivr/gh).
- Google Fonts preconnect + combined `Bebas+Neue` & `Montserrat` stylesheet.
- Custom `app.css` uses CSS variables and sets body to Montserrat, headings to Bebas Neue.

Replicate the **same URLs and integrity attributes** as NowPlayingApp where applicable so behavior matches the course reference. If a future Bootstrap patch requires bumping versions, update all three of: CSS link, JS link, and integrity hashes together.

## Goals / Non-Goals

**Goals:**

- No Bootstrap files under `wwwroot/lib/bootstrap` after the change.
- `App.razor` loads CDN CSS/JS and font/icon sheets; `app.css` defines typography (Montserrat body, Bebas Neue headings).
- Preserve existing `app.css` rules unrelated to the default `font-family` on `html, body` (validation, error boundary, etc.).

**Non-Goals:**

- Adding `themes.css` or `theme.js` from NowPlayingApp (not requested).
- Changing NavMenu markup to use icons (icons are available globally for future use).
- Self-hosting fonts or pinning npm packages for Bootstrap.

## Decisions

1. **Where to declare CDNs:** `App.razor` `<head>` for styles; `<body>` end for scripts (Bootstrap bundle **before** `_framework/blazor.web.js`), mirroring NowPlayingApp ordering relative to the Blazor bootstrap script.

2. **Absolute URLs:** CDN `<link>` and `<script src>` use full `https://` URLs. They are **not** passed through `@Assets[...]` (static asset pipeline is for local files only).

3. **Bootstrap version:** Use **5.3.8** with the same SRI hashes as NowPlayingApp `index.html` for CSS and JS bundle.

4. **Typography implementation:** Prefer `:root` CSS custom properties `--cf-body-font` / `--cf-title-font` (or equivalent names) and reference them on `html, body` and `h1–h6, .h1–.h6` like NowPlayingApp `wwwroot/css/app.css` for consistency and easy overrides.

5. **Deletion scope:** Remove `wwwroot/lib/bootstrap/` entirely (entire tree). Do not leave empty `lib` folders unless other libraries exist there; if `wwwroot/lib` is empty after deletion, remove the `lib` directory.

## Risks / Trade-offs

| Risk | Mitigation |
|------|------------|
| CDN or Google Fonts blocked (corporate proxy) | Document in proposal impact; optional follow-up for local fallback. |
| SRI mismatch when bumping CDN URLs | Change design/tasks to always update CSS + JS integrity together. |
| CSP in production | If a later change adds strict CSP, allowlist jsDelivr, fonts.gstatic.com, fonts.googleapis.com. |

## Migration Plan

1. Edit `App.razor` to swap links/scripts.
2. Edit `app.css` for fonts.
3. Delete `wwwroot/lib/bootstrap`.
4. `dotnet build` / `dotnet test` on existing test project (integration test hits web if applicable).

## Open Questions

- None for initial implementation.
