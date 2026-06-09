# Odysseus on Railway

Self-hosted AI workspace — a privacy-first alternative to the ChatGPT/Claude UI, with chat across multiple LLM providers, an agent system with tools, deep research, persistent memory, document editing, notes/tasks, calendar (CalDAV), and email. Bring your own model via any OpenAI-compatible API.

This is a Railway-ready packaging of [pewdiepie-archdaemon/odysseus](https://github.com/pewdiepie-archdaemon/odysseus).

## Architecture

The template deploys four services:

| Service     | Image / Source                    | Role                                   | Exposure |
| ----------- | --------------------------------- | -------------------------------------- | -------- |
| `odysseus`  | this repo (Dockerfile)            | FastAPI web UI + agent backend (:7000) | Public   |
| `chromadb`  | `chromadb/chroma:1.0.21`          | Vector store for persistent memory     | Private  |
| `searxng`   | `Shinyduo/odysseus-searxng`       | Metasearch (JSON) for deep research    | Private  |
| `ntfy`      | `binwiederhier/ntfy:v2.24.0`      | Push notifications                     | Private  |

The three supporting services are reached over Railway private networking; only `odysseus` gets a public domain.

## Required configuration

- **`OPENAI_API_KEY`** — any OpenAI-compatible key. Without a model backend the chat UI loads but can't generate.
- **`ODYSSEUS_ADMIN_PASSWORD`** — pre-seeds the first admin login. If left blank, an admin password is auto-generated and printed in the deploy logs on first boot.

## Notes

- A volume is mounted at `/app/data` (SQLite DB, sessions, auth, uploads, skills, caches).
- `SECURE_COOKIES=true` and `AUTH_ENABLED=true` are set — Railway serves over HTTPS.
- Memory works out of the box via the bundled `fastembed` local embedder; set `EMBEDDING_URL`/`EMBEDDING_MODEL`/`EMBEDDING_API_KEY` to use a remote embedding API instead.
