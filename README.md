# My Agent

A personal AI agent built with LangGraph. One chat that combines:

- **RAG** over your own notes (`knowledge_base.md`) using Milvus + Text Embeddings Inference
- **Budget tools** — balance, transactions, budgets and assets via a budget API
- **Database tool** — `query_database` runs SQL against PostgreSQL; `SELECT` runs immediately,
  `INSERT`/`UPDATE`/`DELETE` wait for an explicit yes/no confirmation
- **Entry forms** — a prefilled, validated bottom sheet to record an expense, income, budget or asset

All human-in-the-loop steps (forms, confirmations) go through `interrupt_protocol.py`, a shared JSON
schema, so the web UI — or any other client — renders them without the agent knowing about HTML.

## Stack

Python · LangGraph · OpenRouter (OpenAI-compatible API) · Flask · Milvus · TEI · PostgreSQL · Docker Compose

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env              # then fill in your keys
pip install huggingface_hub
python download_model.py          # one-time: download the embedding model to ./models
docker compose up -d              # Milvus, etcd, MinIO, Attu, TEI
python embed_knowledge.py         # embed knowledge_base.md (re-run when it changes)
```

## Run

```bash
python agent_openrouter.py "what's my balance?"   # one-shot
python agent_openrouter.py                         # interactive terminal
python web_app.py                                  # web UI at http://localhost:5000
```

## Configuration

See [`.env.example`](.env.example). `OPENROUTER_API_KEY` is required; the `DB_*` and `BUDGET_*`
variables enable the database and budget tools.

## Layout

| File | Purpose |
|---|---|
| `agent_openrouter.py` | LangGraph agent, tools, interrupts |
| `interrupt_protocol.py` | Shared schema for forms and confirmations |
| `web_app.py`, `static/` | Flask server and chat UI |
| `embed_knowledge.py` | Chunk and embed the knowledge base into Milvus |
| `download_model.py` | Fetch the embedding model for TEI |
| `docker-compose.yml` | Milvus, TEI and the web service |

## Notes

The database tool is meant for local/dev databases. Never point it at production.
