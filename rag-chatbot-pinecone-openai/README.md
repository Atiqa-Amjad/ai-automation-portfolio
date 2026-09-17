# RAG Knowledge Base Chatbot (Pinecone + OpenAI)

An AI chatbot that answers questions using a company's own documents — instead of relying only on the model's general knowledge, it retrieves the most relevant passages from a private knowledge base and grounds its answers in them (Retrieval-Augmented Generation).


## 🎯 Problem It Solves
Generic chatbots can't answer questions about a company's internal documents, product manuals, policies, or knowledge base — and fine-tuning a model for this is expensive and slow. RAG solves this by letting the AI "look up" relevant information from real documents at the moment it answers, so responses stay accurate and up to date as documents change.

## ⚙️ How It Works

The workflow has two parts:

**1. Indexing Pipeline** (prepares the knowledge base)
- **Google Drive** — pulls the source document to index
- **Recursive Character Text Splitter** — breaks the document into smaller, overlapping chunks so retrieval stays precise
- **Embeddings (OpenAI)** — converts each chunk into a vector representation
- **Pinecone Vector Store** — stores those vectors for fast similarity search

**2. Chat / Retrieval Pipeline** (answers questions)
- **When chat message received** — triggers on an incoming user question
- **Question and Answer Chain** — the core RAG chain that ties the model and retriever together
- **Vector Store Retriever → Pinecone Vector Store** — searches the indexed vectors for the chunks most relevant to the question
- **OpenAI Chat Model** — generates a natural-language answer using the retrieved chunks as context

## 🧩 Tech Stack
| Component | Role |
|---|---|
| n8n | Workflow orchestration |
| Google Drive node | Source document ingestion |
| Recursive Character Text Splitter | Document chunking |
| OpenAI Embeddings | Converts text chunks into vectors |
| Pinecone | Vector database for semantic search |
| OpenAI Chat Model + Question and Answer Chain (LangChain) | Retrieval-grounded answer generation |

## 💡 Why This Matters
This is a foundational RAG pattern — the same architecture scales to customer support knowledge bases, internal company wikis, product documentation bots, and legal/policy Q&A assistants. Swapping the vector store or embedding model (see the companion Supabase + Gemini version) shows the same pattern adapts across different providers.

## 🚀 How to Recreate This
1. In n8n, add a Google Drive node to pull your source document(s), then a Recursive Character Text Splitter to chunk the text.
2. Add an OpenAI Embeddings node and connect it to a Pinecone Vector Store node (insert mode) to index the chunks.
3. On a separate branch, add a Chat Trigger connected to a Question and Answer Chain, with an OpenAI Chat Model and a Vector Store Retriever (pointed at the same Pinecone index) as its inputs.
4. Connect credentials: Google Drive OAuth2, OpenAI API, Pinecone API.
5. Run the indexing branch once to populate Pinecone, then use the chat trigger to ask questions about the indexed documents.

*(This project is shared here as a portfolio reference — the exported workflow file isn't included, but I'm happy to walk through the setup or build a version for your specific use case.)*
