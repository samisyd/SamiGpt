# SamiGPT

SamiGPT is a web-based AI chat assistant built with FastAPI, LangChain, and LangGraph. It uses Google Gemini for chat, can search the web with Tavily, and supports document question answering with retrieval-augmented generation (RAG).

## Features

- Chat with selectable Gemini models
- Stream assistant responses in the browser
- Keep conversation history and agent checkpoints in SQLite
- Upload PDF, DOCX, TXT, Markdown, Python, and CSV files and ask questions about them
- Search the web with Tavily
- Use calculator and per-conversation long-term memory tools

## Requirements

- Python 3.13 or newer
- [uv](https://docs.astral.sh/uv/) (recommended), or pip
- A Google AI API key
- A Tavily API key for web search

## Setup

Clone the repository and open its directory:

```powershell
git clone https://github.com/samisyd/SamiGpt.git
cd SamiGpt
```

Install the project dependencies with uv:

```powershell
uv sync
```

Alternatively, create a virtual environment and install with pip:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

Create a `.env` file in the project root and add your API keys:

```dotenv
GOOGLE_API_KEY=your_google_api_key
TAVILY_API_KEY=your_tavily_api_key
```

The Gemini model defaults to `gemini-3.6-flash`. To choose another supported model, optionally set `GEMINI_MODEL` in `.env`.

## Run

Start the web application from the project root:

```powershell
uv run uvicorn app:app --reload --host 127.0.0.1 --port 8080
```

Then open <http://127.0.0.1:8080> in your browser. The API also provides `/conversations`, `/history/{thread_id}`, `/upload`, and `/chat/stream`.

## Project structure

| File or directory | Purpose |
| --- | --- |
| `app.py` | FastAPI web application, chat streaming, conversation/history routes, and document uploads |
| `agent.py` | Gemini model setup and LangGraph agent workflow |
| `tools.py` | Agent tools for calculation, uploaded-document search, memory, and web search |
| `rag.py` | Reads supported files, creates document chunks and embeddings, and retrieves relevant content from Chroma |
| `database.py` | SQLite models and helpers for conversations, chat messages, and long-term memory |
| `templates/index.html` | Browser chat interface |
| `test.py` | Manual agent test script |
| `main.py` | Small standalone greeting script; the web app is started through `app.py` |
| `pyproject.toml`, `uv.lock` | Project metadata, dependencies, and uv lockfile |
| `requirements.txt` | Dependencies for pip installation |

Runtime files are stored in `data/`, `uploads/`, and `chroma_db/`.

## License

See [LICENSE](./LICENSE).
