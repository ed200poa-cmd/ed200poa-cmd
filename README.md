# Edward Kim

Full-stack AI developer working primarily in Python and FastAPI, shipping production systems.

I design, build, review, and ship AI systems end to end — retrieval pipelines, voice agents,
and the APIs and data layers underneath them. That includes a production voice agent for a
customer service company that answers live inbound calls over Twilio: an 18-intent classifier,
a state machine that carries a caller from triage through to a confirmed service booking, and
automatic handoff to a human on an emergency intent or after two consecutive misunderstandings.
Built on FastAPI, async SQLAlchemy, and the Claude API.

A large share of that work is reviewing and correcting code and output I did not write by
hand. My approach is to make correctness observable before trusting it — which in practice
means writing the measurement harness alongside the system.

## Measurement, not impressions

I do not judge an AI system by how good its output looks in a few manual checks. I build an
evaluation harness against a frozen dataset, define the metrics that must not regress, and
treat a regression in those as a blocking failure.

[**docmind-ai**](https://github.com/ed200poa-cmd/docmind-ai) is the worked example. Its harness
runs a 30-case dataset through the application's real retrieval and answering code — imported,
not reimplemented — and reports retrieval recall@k, answer correctness via an LLM judge at
temperature 0, citation grounding, refusal accuracy, and latency. Current measured results:

| Metric | Value |
|---|---|
| recall@1 / @3 / @5 (overall) | 87.0% / 95.7% / 100.0% |
| answer correctness (n=23) | 91.3% correct, 8.7% partially correct, 0.0% incorrect |
| citation grounding | 100.0% (115 of 115 citations verified verbatim) |
| refusal accuracy | 100.0% (7 of 7), zero hallucinations |
| median latency | 1.13-1.15s across two runs |

Citation grounding and refusal accuracy are protected: a change that lowers either is reverted
regardless of what it improves elsewhere. Both have held at 100% across every run. The
optimisation work that produced these numbers took recall@1 from 73.9% and incorrect answers
from 4.3% to 0.0%, one change at a time, each measured against the same unmodified dataset.

Two things I consider more useful than the headline figures:

- I ran the suite twice back to back to check the judge was actually reproducible. All 30 case
  verdicts matched; latency did not, because it is wall-clock. Reproducible at the verdict
  level is a different claim from reproducible byte for byte, and the write-up says which one
  holds.
- The weakest-looking number, 62.5% multi_chunk recall@1, is a metric artifact rather than a
  retrieval defect: the dataset marks one source snippet per case, but those questions need two
  chunks to answer, so recall@1 scores a miss whenever the other equally-required chunk ranks
  first. The application retrieves top_k=5 and recall@5 is 100%. I documented that instead of
  reporting a number I knew was misleading, and instead of editing the dataset to make it look
  better.

Reasoning and per-change analysis: [`evals/RESULTS.md`](https://github.com/ed200poa-cmd/docmind-ai/blob/main/evals/RESULTS.md).

## Selected public repositories

| Repository | What it is | Stack |
|---|---|---|
| [docmind-ai](https://github.com/ed200poa-cmd/docmind-ai) | RAG document Q&A with cited sources and an evaluation harness — [live](https://docmind-ai-production-ae3a.up.railway.app) | FastAPI, FAISS, fastembed on local ONNX, SQLite, Claude API |
| [dentalvoice-ai](https://github.com/ed200poa-cmd/dentalvoice-ai) | AI phone receptionist for dental practices, with a web chat demo — [live](https://dentalvoice-ai-production.up.railway.app) | FastAPI, Twilio, ElevenLabs TTS, SQLite, Claude API |
| [datamind-platform](https://github.com/ed200poa-cmd/datamind-platform) | ML analytics for churn prediction, revenue forecasting, and segmentation | FastAPI, scikit-learn, Claude API, Docker |
| [omnichat-platform](https://github.com/ed200poa-cmd/omnichat-platform) | Multi-channel chatbot platform | FastAPI, WhatsApp, ManyChat, vLLM, Firebase, Claude API |
| [agenthub-demo](https://github.com/ed200poa-cmd/agenthub-demo) | CRM automation agents | LangChain, Supabase, GoHighLevel, Zapier, Claude API |
| [autoflow-ai](https://github.com/ed200poa-cmd/autoflow-ai) | Lead scoring and outreach automation — [live](https://autoflow-ai-production-6432.up.railway.app) | FastAPI, n8n, SQLite, Claude API |
| [subflow-saas](https://github.com/ed200poa-cmd/subflow-saas) | Subscription management SaaS | TypeScript, tRPC, Drizzle ORM, PostgreSQL, Stripe, React |
| [ai-chat-widget](https://github.com/ed200poa-cmd/ai-chat-widget) | WordPress chatbot plugin with streaming responses | PHP plugin, TypeScript widget, FastAPI, PostgreSQL with pgvector, Claude API over SSE |
| [okcheck-site](https://github.com/ed200poa-cmd/okcheck-site) | Website for a shipped iOS safety check-in app — [live](http://www.okcheck.app) | Static HTML, GitHub Pages |

Additional production work, including the voice agent above and a civic simulation platform
built on FastAPI, async SQLAlchemy, PostgreSQL, and Redis, is in private repositories.

## Contact

ed200.poa@gmail.com
