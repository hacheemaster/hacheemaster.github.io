---
title: "Turning Weeks of Legal-Medical Review into Minutes: An Event-Driven AI Summarization Platform"
date: 2026-07-05
categories: [Gen AI, MLOps]
tags: [LLM, Azure, Durable Functions, OCR, Document Intelligence, Summarization, HIPAA, Multi-Agent]
header:
  image: "/assets/images/cover2.jpg"
excerpt: "How we helped a professional document-services firm move into AI document summarization for a major insurance client — cutting manual review time by ~70%."
---

> This post describes the architecture and the business problem at a high level. No client data, credentials, network topology, or proprietary prompts are disclosed.

## The business problem

Professional document-services firms sit on a firehose of paper. When an attorney or an insurance carrier subpoenas a claimant's records, what comes back is often **thousands of pages** per matter — medical charts, depositions, discovery productions, and meet-and-confer correspondence — scanned, unsearchable, and in no particular order. A human reviewer then reads all of it to produce the one thing everyone actually needs: a faithful, well-organized **summary**.

A professional document-services (PDS) provider serving **USAA** wanted to move up the value chain from *delivering documents* to *delivering understanding* — AI-generated summaries their reviewers could trust and turn around in a fraction of the time. The bar was high: the output had to be accurate enough to build legal and claims decisions on, and the whole thing had to run inside a security posture that satisfied **HIPAA and SOC 2**, because it processes protected health information end to end.

The goal we set: **cut manual review time by ~70%** without ever letting an unreviewed machine summary reach a decision-maker.

## What we built

An **event-driven, multi-agent AI platform** that ingests a batch of documents, understands what each one is, and routes it to a summarizer specialized for that document type — with a human reviewer always in the loop before anything is finalized.

The pipeline is modeled as a **durable orchestration**: each document flows through OCR, classification, and type-specific summarization as independent, retryable steps, and batches of hundreds of documents fan out in parallel and rejoin when complete.

```
                        ┌───────────────────────────────────────────┐
   Batch of documents   │            ORCHESTRATION (durable)         │
   (SAS-signed URLs) ──▶ │   fan-out · retries · callbacks · status   │
                        └───────────────────────────────────────────┘
                                          │  (parallel, per document)
                 ┌────────────────────────┼────────────────────────┐
                 ▼                        ▼                          ▼
          ┌────────────┐          ┌──────────────┐          ┌────────────────┐
          │  1. OCR    │          │ 2. Classify  │          │ 3. Summarize   │
          │  scanned   │  text ──▶│  what is this│  route ─▶│  by doc type:  │
          │  PDF → text│          │  document?   │          │  · medical     │
          └────────────┘          └──────────────┘          │  · deposition  │
                                                            │  · discovery   │
                                                            │  · meet&confer │
                                                            └───────┬────────┘
                                                                    │
                                                          ┌─────────▼─────────┐
                                                          │  Human-in-the-loop │
                                                          │  review & sign-off │
                                                          └─────────┬─────────┘
                                                                    ▼
                                                      structured summary + status callback
```

**Stage by stage:**

- **Intake** — Documents arrive as a batch, each referenced by a short-lived signed URL rather than being pre-copied into our environment. That let the platform read from a caller's own storage without either side loosening its security boundary, and gave us a clean in-domain copy for the rest of the pipeline and for audit.
- **OCR** — Every scanned PDF is converted to text with a managed document-intelligence service, so downstream models work on clean, machine-readable content.
- **Classification** — A language model decides *what each document actually is*. Getting this right matters more than it sounds: a discharge summary, a deposition transcript, and a discovery cover letter each need to be read very differently.
- **Specialized summarization** — Each document is handed to a summarizer tuned for its type — **medical records, depositions, discovery, and meet-and-confer** — each with its own prompt contract and expected structured output. Type specialization consistently beat a single "summarize anything" prompt on both faithfulness and format.
- **Human-in-the-loop** — No summary is treated as final until a human reviewer approves it. The machine does the reading and drafting; the professional does the judging.
- **Callbacks & status** — Because a batch can take a while, the orchestration reports progress and completion back to the calling system rather than making anyone poll.

## Making it production-grade (not a demo)

The interesting engineering wasn't the prompts — it was everything that keeps an LLM system trustworthy at scale:

- **Guardrails and evaluation** — an evaluation harness scored summaries against reference expectations so we could change a prompt or a model and *measure* whether quality moved, instead of guessing.
- **Prompt and version management** — prompts are versioned artifacts, not strings buried in code, so changes are reviewable and reversible.
- **Model monitoring and feedback loops** — reviewer corrections feed back into how we evaluate and improve the summarizers over time.
- **Fail-loud behavior** — early on, a misconfiguration let the classifier quietly return "not medical" for medical records. We rebuilt those paths to fail loudly rather than return a confident wrong answer — in this domain, a silent wrong answer is far more dangerous than an error.

## Security & compliance posture

Because this is PHI, the security model was a first-class design input, not an afterthought:

- **Identity, not secrets** — services authenticate to AI and storage with managed identities and role-based access, so there are no long-lived API keys to leak or rotate.
- **Private by default** — data services are reached over private networking and centralized secret storage; nothing sensitive is exposed to the public internet.
- **No secrets in logs** — signed URLs and any sensitive values are never logged; we log stable identifiers instead.
- **Infrastructure as code** — the environment is reproducible and reviewable, and promotes cleanly from dev to higher environments.

## Results

- **~70% reduction** in manual review time for the targeted document types.
- **Batch throughput** — hundreds of documents summarized per batch with parallel fan-out, retries, and progress callbacks.
- **A repeatable path up-market** — the client moved from selling *documents* to selling *summarized understanding*, with a platform that new document types can be added to as new specialized summarizers rather than a rewrite.

## Takeaways

1. **Classification before summarization.** Knowing *what* a document is unlocks a much better summary than any general-purpose prompt.
2. **Durable orchestration beats a monolithic script.** Retries, parallel fan-out, and callbacks are exactly what a batch-of-thousands, hours-long workload needs.
3. **Human-in-the-loop is the product, not a limitation.** In legal and medical work, the value is a trustworthy draft that a professional signs off on — speed *with* accountability.
4. **Treat the LLM system like a production service.** Evals, versioned prompts, monitoring, and fail-loud behavior are what separate a compelling demo from something a business will actually stake decisions on.
