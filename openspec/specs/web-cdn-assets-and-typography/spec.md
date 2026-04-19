# web-cdn-assets-and-typography

AspireApp.Web loads Bootstrap, Bootstrap Icons, Devicons, and Google Fonts from CDNs; typography uses Montserrat (body) and Bebas Neue (headings). Vendored Bootstrap under `wwwroot/lib/bootstrap` is not used.

## Requirements

### Requirement: Bootstrap from CDN only

The `AspireApp.Web` application MUST NOT ship Bootstrap CSS or JavaScript under `wwwroot/lib/bootstrap`. The Blazor host document (`Components/App.razor`) MUST load Bootstrap global CSS and the Bootstrap bundle JavaScript from a public CDN using HTTPS, and MUST include `integrity` and `crossorigin="anonymous"` attributes matching the chosen CDN asset (aligned with the NowPlayingApp reference: Bootstrap 5.3.8 on jsDelivr).

#### Scenario: No vendored Bootstrap in wwwroot

- **WHEN** a reviewer lists files under `AspireApp.Web/wwwroot/lib/bootstrap`
- **THEN** that path does not exist or contains no Bootstrap distribution files

#### Scenario: Document loads Bootstrap from CDN

- **WHEN** the rendered HTML for the web app is inspected
- **THEN** it contains a `<link>` to Bootstrap CSS and a `<script>` to Bootstrap bundle JS both pointing at the CDN host, not at `lib/bootstrap`

### Requirement: Bootstrap Icons and Devicons available

The `AspireApp.Web` application head MUST include a stylesheet link for Bootstrap Icons and a stylesheet link for Devicons from the CDN URLs used in the NowPlayingApp reference (jsDelivr/npm or documented devicon CDN).

#### Scenario: Icon stylesheets present

- **WHEN** the rendered `<head>` is inspected
- **THEN** it includes link elements for Bootstrap Icons CSS and Devicon CSS whose `href` values use HTTPS CDN URLs

### Requirement: Google Fonts Montserrat and Bebas Neue

The `AspireApp.Web` application MUST load the Montserrat and Bebas Neue families from Google Fonts using `fonts.googleapis.com` and `fonts.gstatic.com` preconnect links and a combined `css2` stylesheet link consistent with the NowPlayingApp reference.

#### Scenario: Fonts available to CSS

- **WHEN** the rendered `<head>` is inspected
- **THEN** it includes the preconnect pair and a Google Fonts stylesheet that lists both `Bebas Neue` and `Montserrat`

### Requirement: Body and heading typography

Site-wide CSS (`wwwroot/app.css`) MUST set the default document/body font family to Montserrat and heading elements (`h1`–`h6` and Bootstrap heading classes `.h1`–`.h6`) to Bebas Neue, using the same approach as NowPlayingApp (CSS variables for font family names are acceptable).

#### Scenario: Body uses Montserrat

- **WHEN** default text is rendered in the main layout without per-element overrides
- **THEN** computed `font-family` for `body` resolves to a stack that begins with Montserrat

#### Scenario: Headings use Bebas Neue

- **WHEN** an `h1`–`h6` or `.h1`–`.h6` element is rendered
- **THEN** computed `font-family` for that element resolves to a stack that begins with Bebas Neue

### Requirement: Bootstrap Icons rendered once

When the Bootstrap Icons CDN stylesheet is enabled, layout or component CSS MUST NOT paint the same `.bi` / `.bi-*` icon classes with `background-image` (or other duplicate glyph techniques) that would stack on top of the font-based `::before` icon from Bootstrap Icons.

#### Scenario: Sidebar nav icons

- **WHEN** a user views the main navigation that uses `<span class="bi bi-*">` with Bootstrap Icons loaded from the CDN
- **THEN** each menu item shows exactly one icon glyph per icon class (no overlapping duplicate icon artwork)
