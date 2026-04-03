# Build Guide

## Why this document exists

Every technical decision in ATS-CVC was made deliberately. This document explains what was built, why each decision was made, and what it protects against or enables. It is the record of intent behind the code — so that contributors, auditors, and curious users can understand not just what the tool does but why it does it that way.

## What was built

ATS-CVC is a single HTML file. No build step, no dependencies to install, no framework, no bundler. Open the file in a browser and it works.

The tool:
1. Accepts a CV (PDF) and a job description (PDF or text)
2. Extracts the text locally using PDF.js
3. Sends the extracted text to an AI provider API
4. Renders the structured analysis as a collapsible five-section report
5. Allows the report to be saved as a PDF via the browser's print function

Everything runs in the browser. There is no server.

## Why a single HTML file

**Trust.** A single file is auditable. Anyone who wants to verify what the tool does can open it in a text editor and read every line. There are no minified bundles, no obfuscated dependencies, no external scripts that could change without warning.

**Simplicity.** A single file can be downloaded, emailed, shared, and run locally without any setup. No `npm install`, no Python environment, no Docker. Open it. It works.

**Security.** A static file hosted on GitHub Pages cannot run server-side code. This is not a policy — it is a technical constraint of the hosting environment. The absence of a server is not something that could accidentally be misconfigured. It is structurally impossible.

**Portability.** The file works identically whether served from GitHub Pages, opened from a local filesystem, or hosted anywhere else. No configuration, no environment variables, no deployment pipeline.

## Why the bring-your-own-key model

The tool requires users to provide their own API key rather than embedding one. This was a deliberate decision made for two reasons:

**Security.** An embedded API key in a public repository would be scraped and abused within hours. Even with rate limits, the key is the tool maintainer's key — any abuse is the maintainer's liability. The BYOK model eliminates this entirely.

**Transparency.** When a user provides their own key, the flow of money and data is unambiguous. Their key goes to their chosen provider. They are charged by that provider at that provider's rates. The tool maintainer is not in the payment chain, not in the data chain, and has no visibility into what is analysed.

## Why four AI providers

**Accessibility.** Different users have different API access. A single-provider tool excludes everyone who doesn't have that provider's API credentials. Four providers — including one with a genuine free tier (Mistral) — means the tool works for the broadest possible audience.

**Avoiding vendor lock-in.** The tool's value is the ATS-CVC Framework, not the AI engine. Any capable model can apply the framework. Supporting multiple providers demonstrates this and makes the tool more resilient to API changes, pricing changes, or access restrictions.

**Honesty.** The per-provider cost information and the debunking of the "just use your existing subscription" misconception are only possible because we support multiple providers and understand how each one works.

## Architecture decisions

### PDF parsing (PDF.js)

PDF.js is Mozilla's open-source PDF rendering library, loaded from Cloudflare CDN. It runs entirely in the browser — no PDF bytes are sent to any server.

The choice of PDF.js over a server-side parser was made for privacy reasons. The alternative — sending the PDF to a server for parsing — would require a backend, which conflicts with the no-server architecture.

Limitation: PDF.js text extraction is imperfect for heavily formatted PDFs, particularly those with two-column layouts or complex tables. The extracted text may be out of order. This is a known limitation and is addressed in the tool by truncating to a sensible length and by the AI model's robustness to imperfectly structured input.

### Text truncation

CV text is truncated to 8,000 characters and JD text to 4,000 characters before being sent to the API. These limits were chosen to:
- Keep API costs low (fewer tokens = lower cost)
- Stay well within the context limits of all four supported models
- Prevent accidental transmission of very large files that were uploaded in error

A standard two-page CV is approximately 3,000–5,000 characters of extracted text. 8,000 characters comfortably accommodates all but the longest CVs.

### Prompt injection sanitisation

Extracted text is passed through a sanitisation function before being embedded in the AI prompt. This prevents a maliciously crafted CV or job description from injecting instructions that would alter the AI's behaviour.

The sanitisation is a best-effort defence — it catches common patterns but is not a complete solution to prompt injection. The main protection is the structured JSON output format: the AI is instructed to return only JSON, which limits the surface area for injection to cause harm.

### The confirm step

After extracting text from the uploaded files, the tool makes a small API call to extract the candidate's name and the role title. These are then shown in editable fields before the main analysis runs.

This step exists because:
- The PDF text extraction may scramble names or titles (particularly from formatted headers)
- The candidate should be able to correct their name before it appears in the report header
- It provides a clear point of confirmation before the main (more expensive) API call is made

### Collapsible sections with print expansion

The five analysis sections are collapsible in the UI — this keeps the page manageable for long outputs. The Save as PDF button expands all sections before triggering `window.print()`, so the saved PDF contains the complete report.

The print CSS switches the colour scheme from dark to light, hides the UI chrome (API key input, upload buttons, provider cards), and adds a report header with the tool name and repository URL.

This approach — using the browser's print function rather than generating a PDF — was chosen because:
- It requires no server
- It requires no PDF generation library
- The output quality matches the browser's rendering of the HTML
- Users can choose their own paper size and margins

### Session cleanup

The `beforeunload` event handler explicitly clears sensitive state:
```javascript
window.addEventListener('beforeunload', () => {
  S.cvText = '';
  S.jdText = '';
  S.candName = '';
  document.getElementById('api-key').value = '';
});
```

This is belt-and-braces — the browser would garbage-collect the JavaScript state anyway. The explicit clear is an additional signal of intent and ensures the API key field is visually empty if the page is restored from cache.

## The framework injection (future)

The tool currently bakes the AI system prompt into the HTML at build time. The intention is to fetch `FRAMEWORK_PROMPT.md` from the GitHub repository at runtime so that framework updates automatically propagate to all users without requiring a new HTML release.

The implementation will:
1. Fetch the raw content of `FRAMEWORK_PROMPT.md` from `raw.githubusercontent.com` when the page loads
2. Cache the result in `sessionStorage` (keyed with the commit hash) to avoid repeated fetches within a session
3. Fall back to the baked-in prompt if the fetch fails (offline use, GitHub unavailability)
4. Show the framework version and a link to the METHODOLOGY.md in the UI

This is not yet implemented in v3.0.0.

## Hosting on GitHub Pages

To host ATS-CVC on GitHub Pages:

1. Fork or clone the repository
2. Go to Settings → Pages in your repository
3. Set Source to "Deploy from a branch"
4. Select `main` and `/ (root)`
5. Save — GitHub Pages will build and deploy automatically

The tool will be available at `https://[username].github.io/ats-cvc/` within a few minutes.

GitHub Pages enforces HTTPS. There is no configuration required — the tool works on any HTTPS origin.

## Running locally

Open `index.html` in any modern browser. There is no build step, no server requirement, and no dependencies to install.

For development, any local file server works:
```bash
python3 -m http.server 8080
# or
npx serve .
```

Running from `file://` works for most functionality but some browsers restrict API calls from `file://` origins due to CORS policy. A local server avoids this.

## Contributing

The tool is open source under the MIT licence. Contributions are welcome.

Before contributing, read METHODOLOGY.md — the framework document is the source of truth for analytical decisions. Code changes that alter the analysis outputs should be justified against the framework.

Raise issues for: bugs, security concerns, inaccurate framework claims, evidence challenges, provider API changes.

Pull requests welcome for: bug fixes, provider additions, UI improvements, documentation.

---

*ATS-CVC v3.0.0 — github.com/wblv-dev/ats-cvc*
