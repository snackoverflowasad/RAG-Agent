# Agent With RAG

This notebook demonstrates an agentic Retrieval-Augmented Generation (RAG) workflow built with LangChain. It combines external tools, a retriever built from LangChain docs, and an OpenAI chat model into a single tool-using agent.

The notebook is centered on `agents.ipynb`.

## What It Does

The notebook builds an agent that can:

- Search Wikipedia.
- Query ArXiv for research papers.
- Retrieve relevant information from LangChain documentation through a FAISS-backed retriever.
- Use an OpenAI chat model to decide which tool to call and synthesize the final response.

This is a practical example of a multi-tool agent, where the model is not limited to its own knowledge and can fetch live or indexed context before answering.

## Notebook Flow

### 1. Wikipedia tool

The notebook initializes `WikipediaAPIWrapper` and `WikipediaQueryRun` so the agent can answer general factual questions.

### 2. Environment setup

The notebook loads environment variables from `.env` and uses `OPENAI_API_KEY` for OpenAI access.

### 3. Retriever-backed RAG tool

A web page from LangChain docs is loaded with `WebBaseLoader`, split into chunks, embedded with `OpenAIEmbeddings`, and indexed with FAISS.

That vector store is converted into a retriever and wrapped with `create_retriever_tool` so the agent can search the indexed documentation.

### 4. ArXiv tool

The notebook sets up `ArxivAPIWrapper` and `ArxivQueryRun` to fetch current research paper metadata.

### 5. Tool list

The agent is given three tools:

- `wiki`
- `arxiv`
- `retrieval_tool`

### 6. LLM and prompt

A `ChatOpenAI` model is created and paired with the `hwchase17/openai-functions-agent` prompt template.

### 7. Agent creation

The notebook builds an OpenAI tools agent with `create_openai_tools_agent` and wraps it in `AgentExecutor`.

### 8. Final invocation

The notebook runs a sample query:

- What is LangSmith?
- What are the latest research papers on RAG?

## Requirements

The dependencies for this notebook are listed in `requirement.txt`.

Important packages include:

- `langchain-openai`
- `langchain-community`
- `langchain-core`
- `langchain-classic`
- `faiss-cpu`
- `python-dotenv`
- `wikipedia`
- `arxiv`

## Setup

### 1. Create a virtual environment

```bash
python -m venv .venv
.venv\\Scripts\\activate
```

### 2. Install dependencies

```bash
pip install -r requirement.txt
```

### 3. Configure environment variables

Create a `.env` file in this folder and add your OpenAI key:

```env
OPENAI_API_KEY=your_api_key_here
```

### 4. Run the notebook

Open `agents.ipynb` in VS Code or Jupyter and execute the cells from top to bottom.

## Notes

- The notebook uses `create_retriever_tool`, so the retriever must be an actual retriever object, not the `as_retriever` method itself.
- `AgentExecutor.invoke(...)` returns a dictionary, so the final answer should be accessed with `['output']`.
- If the web loader or API tools fail, check network access and your API key first.
