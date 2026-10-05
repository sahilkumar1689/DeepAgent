# Deep Agents Chatbot

A Streamlit chatbot built on the [`deepagents`](https://github.com/langchain-ai/deepagents) library. It plans multi-step work, searches the web, loads project context from `AGENTS.md`, uses skills under `skills/`, and can delegate to research subagents.

## Features

- Custom Gemini model and system prompt
- Tavily web search
- Built-in planning (`write_todos`) and virtual files
- Context from `projects/AGENTS.md` (`memory=`)
- Skills from `skills/` (`/skills/`)
- Swappable backends: StateBackend, FilesystemBackend, StoreBackend
- Subagents, including structured research output
- Conversation memory via a LangGraph checkpointer

## Project layout

```
Basic_Deepagent/
├── streamlit_app.py      # Streamlit UI and agent wiring
├── requirements.txt      # Runtime dependencies
├── .env                  # Local API keys (do not commit)
├── projects/
│   └── AGENTS.md         # Durable agent context
├── skills/               # Agent skills (aws, langgraph, python, report-writer)
└── README.md
```

Paths are resolved from the folder that contains `streamlit_app.py`, so the same layout works locally and on Streamlit Community Cloud.

## Prerequisites

- Python 3.11 or newer (3.12 is a good default)
- A [Google AI Studio](https://aistudio.google.com/apikey) API key (`GOOGLE_API_KEY`)
- Optional: a [Tavily](https://tavily.com/) API key (`TAVILY_API_KEY`) for web search

## Local setup

### 1. Clone and enter the project

```powershell
cd f:\ProjectsFolder\Basic_Deepagent
```

### 2. Create a virtual environment

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
```

On macOS/Linux:

```bash
python3 -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 3. Add API keys

Create a `.env` file next to `streamlit_app.py`:

```env
GOOGLE_API_KEY=your-google-api-key
TAVILY_API_KEY=your-tavily-api-key
```

`TAVILY_API_KEY` is optional. Without it, the app still runs; web search is disabled.

### 4. Run the app

```powershell
streamlit run streamlit_app.py
```

Open the URL Streamlit prints (usually `http://localhost:8501`).

Use the sidebar to choose backend, toggle `AGENTS.md` / skills / subagents, and start a new thread or reset state.

## Deploy on Streamlit Community Cloud

1. Push this repo to GitHub. Do **not** commit `.env` (it is listed in `.gitignore`).
2. Go to [share.streamlit.io](https://share.streamlit.io) and sign in with GitHub.
3. Click **Create app** and select this repository.
4. Set:
   - **Main file path:** `streamlit_app.py`
   - **Python version:** 3.11 or 3.12
5. Open **App settings → Secrets** and add:

```toml
GOOGLE_API_KEY = "your-google-api-key"
TAVILY_API_KEY = "your-tavily-api-key"
```

6. Deploy. Cloud installs packages from `requirements.txt` and reads keys from secrets (not from `.env`).

If the filesystem backend cannot write inside the app directory, the app copies `projects/` and `skills/` into a temp workspace and continues.

## Secrets checklist

| Key | Required | Local | Streamlit Cloud |
|-----|----------|--------|-----------------|
| `GOOGLE_API_KEY` | Yes | `.env` | App secrets |
| `TAVILY_API_KEY` | No (search only) | `.env` | App secrets |

Never put API keys in `requirements.txt`, `README.md`, or committed source files.
