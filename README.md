# ATS-CVC — CV Gap Analysis Tool

**CV gap analysis. Not a CV writer.**

**[View the project page](https://wellbelove.org/ATS-CVC/)** | **[Use the tool](https://wellbelove.org/ATS-CVC/)**

ATS-CVC analyses the gap between how you present yourself and what a hiring manager is actually looking for. It applies the [ATS-CVC Framework](METHODOLOGY.md) to your CV and a specific job description, then tells you what is missing, what is undersold, and what a hiring manager will probe on in interview.

It does not write your CV. That is your job.

---

## What it produces

Five structured outputs, every time, for any role in any industry:

| Output | What it answers |
|---|---|
| **Role Fit Assessment** | Can you genuinely do this job? Honest percentage fit against the essential requirements. |
| **Seniority Calibration** | Does your CV read at the right level? Task-focused vs outcome-focused, scope of impact, language of ownership. |
| **Keyword & Positioning Gap Analysis** | What is missing, and what are you calling something different to the JD? |
| **Expand These Points** | What is present but doing insufficient work for this specific role? |
| **Interview Risk Flags** | What will a hiring manager probe on, and are you ready? |

---

## How it works

1. **Choose your AI provider.** Mistral is free — phone number only, no card. OpenAI, Gemini, and Anthropic require a minimum spend paid directly to that company. See [provider costs](#ai-providers--costs) below.
2. **Get your API key** from your chosen provider and paste it in. Your key goes directly to them — not to this tool.
3. **Upload your CV** as a PDF and paste the job description.
4. **Confirm your name and role title** — extracted automatically, editable.
5. **Run the analysis.** Results render in seconds.
6. **Save as PDF** if you want a clean report to work from.

Close the tab — everything is gone. Nothing was ever stored.

---

## Your data and privacy

**This tool has no backend, no server, and no database.**

- Your CV, job description, and API key travel directly from your browser to your chosen AI provider
- Nothing passes through any intermediate server
- Nothing is stored, logged, or retained after you close the tab
- I make no money from this tool and see no data from it. Ever.

[Full privacy policy](PRIVACY.md) | [Security documentation](SECURITY.md) | [Verify in the source code](index.html)

---

## AI providers & costs

| Provider | Cost to start | Notes |
|---|---|---|
| 🟣 **Mistral** | **Free** — phone number only, no card | Experiment plan. Rate limited but sufficient for CV analysis. [Get key →](https://console.mistral.ai/api-keys) |
| 🟢 **Google Gemini** | ~£8 ($10) minimum prepay | Pay-as-you-go via Google AI Studio. **Separate from all Google/Workspace subscriptions.** Free tier not available in UK. [Get key →](https://aistudio.google.com/app/apikey) |
| ⚫ **OpenAI** | ~£4 ($5) minimum prepay | Pay-as-you-go. **Separate from ChatGPT Plus/Team subscriptions.** Free trial credits discontinued mid-2025. [Get key →](https://platform.openai.com/api-keys) |
| 🔵 **Anthropic** | ~£4 ($5) minimum prepay | Pay-as-you-go. **Separate from Claude.ai/Claude Max subscriptions.** Credits required before first use. [Get key →](https://console.anthropic.com/settings/keys) |

**Important:** Your existing AI subscription (ChatGPT Plus, Claude Max, Gemini AI Pro) does not include API access. The API is a separate product billed separately by each company. Money goes directly to them — not to this tool.

Each analysis costs fractions of a penny in API usage. Your primary cost is the minimum deposit to activate API access.

---

## The methodology

ATS-CVC is not a prompt. It is a documented, evidence-based framework applied consistently by an AI engine.

The [ATS-CVC Framework v1.0](METHODOLOGY.md) documents:
- The market data that underpins every structural decision
- The four guardrails that govern the analysis
- The eight-stage manual tailoring process
- The five analytical outputs and why each one exists
- The CV format specification
- The full evidence base with primary sources

The framework is the product. The AI is the engine that applies it.

---

## Using the framework manually

If you want to run the full framework interactively — to actually rewrite your CV against a JD — the METHODOLOGY.md documents the eight-stage process you can run manually with any capable AI assistant.

Open a new private/incognito session. Upload your CV and the job description. Paste the framework document as context. Work through the stages in sequence.

The framework works with any capable AI: Claude, ChatGPT, Gemini. See METHODOLOGY.md for guidance on which to use and why.

---

## Repository structure

```
/
├── index.html          — The tool (single file, no build step)
├── README.md           — This document
├── METHODOLOGY.md      — ATS-CVC Framework v1.0, full evidence base
├── FRAMEWORK_PROMPT.md — Machine-readable framework for runtime injection
├── SECURITY.md         — Security architecture and decisions
├── PRIVACY.md          — Data handling, plain English
└── BUILD.md            — Technical decisions and build guide
```

---

## Licence

MIT — free to use, adapt, and distribute with attribution.

---

## Contributing

Issues and pull requests welcome. Read [BUILD.md](BUILD.md) before contributing code. Read [METHODOLOGY.md](METHODOLOGY.md) before contributing analytical changes — the framework is the source of truth for output decisions.

---

*Built by [Harry Wellbelove](https://linkedin.com/in/harrywellbelove) · [github.com/wblv-dev](https://github.com/wblv-dev)*
