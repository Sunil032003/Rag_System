# 🤖 Agentic RAG Assistant with LangGraph

An **Agentic RAG (Retrieval-Augmented Generation) Assistant** built using **LangGraph, LangChain, Groq, Pinecone, Tavily, and Hugging Face embeddings**.

The system intelligently decides where to get information from:

**Private Knowledge Base → Web Search → Query Rewrite → Retry → Insufficient Evidence**

Instead of blindly retrieving documents for every question, the agent evaluates the retrieved evidence and dynamically chooses the next step.

---

## 🚀 Project Overview

Traditional RAG systems generally follow a simple pipeline:

```text
User Question
      ↓
Retrieve Documents
      ↓
Generate Answer
```

This project improves that approach by introducing an **agentic workflow**.

The agent can:

- Decide whether a question requires the private knowledge base
- Retrieve relevant documents from Pinecone
- Grade the retrieved documents
- Generate an answer when sufficient evidence exists
- Fall back to Tavily web search when the knowledge base is insufficient
- Grade web search results
- Rewrite the query when web evidence is insufficient
- Retry the web search
- Return an insufficient-evidence response when reliable information cannot be found
- Answer simple conversational questions directly

---

# 🧠 Architecture

```text
                         ┌─────────────────────┐
                         │    User Question    │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │   Question Router   │
                         └──────────┬──────────┘
                                    │
                     ┌──────────────┴──────────────┐
                     │                             │
                  KB Question                Simple Chat
                     │                             │
                     ▼                             ▼
              ┌─────────────┐              ┌──────────────┐
              │ Pinecone    │              │ Direct LLM   │
              │ Retriever   │              │   Answer     │
              └──────┬──────┘              └──────────────┘
                     │
                     ▼
              ┌─────────────┐
              │ KB Evidence │
              │   Grader    │
              └──────┬──────┘
                     │
              ┌──────┴──────┐
              │             │
            Good           Weak
              │             │
              ▼             ▼
       ┌─────────────┐  ┌─────────────┐
       │ Generate    │  │   Tavily    │
       │ from KB     │  │ Web Search  │
       └─────────────┘  └──────┬──────┘
                               │
                               ▼
                        ┌─────────────┐
                        │ Web Evidence│
                        │   Grader    │
                        └──────┬──────┘
                               │
                    ┌──────────┴──────────┐
                    │                     │
                  Good                   Weak
                    │                     │
                    ▼                     ▼
             ┌─────────────┐       ┌─────────────┐
             │ Generate    │       │ Rewrite     │
             │ from Web    │       │ Query       │
             └─────────────┘       └──────┬──────┘
                                          │
                                          ▼
                                   ┌─────────────┐
                                   │ Retry Web   │
                                   │   Search    │
                                   └──────┬──────┘
                                          │
                                          ▼
                               ┌────────────────────┐
                               │ Insufficient        │
                               │ Evidence Response   │
                               └────────────────────┘
```

---

# ✨ Key Features

### 1. Intelligent
