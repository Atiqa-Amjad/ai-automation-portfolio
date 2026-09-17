# RAG Knowledge Base Chatbot (Supabase + Google Gemini)

An AI chatbot that answers questions grounded in a company's own documents, built on an open-source-friendly stack — Supabase as the vector database and Google Gemini as the language model.


## 🎯 Problem It Solves
Generic chatbots can't answer questions about a company's internal documents, product manuals, policies, or knowledge base — and fine-tuning a model for this is expensive and slow. RAG solves this by letting the AI "look up" relevant information from real documents at the moment it answers, so responses stay accurate and grounded in the source material.

This version demonstrates the same RAG pattern built on a different, budget-friendly stack — useful for clients who already use Supabase for their database or prefer Gemini's pricing/context window over OpenAI.

## ⚙️ How It Works

The workflow has two parts:

**1. Indexing Pipeline** (prepares the knowledge base)
- **Google Drive** — pulls the source document to index
- **Recursive Character Text Splitter** — breaks the document into smaller, overlapping chunks so retrieval stays precise
- **Embeddings (Google Gemini)** — converts each chunk into a vector representation
- **Supabase Vector Store** — stores those vectors (using Supabase's `pgvector` extension) for fast similarity search

**2. Chat / Retrieval Pipeline** (answers questions)
- **When chat message received** — triggers on an incoming user question
- **Question and Answer Chain** — the core RAG chain that ties the model and retriever together
- **Vector Store Retriever → Supabase Vector Store** — searches the indexed vectors for the chunks most relevant to the question
- **Google Gemini Chat Model** — generates a natural-language answer using the retrieved chunks as context

## 🧩 Tech Stack
| Component | Role |
|---|---|
| n8n | Workflow orchestration |
| Google Drive node | Source document ingestion |
| Recursive Character Text Splitter | Document chunking |
| Google Gemini Embeddings | Converts text chunks into vectors |
| Supabase (pgvector) | Vector database for semantic search |
| Google Gemini Chat Model + Question and Answer Chain (LangChain) | Retrieval-grounded answer generation |

## 💡 Why This Matters
This is the same RAG architecture as the [Pinecone + OpenAI version](../rag-chatbot-pinecone-openai), rebuilt on a different vector database and model provider. Being able to swap components like this matters for client work — some clients want the cheapest stack, some already run Supabase for their app database, some need a specific model provider for compliance reasons. Knowing the pattern, not just one specific tool combination, is what makes a RAG implementation genuinely reusable.

## 🚀 How to Recreate This
1. In n8n, add a Google Drive node to pull your source document(s), then a Recursive Character Text Splitter to chunk the text.
2. Add a Google Gemini Embeddings node and connect it to a Supabase Vector Store node (insert mode) to index the chunks into a Supabase table with `pgvector` enabled.
3. On a separate branch, add a Chat Trigger connected to a Question and Answer Chain, with a Google Gemini Chat Model and a Vector Store Retriever (pointed at the same Supabase table) as its inputs.
4. Connect credentials: Google Drive OAuth2, Google Gemini API, Supabase API/project credentials.
5. Run the indexing branch once to populate Supabase, then use the chat trigger to ask questions about the indexed documents.

*(This project is shared here as a portfolio reference — the exported workflow file isn't included, but I'm happy to walk through the setup or build a version for your specific use case.)*
