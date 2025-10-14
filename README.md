## Advanced Moz API Tool

A single-file, client-side web app to explore Moz API (v3) endpoints. It lets you authenticate with your own Moz API key, run requests through a configurable proxy, and view results as JSON or an interactive table with filtering and CSV export.

### Key Features
- **Direct Moz API access**: Supply your Moz API key and call multiple endpoints.
- **Configurable proxy**: Uses the `data-proxy` attribute on the `<body>`; swap it for your own endpoint.
- **Dynamic forms**: Input fields change based on the selected method.
- **JSON and Table views**: Toggle between raw JSON and a Tabulator-powered table.
- **Filtering & CSV export**: Filter on any column and export results to CSV.
- **Local persistence**: API key and theme stored in `localStorage`.
- **Themes**: Light, Dark, Retro, and High Contrast.
- **Helpful errors**: Hints for quota issues, invalid keys, and network timeouts.

### Quick Start
1. **Get a Moz API key**: Manage your key in the Moz dashboard (`https://moz.com/products/api/keys`).
2. **Serve the file locally**:
   - Python: `python3 -m http.server 5173` then open `http://localhost:5173/`.
   - Node (http-server): `npx http-server -p 5173` then open `http://localhost:5173/`.
   - Any static server works. Avoid `file://` to prevent browser fetch restrictions.
3. **Open the app**: Enter your API key, pick a method, fill inputs, and click "Run Request".

### Usage
- **API Key**: Stored locally in the browser. The UI shows an obfuscated preview and lets you edit or replace it. Quota is fetched on load if a key is present.
- **Method selection**: Choose from the dropdown; the form below updates with method-specific fields.
- **Run request**: Submits to the proxy (`POST` JSON). Responses display under "API Results".
- **Views**:
  - JSON: Raw response (copyable via "Copy JSON").
  - Table: Interactive Tabulator table with pagination, column dragging, and client-side filtering.
- **Export**: "Export to CSV" saves a UTF‑8 CSV of the normalized rows.
- **Filtering**: Click "Filter" in table view to add text filters per column. Active filters appear as removable chips.
- **Themes**: Buttons in the footer switch themes and persist your choice.

### Available Methods (from the UI)
- **Site Metrics**: Scope (domain/subdomain/url), list of URLs/domains.
- **Brand Authority**: List of domains/URLs.
- **Top Pages**: Scope, filter, sort, limit, domains/URLs.
- **Keyword Metrics**: Metric type (all/volume/difficulty/opportunity/priority), device, engine, locale, keywords.
- **Search Intent**: Locale, keywords.
- **Ranking Keywords**: Scope, locale, limit, domains/URLs.
- **Related Keywords**: Locale, keywords.
- **Keyword Count**: Scope (domain/subdomain), locale, domains/URLs.
- **Anchor Text**: Scope, limit, domains/URLs.
- **Linking Domains**: Scope, sort, filters, limit, domains/URLs.
- **Recently Gained Links**: Scope, optional begin/end dates, limit, domains/URLs.
- **Recently Lost Links**: Scope, optional begin/end dates, limit, domains/URLs.
- **Link Intersect**: Scope, sort, minimum matches, lists of "linking to" and optional "not linking to" targets.
- **List Links**: Scope, sort, filters, limit, domains/URLs.
- **Filter Links by Anchor Text**: Scope, anchor text, sort, filters, limit, domains/URLs.
- **Filter Links by Domain**: Target scope/URLs, Source scope/URLs, filters, limit.
- **Final Redirect**: Scope, domains/URLs.
- **Link Status**: Target scope/URL and Source scope/URL.

### How Results Are Shaped
- The app flattens and title-cases fields for consistency.
- For link-based endpoints, source/target site metrics are expanded to columns (e.g., `Source Page Authority`, `Target Domain Authority`).
- CSV export includes a header built from all encountered keys, ensuring uniform columns across rows.

### Configuration
- **Proxy endpoint**: Defined on `<body data-proxy="...">` in `index.html` and read at runtime. Replace with your own proxy if desired.
- **Timeouts**: Requests use `AbortController` to guard against hangs. Quota check has a 10s timeout.
- **Local storage keys**: `mozApiKey` (API key), `theme` (UI theme).

### Security & Privacy
- Your API key is stored only in your browser (`localStorage`).
- Requests are sent to the configured proxy and onward to Moz. Use a proxy you trust.
- Be mindful of Moz quota usage; each call consumes rows per your plan.

### Troubleshooting
- **Unauthorized / 401-403**: Recheck your API key in the Moz dashboard.
- **Quota/Limit errors**: You may be out of rows; verify usage in your Moz account.
- **Network/Timeout**: Try again; ensure your proxy is reachable and supports CORS.
- **No data to export**: Some endpoints return empty arrays for certain inputs; review parameters.

### Tech Notes
- **Stack**: Static HTML + vanilla JS, Tailwind via CDN, Tabulator via CDN, Inter font via Google Fonts.
- **No build step**: Everything runs directly in the browser.
- **Accessibility**: Uses semantic labels, `aria-live` for status, focus-friendly controls, and a High Contrast theme.

### Customization
- Update the built-in help modal by editing the `readmeContent` string in `index.html`.
- Add or adjust methods in the `methodSelector` and corresponding input+payload sections.
- Swap the proxy by editing the `data-proxy` attribute on `<body>`.

---

If you plan to host this publicly, configure your own proxy service and review CORS and rate-limiting policies to protect your Moz API usage.
