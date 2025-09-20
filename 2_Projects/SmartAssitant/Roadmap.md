### ## Phase 1: Foundation and API Verification

**Objective:** The primary goal of this initial phase is to de-risk the project by establishing a stable and verified communication channel with the **Gemini 2.5 Pro API**.

#### **Key Concepts & Strategy**

This phase is foundational. Before any complex logic is written, we must confirm that the core component—the connection to the generative model—is functional. This involves setting up a clean, isolated development environment and running a minimal test case to validate authentication, network access, and the basic request/response cycle.

#### **Considerations**

- **Environment Management:** A dedicated virtual environment is non-negotiable. It prevents dependency conflicts and ensures the application's requirements are explicitly defined, which is critical for future deployment or collaboration.
    
- **API Key Security:** The handling of the Google API key is a key security consideration. The strategy will be to use environment variables during development, which keeps credentials out of the source code. For any future production-level system, this would evolve to use a dedicated secrets management service.
    
- **Testable Outcome:** A simple script can successfully send a query to the Gemini 2.5 Pro endpoint and receive a coherent text response. This validates the entire communication pipeline from end to end.
    

---

### ## Phase 2: The Knowledge Core - Data Pipeline & Vectorization

**Objective:** To design and implement a robust pipeline that transforms the raw source documentation into a queryable, high-performance knowledge base using a local vector database.

#### **Key Concepts & Strategy**

This phase builds the foundation of the RAG system. The quality of this knowledge core will directly determine the quality of the chatbot's specialized answers. The strategy is to process and structure the data in a way that maximizes the chance of retrieving relevant context for any given query.

The pipeline will consist of three main stages:

1. **Ingestion:** All source Markdown files will be systematically located and loaded.
    
2. **Chunking:** This is a critical architectural decision. Instead of naive, fixed-size chunking which can sever context, the chosen strategy is **structure-aware chunking**. By using the Markdown headers (`#`, `##`, etc.) as delimiters, we create chunks that are semantically coherent, keeping function descriptions with their parent functions, for example.
    
3. **Embedding & Storage:** Each text chunk will be converted into a numerical vector using Google's latest embedding model. These vectors will be stored in **ChromaDB**. The choice of ChromaDB is strategic: it's a powerful, local-first vector store that requires minimal setup, making it ideal for this project's scale. The architecture will be modular, allowing ChromaDB to be swapped for a larger, cloud-based solution in the future if the data corpus grows exponentially.
    

#### **Considerations**

- **Metadata:** During ingestion, we will attach the source filename to each chunk. This metadata is invaluable for debugging and for potentially citing sources in the chatbot's response.
    
- **Idempotency:** The ingestion script should be designed to be runnable multiple times without creating duplicate entries, allowing for easy updates to the knowledge base as the source documentation changes.
    
- **Testable Outcome:** A fully populated vector database exists locally. This can be tested independently of the chatbot by writing a script that takes a text query, embeds it, and queries the database directly to inspect the relevance of the returned text chunks.
    

---

### ## Phase 3: RAG Orchestration Logic

**Objective:** To develop the core logic that connects the user's query, the vectorized knowledge core, and the generative model into a cohesive system.

#### **Key Concepts & Strategy**

This phase is the heart of the application, where retrieval and generation are orchestrated. The flow is as follows: a user's query is vectorized, used to search the knowledge core for relevant context, and this context is then packaged with the original query and sent to the LLM.

- **Semantic Search:** The retrieval mechanism will be **semantic**, not keyword-based. This means the system finds chunks that are conceptually similar to the user's question, even if they don't share the same words. This is fundamental to the system's ability to understand intent.
    
- **Prompt Engineering:** The structure of the prompt sent to Gemini 2.5 Pro is the most critical element of this phase. A carefully engineered **system prompt** will act as the model's "constitution," instructing it on its persona and, most importantly, its operational logic. It will define a clear hierarchy of knowledge:
    
    1. Prioritize the retrieved RAG context above all else.
        
    2. If context is present but insufficient, acknowledge this limitation before proceeding.
        
    3. Only use general knowledge as a fallback or for non-domain-specific questions. This engineered prompt is the key to achieving the desired hybrid behavior.
        
- **Hyperparameter Tuning:** The number of documents to retrieve (`k`) is a key parameter. The roadmap suggests starting with `k=4`, which represents a balance between providing enough context and avoiding irrelevant noise that could distract the model.
    
- **Testable Outcome:** The CLI application can now answer questions about the source documentation with high fidelity. The test involves asking a specific question that the base Gemini model would not know, and verifying that the RAG-enabled application answers it correctly.
    

---

### ## Phase 4: State Management and Usability Enhancements

**Objective:** To elevate the core RAG loop from a simple request-response mechanism to a stateful, conversational agent with robust logging.

#### **Key Concepts & Strategy**

- **Conversational Memory:** The strategic choice here is to leverage Gemini 2.5 Pro's very large context window by implementing a **full-history memory**. For the expected conversation length (~20 turns), this is the optimal approach. It is computationally simpler, lower latency, and guarantees perfect recall compared to summarization techniques, which can lose nuance. The entire chat history will be passed along with each new query.
    
- **Interaction Logging:** A real-time Markdown logging system will be implemented. This serves two purposes. First, it creates an automatic, human-readable transcript of every conversation for review and analysis. Second, it provides an immediate method for visualizing the model's formatted output (like code blocks and lists) without needing a web interface.
    
- **Testable Outcome:** The chatbot can answer follow-up questions that refer to previous turns in the conversation. Simultaneously, a corresponding `.md` log file is created and accurately populated with the full dialogue.
    

---

### ## Phase 5: Advanced Feature - Dynamic Codebase Injection

**Objective:** To implement a power-user feature that allows for the temporary injection of an entire codebase into the conversation for specific, deep-dive analysis.

#### **Key Concepts & Strategy**

This feature provides on-demand, hyper-specific context. It will be triggered by a special command (e.g., `/load <path>`).

- **Mechanism:** Upon receiving the command, the application will perform a recursive scan of the target directory. It will read the content of all relevant source files (filtering by extension) and concatenate them into a single, massive text block. This block, clearly demarcated, will be prepended to the context of the next API call.
    
- **Contextual Scope:** This injected context is ephemeral, intended for a single session of analysis. It supplements, rather than replaces, the permanent knowledge stored in the vector database.
    

#### **Considerations**

- **Token Limit Awareness:** This feature's primary technical constraint is the model's maximum context window. The implementation must include safeguards to warn the user or truncate the content if a provided folder is too large to be processed. This is the most significant trade-off of this powerful capability.
    
- **Testable Outcome:** The user can issue the `/load` command with a path to a local code repository. They can then ask a specific question about a function or dependency within that repository, and the chatbot will provide an answer based on the just-provided source code.

