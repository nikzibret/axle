# Axle — AI Agent Visibility Auditor for Shopify

**Axle audits Shopify stores for AI agent discoverability and generates the exact files needed to fix the gaps it finds.**

As AI shopping agents (ChatGPT, Perplexity, Claude) replace traditional search as the primary way consumers discover products, most e-commerce stores are completely invisible to them — not because their products are bad, but because their sites were never configured for machine readability. Axle solves that.

> 🟢 **Status:** Working proof-of-concept. A conversational v2 with session memory and authenticated crawling is in progress.

---

## The Problem

When a shopper asks ChatGPT to "find me the best sustainable hoodie under $80," an AI agent does the searching. That agent doesn't click ads. It doesn't look at your Google ranking. It crawls your store's underlying configuration — and if that configuration isn't set up correctly, your store simply doesn't exist to it.

Three failure modes affect nearly every Shopify store:

- **robots.txt is blocking AI crawlers** — bots like OAI-SearchBot and PerplexityBot are turned away before reading a single product
- **No llms.txt file** — without a structured entry point, AI agents have no map to your catalog
- **Product feed not discoverable** — agents can't enumerate your SKUs even if they get in

The result: stores that look great to humans are unreadable to AI agents, and losing sales silently.

---

## What Axle Does

A merchant pastes their Shopify store URL into Axle's frontend. Within 60 seconds:

1. **Audits** the store across three AI-readiness dimensions: robots.txt configuration, llms.txt presence, and product feed accessibility
2. **Scores** the store on a 0–100 scale with a plain-language gap report explaining exactly what's broken and why it matters
3. **Generates** two corrective files — a fixed robots.txt and a custom llms.txt — mapped to that store's catalog and ready to deploy

No developer required. No agency. The merchant drops the files into their Shopify root directory and they're done.

---

## How It Works

```
User submits Shopify URL (Lovable frontend)
        ↓
Webhook triggers n8n workflow
        ↓
AI Agent fires three parallel fetch requests:
  → robots.txt
  → products.json (product feed)
  → sitemap.xml (llms.txt check)
        ↓
Gap Scoring engine grades each dimension:
  → robots.txt: PASS / PARTIAL / BLOCKED / MISSING
  → llms.txt:   PASS / MISSING
  → Product Feed: PASS / MISSING
        ↓
File generation nodes produce:
  → Corrected robots.txt (preserves existing rules, injects AI crawler block)
  → Custom llms.txt (mapped to store's catalog, collections, and APIs)
        ↓
Assembler packages score + files → returns to frontend
        ↓
Merchant downloads files, drops into Shopify root
```

### Key Design Decisions

**Retrieval bots vs. training bots are treated differently.** Axle explicitly distinguishes between AI retrieval bots (OAI-SearchBot, PerplexityBot, ChatGPT-User) that drive referral traffic, and training crawlers (GPTBot, ClaudeBot) that don't. The generated robots.txt allows retrieval bots by default and leaves training bot access as a commented merchant decision.

**Files are download-only, never auto-deployed.** The merchant reviews and deploys manually. Axle never touches a live store directly.

**Shopify-only by design.** The audit logic is built around Shopify's URL structure, product feed endpoints, and sitemap conventions. Shopify-first means the audit is deep and specific, not shallow and generic.

---

## Tech Stack

| Layer | Tool |
|-------|------|
| Workflow orchestration | n8n |
| LLM | GPT-4o-mini (OpenAI API) |
| Frontend | Lovable |
| File generation | JavaScript (n8n Code nodes) |
| Trigger | n8n Webhook |

---

## Scoring Model

Each dimension is scored independently, then averaged for the overall score:

| Dimension | PASS | PARTIAL | BLOCKED/MISSING |
|-----------|------|---------|-----------------|
| robots.txt | 100 | 50 | 0 |
| llms.txt | 100 | — | 0 |
| Product Feed | 100 | — | 0 |

A store scoring below 50 is rated **NOT VISIBLE**. Between 50–79: **NEEDS WORK**. 80+: **AGENT-READY**.

---

## Demo

> 📹 **[Watch the 60-second demo](#)** — Axle auditing a $24M+ Shopify brand live.

*Demo video placeholder — link to be added.*

---

## What's Next (v2 Roadmap)

- **Conversational agent** — ask Axle follow-up questions about your audit, request regenerated files, simulate score improvements
- **Session memory** — audit context persists across the conversation, no re-crawling required
- **Authenticated crawling** — Shopify Storefront API token support for password-protected stores
- **Store-specific llms.txt** — pull brand and catalog context from product data to replace generic descriptions

---

## Built By

[Nik Žibret](https://www.linkedin.com/in/nikzibret/) · [Kim Kraft](https://www.linkedin.com/in/kimkraft-nyu/) · [Nabiha Jiwani](https://www.linkedin.com/in/nabiha-jiwani/)

NYU Stern School of Business — Foundations of AI Agents, Spring 2026

---

*Axle is a proof-of-concept. It is not affiliated with Shopify.*
