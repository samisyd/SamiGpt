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

## Docker Deployment

### 1. Build the Docker image

```bash
docker build -t bappygpt .
```

### 2. Run the Docker container

```bash
docker run -d \
  --name bappygpt \
  --restart always \
  -p 8080:8080 \
  --env-file .env \
  bappygpt
```

The app will be available at:

```text
http://localhost:8080
```

For CI/CD Deployment via Github actions
Create folder .github\wokflows
Create file cicd.yaml under this folder

You can generate the code from any chat models like chatgpt
GITHUB Actions CI-CD Deployment 

---

## AWS CI/CD Deployment with GitHub Actions

This project can be deployed to AWS using:

* GitHub Actions
* Amazon ECR
* Amazon EC2
* Docker
* GitHub self-hosted runner

---

### 1. Create an IAM User

Create an IAM user for deployment and attach the following policies:

```text
AmazonEC2ContainerRegistryFullAccess
AmazonEC2FullAccess
```

You can also use a more restricted custom IAM policy for production.

---

### 2. Create an ECR Repository to store/save Docker Image

Create an Amazon ECR repository.

Example full ECR image URI:
save the URI
```text
274285228998.dkr.ecr.us-east-2.amazonaws.com/samigpt

For GitHub Secrets, only save the repository name:

```text
ECR_REPO=samigpt
```

Do not save the full ECR URI as `ECR_REPO`.

---

### 3. Create an EC2 Instance

Create an Ubuntu EC2 instance.

Recommended inbound security group rule:

```text
Type: Custom TCP
Port: 8080
Source: 0.0.0.0/0
```

---

### 4. Install Docker on EC2

Connect to your EC2 instance and run:

```bash
sudo apt-get update -y
sudo apt-get upgrade -y
```

Install Docker:

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

Add the Ubuntu user to the Docker group:

```bash
sudo usermod -aG docker ubuntu
newgrp docker
```

Check Docker:

```bash
docker --version
```

---

### 5. Configure EC2 as a GitHub Self-Hosted Runner

Go to your GitHub repository:

```text
Settings → Actions → Runners → New self-hosted runner
```

Select Linux and follow the commands shown by GitHub.
Run all commands listed in github on the new EC2 instance 

it will ask Enter name of the runner - give - self-hosted 

After setup, start the runner:

```bash
./run.sh
```

For production, you can configure the runner as a service:

```bash
sudo ./svc.sh install
sudo ./svc.sh start
```

---

## GitHub Secrets

Add the following secrets in your GitHub repository:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_DEFAULT_REGION
ECR_REPO

GOOGLE_API_KEY
GOOGLE_MODEL
TAVILY_API_KEY
LANGSMITH_TRACING
LANGSMITH_ENDPOINT
LANGSMITH_API_KEY
LANGSMITH_PROJECT
```

Path:

```text
GitHub Repository → Settings → Secrets and variables → Actions → New repository secret
```

Example:

```text
AWS_DEFAULT_REGION=us-east-1
ECR_REPO=samigpt
GOOGLE_MODEL=gemini-2.5-flash
LANGSMITH_TRACING=true
LANGSMITH_ENDPOINT=https://api.smith.langchain.com
LANGSMITH_PROJECT=samigpt
```

---

## GitHub Actions Workflow

Create this file:

```text
.github/workflows/cicd.yaml
```

This workflow will:

1. Build your Docker image
2. Push the image to Amazon ECR
3. Pull the latest image on EC2
4. Stop the old container
5. Run the new container

---

## Usage

After running locally or deploying to AWS:

1. Open the app in your browser.
2. Start chatting with the AI assistant.
3. Upload documents to use them as context.
4. Ask questions about uploaded files.
5. Ask current-information questions to trigger web search.
6. Continue conversations with saved chat history.

---

## Example Questions

```text
Summarize the uploaded PDF.
```

```text
Search the web for the latest AI agent news.
```

```text
Based on my uploaded document, what are the key points?
```

```text
Calculate 125 * 48 / 6.
```

---

## Notes

* Do not commit your `.env` file to GitHub.
* Keep API keys inside GitHub Secrets for deployment.
* For production, avoid using `reload=True` in Uvicorn.
* Make sure port `8080` is open in your EC2 security group.
* Rotate any API keys that were accidentally exposed publicly.

---

## Contributing

Contributions are welcome.

To contribute:

1. Fork the repository.
2. Create a new branch.
3. Make your changes.
4. Submit a pull request.

---

## License

This project is open source. Please check the repository license for usage terms.

## License

See [LICENSE](./LICENSE).
