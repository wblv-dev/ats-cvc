# Privacy

## The short version

This tool has no backend. It stores nothing. When you close the tab, everything is gone — your CV, your job description, your API key, your results. It is as if you never opened it.

The only data that leaves your browser is what you send to your chosen AI provider's API. I never see it. There is no server for it to pass through.

## What happens to your data

### On your device

Your CV is parsed locally in your browser using PDF.js. The text is extracted on your machine and held in JavaScript memory. It is never written to disk by this tool.

Your API key is held in a password field and in JavaScript memory. It is explicitly cleared when you close or refresh the tab.

Your results are rendered into the page in your browser. They are never sent anywhere except to your chosen AI provider to generate them.

### What is sent to your AI provider

When you run an analysis, the following is sent directly from your browser to your chosen provider's API endpoint:

1. Your API key — in the HTTP `Authorization` or `x-api-key` header
2. The extracted text of your CV — truncated to 8,000 characters maximum
3. The extracted text of the job description — truncated to 4,000 characters maximum
4. The ATS-CVC Framework system prompt — the instructions that shape the analysis

Nothing else is sent. Not your name from the confirm step (that is populated client-side from the CV text), not your browser version, not your IP address beyond what the API provider receives in any normal HTTP request.

### What I see

Nothing. There is no server, no logs, no analytics, no monitoring. The tool is a static HTML file. It has no mechanism to observe, record, or transmit anything.

### What your AI provider sees

Your provider receives the data described above. Their handling of that data is governed by their own privacy policies:

| Provider | Privacy policy |
|---|---|
| Mistral | mistral.ai/terms |
| Google Gemini | ai.google.dev/gemini-api/terms |
| OpenAI | openai.com/policies/privacy-policy |
| Anthropic | anthropic.com/legal/privacy |

Key things to check in each policy: whether your data is used for model training, how long it is retained, and what rights you have to request deletion.

**Note on Mistral's free tier:** The Mistral Experiment plan (free tier) may use your data for model improvement. If this is a concern, upgrade to the Mistral Scale plan (paid) which includes data isolation and zero-retention options.

## Cookies and tracking

This tool sets no cookies. It uses no tracking scripts. There are no analytics, no session identifiers, and no persistent state of any kind.

The only external resources loaded are Google Fonts (typography) and PDF.js from Cloudflare CDN (PDF parsing). These are loaded once when the page opens. Google's standard font loading privacy policy applies.

## What persists after you close the tab

Nothing from this tool. The browser's JavaScript garbage collector clears all in-memory state. The `beforeunload` event explicitly nulls the CV text, JD text, and API key before the page unloads.

Your browser may retain:
- The page in your browser history
- The file name of your uploaded CV in the browser's file input history (depending on your browser settings)
- Fonts in the browser's font cache

None of these contain your CV content or API key.

## Data minimisation

The tool is designed to handle as little data as possible:

- CV text is truncated to 8,000 characters before being sent to the API — enough for a complete CV, but not unlimited
- Job description text is truncated to 4,000 characters
- The PDF file itself is never transmitted — only the extracted text
- No metadata from the PDF (author, creation date, modification history) is extracted or transmitted

## Your rights

Because this tool stores no data about you, there is nothing to request, correct, or delete. If you have concerns about data held by your AI provider, contact them directly using the links above.

## Changes to this policy

If the tool's data handling changes in a way that affects privacy, this document will be updated and the change will be noted in the repository commit history with an explanation.

---

*ATS-CVC v3.0.0 — github.com/wblv-dev/ats-cvc*
*This tool is open source. The privacy claims in this document can be verified by reading the source code.*
