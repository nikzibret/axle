Axle is a multi-agent workflow engine with five functional layers that execute in sequence every time a merchant submits a store URL.

Layer 1 — Trigger
The merchant submits a Shopify URL through the Lovable-built frontend. This fires a webhook that kicks off the n8n workflow. The URL is the only required input — no API keys, no credentials, no setup.

Layer 2 — Crawl
The AI agent fires three HTTP requests in parallel: one to the store's robots.txt, one to products.json (Shopify's native product feed endpoint), and one to sitemap.xml to check for an llms.txt file. Each request returns either structured content or a failure signal. The agent is instructed to continue regardless of outcome — a missing resource is recorded as a gap, not a crash.

Layer 3 — Score
A gap scoring engine evaluates the results from each fetch against a defined rubric. robots.txt is graded PASS, PARTIAL, BLOCKED, or MISSING depending on which AI crawler rules are present. llms.txt and the product feed are binary: present or missing. Each dimension gets a numeric score; the three scores are averaged into the store's overall readiness rating on a 0–100 scale.

Layer 4 — Generate
Two separate file generation nodes run in sequence. The first produces a corrected robots.txt: if the file exists, it parses the original line-by-line, strips any conflicting AI crawler rules, and injects a clean AI retrieval bot block while preserving every other existing rule. If no file exists, it generates one from scratch with standard Shopify conventions. The second node produces a custom llms.txt pointing AI agents to the store's product feed, collections, sitemap, and Shopify JSON APIs.

Layer 5 — Deliver
An assembler node packages the score, grade, gap report, and both generated files into a single JSON response. The frontend receives it and renders the audit result — score card, gap summary cards, and downloadable files — in real time.

The entire flow executes in under 60 seconds for a live public Shopify store.

What the LLM does (and doesn't do)
The LLM orchestrates tool calls, applies scoring rules, and ensures the output is structured correctly. It does not write the files — that logic lives in deterministic JavaScript code nodes. This separation keeps file output consistent and auditable regardless of how the model phrases its intermediate reasoning.
