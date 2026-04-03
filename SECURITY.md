# Security

## Overview

ATS-CVC is a static single-file HTML application with no backend, no server, and no database. This document explains every security decision made in the tool, what each one protects against, and what the tool's security boundaries are.

## Architecture — What the Serverless Design Prevents

The decision to build ATS-CVC as a static HTML file hosted on GitHub Pages was made deliberately for security reasons, not convenience.

**There is no server.** GitHub Pages cannot execute server-side code. This is publicly verifiable — GitHub Pages is a static file host. There is no application layer that could intercept, log, or process your data.

**There is no database.** Without a server, there is nothing to write to. Your CV text, job description, and API key cannot be stored because there is nowhere to store them.

**There is no session.** When you close the tab, the JavaScript state is garbage collected. The `beforeunload` event explicitly nulls the CV text, JD text, and API key variables before the page closes. Nothing persists.

**There is no analytics.** No tracking scripts, no third-party analytics, no cookies. The only external resources loaded are Google Fonts (for typography) and PDF.js from Cloudflare CDN (for PDF parsing).

## Outbound Connections

This tool makes exactly one category of outbound connection: to the API endpoint of the AI provider you select.

| Provider | Endpoint |
|---|---|
| Mistral | `https://api.mistral.ai/v1/chat/completions` |
| Google Gemini | `https://generativelanguage.googleapis.com/v1beta/models/...` |
| OpenAI | `https://api.openai.com/v1/chat/completions` |
| Anthropic | `https://api.anthropic.com/v1/messages` |

These connections are enforced by a Content Security Policy meta tag that allows `connect-src` only to these four domains. The browser will block any connection attempt to any other domain.

**What is sent in each API call:**
- Your API key (in the request header, directly to the provider)
- The extracted text of your CV (truncated to 8,000 characters)
- The extracted text of the job description (truncated to 4,000 characters)
- The framework system prompt (see FRAMEWORK_PROMPT.md)

**What is not sent:**
- The PDF file itself — only the extracted text
- Any personally identifying metadata beyond what is in your CV text
- Any information about your browser, OS, or device
- Any information from previous sessions

## File Validation

All uploaded files are validated before processing:

**Magic number check:** The tool reads the first 4 bytes of every uploaded file and verifies they match the PDF file signature (`%PDF` = `0x25 0x50 0x44 0x46`). A file with a `.pdf` extension but different contents will be rejected. This prevents disguised file uploads.

**Extension check:** Files must match the accepted extensions for each upload slot (`.pdf` for CV, `.pdf` or `.txt` for job description).

**Size limits:** CV files are capped at 5MB. Job description files are capped at 2MB. Files above these limits are rejected before any processing occurs.

## Prompt Injection Protection

CV and job description text is sanitised before being embedded in the AI prompt. The following patterns are detected and replaced with `[removed]`:

- `ignore previous instructions` and variants
- `you are now` role-switching attempts
- `system:` prefix injection
- `[INST]` instruction injection tags
- `<|...|>` model control tokens
- `### instruction/system/prompt` header injection
- `jailbreak` and `DAN mode` references

This prevents a maliciously crafted CV or job description from hijacking the AI's behaviour.

## API Key Handling

- The API key is entered into a `type="password"` field — it is masked on screen
- The key exists only in the browser's JavaScript memory during the session
- The key is sent only in HTTP headers to the provider's API endpoint
- The key is never written to `localStorage`, `sessionStorage`, cookies, or any DOM attribute
- The key is explicitly set to an empty string in the `beforeunload` event handler
- The key is never logged, transmitted to any third party, or included in any analytics

**Verify this claim:** The source code is public. Search for `api-key` in the HTML file to see every place the key is read or written.

## Key Format Validation

Before any API call is made, the tool validates that the key matches the expected format for the selected provider:

| Provider | Expected format |
|---|---|
| Mistral | Any string over 20 characters |
| Gemini | Starts with `AIza`, over 20 characters |
| OpenAI | Starts with `sk-`, over 20 characters |
| Anthropic | Starts with `sk-ant-`, over 20 characters |

Keys that do not match are rejected before any network request is made.

## XSS Protection

All content rendered into the DOM from external sources (API responses, extracted PDF text, user input) is passed through an HTML escape function before insertion. The escape function replaces `&`, `<`, `>`, `"`, and `'` with their HTML entity equivalents.

The tool uses `innerHTML` only with pre-escaped content. No user input or API response is inserted raw into the DOM.

## Content Security Policy

A `Content-Security-Policy` meta tag restricts what the browser can load and connect to:

```
default-src 'self';
script-src 'self' 'unsafe-inline' https://cdnjs.cloudflare.com https://fonts.googleapis.com;
style-src 'self' 'unsafe-inline' https://fonts.googleapis.com https://fonts.gstatic.com;
font-src https://fonts.gstatic.com;
connect-src https://api.anthropic.com https://generativelanguage.googleapis.com https://api.openai.com https://api.mistral.ai;
img-src 'self' data:;
```

This means:
- Scripts can only be loaded from the page itself, Cloudflare CDN (PDF.js), and Google Fonts
- Connections can only be made to the four AI provider endpoints
- Images can only be inline data URIs or same-origin — no external image loading
- No frames, no object embeds, no plugins

## What This Tool Does Not Protect Against

**Your AI provider's data handling.** Once your CV text reaches the provider's API, it is subject to their privacy policy and terms of service. Review these before use:
- Mistral: mistral.ai/terms
- Google: ai.google.dev/gemini-api/terms
- OpenAI: openai.com/policies/privacy-policy
- Anthropic: anthropic.com/legal/privacy

**Shoulder surfing.** The API key is masked but the CV and JD text are visible in the upload zones and text area. Use the tool on a private screen.

**Compromised browser extensions.** A malicious browser extension with access to page content could read the CV text or API key from the DOM. Use the tool in a clean browser profile if this is a concern.

**GitHub Pages infrastructure.** The tool is served by GitHub Pages. If GitHub's infrastructure were compromised at the CDN level, a modified version of the file could be served. Verify the file hash against the repository if this is a concern.

## Reporting a Vulnerability

If you find a security issue in ATS-CVC, please raise a GitHub Issue marked `[SECURITY]` or contact the maintainer directly. Do not disclose publicly before the maintainer has had a reasonable opportunity to respond.

---

*ATS-CVC v3.0.0 — github.com/wblv-dev/ats-cvc*
