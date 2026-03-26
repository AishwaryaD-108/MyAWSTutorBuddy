# Building "MyAWSTutorBuddy" — The Agentic AI System

> A guide to building the AI-powered system that **generates**, **teaches**, and **adapts** the 30-day AWS GenAI learning plan automatically — and incrementally builds the **BrainBot** project (a multimodal RAG-powered AI assistant).

---

## 1. What Are We Building?

An **agentic AI system** — an AI that doesn't just answer questions, but **autonomously plans, researches, creates content, writes code, and adapts** to the learner's progress.

**Two layers:**
- **Layer 1 — The Tutor System (MyAWSTutorBuddy):** An agentic AI that generates daily tutorials and code.
- **Layer 2 — The Student Project (BrainBot):** The actual app being built day by day — a multimodal RAG chatbot that reads documents, sees images, and speaks.

**The tutor system will:**
1. Absorb AWS documentation for one GenAI-related service each day (Bedrock, Titan, Rekognition, OpenSearch, etc.)
2. Generate an easy-to-understand tutorial focused on the GenAI use case
3. Produce incremental BrainBot project code (Python + boto3) — each day's code builds on all previous days
4. Walk through the RAG pipeline, voice loop, and multimodal features step-by-step
5. Track learner progress and adapt difficulty
6. Answer follow-up questions with RAG-powered context from AWS docs

---

## 2. Agentic AI Concepts Used

### 2.1 — Agent Architecture

| Concept | What It Means | How We Use It |
|---------|---------------|---------------|
| **Autonomous Agent** | An AI that can plan and execute multi-step tasks independently | The tutor agent decides what to teach, generates content, and writes BrainBot code without manual prompting each step |
| **Tool Use / Function Calling** | The agent can call external tools (APIs, search, file I/O) | Agent calls AWS docs, invokes Bedrock for content generation, validates Python code, writes tutorial files |
| **Planning & Reasoning** | The agent breaks a goal into sub-steps and reasons about order | Agent decomposes "teach Bedrock" into: read docs → extract GenAI concepts → write tutorial → generate BrainBot code → create quiz |
| **Memory (Short-term & Long-term)** | The agent remembers past interactions and accumulated knowledge | Tracks which services are done, the current state of BrainBot's codebase, and learner progress |
| **Reflection / Self-Critique** | The agent reviews its own output and improves it | After generating a tutorial, the agent checks: Is the GenAI concept clear? Does the BrainBot code integrate with yesterday's code? |
| **Retrieval-Augmented Generation (RAG)** | Pulling relevant documents into context before generating answers | Agent retrieves latest AWS docs + existing BrainBot code before writing the tutorial — the tutor itself uses RAG! |
| **Multi-Agent Orchestration** | Multiple specialized agents collaborate on a task | Separate agents for: Research, Content Writing, Code Generation, RAG Pipeline, Voice/Multimodal, Quiz Creation |
| **Multimodal Processing** | Handling text, images, and audio in a single system | Agents coordinate across Rekognition (vision), Transcribe (speech-to-text), Polly (text-to-speech), and Bedrock (reasoning) |

### 2.2 — Core Design Patterns

```
┌──────────────────────────────────────────────────────────────┐
│                     ORCHESTRATOR AGENT                        │
│  (Plans the day, decides which agents to invoke, sequences   │
│   tasks based on day number and service category)            │
└──┬──────────┬──────────┬──────────┬──────────┬──────────┬───┘
   │          │          │          │          │          │
┌──▼───┐ ┌───▼────┐ ┌───▼────┐ ┌───▼────┐ ┌───▼────┐ ┌───▼───┐
│RESRCH│ │CONTENT │ │ CODE   │ │  RAG   │ │ VOICE/ │ │ QUIZ  │
│AGENT │ │WRITER  │ │GENERAT.│ │PIPELINE│ │MULTI-  │ │ AGENT │
│      │ │AGENT   │ │AGENT   │ │AGENT   │ │MODAL   │ │       │
│Fetch │ │Write   │ │BrainBot│ │Embed + │ │Image + │ │Test   │
│AWS   │ │tutorial│ │code    │ │Search  │ │Audio   │ │under- │
│docs  │ │        │ │daily   │ │logic   │ │logic   │ │stand  │
└──┬───┘ └───┬────┘ └───┬────┘ └───┬────┘ └───┬────┘ └───┬───┘
   │         │          │          │          │          │
┌──▼─────────▼──────────▼──────────▼──────────▼──────────▼────┐
│                    SHARED MEMORY / STATE                      │
│  Learner profile │ BrainBot codebase │ Day progress tracker   │
│  Vector store (AWS docs) │ Conversation history               │
└─────────────────────────────────────────────────────────────┘
```

---

## 3. System Components — How to Build It

### 3.1 — Tech Stack

| Component | Technology |
|-----------|------------|
| Language | Python 3.11+ |
| LLM Provider | **AWS Bedrock** (Claude 3 / Titan) — the same service BrainBot uses! |
| Embeddings | **Amazon Titan Embeddings** via Bedrock (or `sentence-transformers` locally) |
| Agent Framework | LangChain + LangGraph (or build from scratch with Bedrock agents) |
| Vector Store (RAG) | **Amazon OpenSearch** (production) / FAISS or ChromaDB (local dev) |
| Document Loader | LangChain document loaders (Web, PDF, Markdown) + **Amazon Kendra** |
| NLP Enrichment | **Amazon Comprehend** (sentiment, entities, PII detection) |
| Image Analysis | **Amazon Rekognition** (labels, text, faces) |
| Voice I/O | **Amazon Polly** (TTS) + **Amazon Transcribe** (STT) + **Amazon Lex** (dialog) |
| Storage | **Amazon S3** (documents) + **DynamoDB** (metadata, chat history) + **Aurora pgvector** (embeddings) |
| Compute | **AWS Lambda** (serverless) + **ECS Fargate** (heavy processing) |
| API Layer | **Amazon API Gateway** (REST endpoints) |
| Orchestration | **AWS Step Functions** (multi-step pipelines) + **EventBridge** (scheduling) |
| Data Pipelines | **AWS Glue** (ETL) + **Amazon Athena** (SQL analytics) |
| Security | **IAM** (roles) + **KMS** (encryption) + **Secrets Manager** (credentials) |
| Frontend | **AWS Amplify** (deployment) + **CloudFront** (CDN) |
| CLI Interface | `rich` library for beautiful terminal output |
| Scheduling | Python `schedule` lib, cron, or **EventBridge Scheduler** |

### 3.2 — Project Structure

```
MyAWSTutorBuddy/
├── agents/
│   ├── orchestrator.py        # Master agent — plans day, delegates, sequences
│   ├── research_agent.py      # Fetches & processes AWS docs
│   ├── content_agent.py       # Writes tutorials (GenAI-focused)
│   ├── code_agent.py          # Generates incremental BrainBot code
│   ├── rag_pipeline_agent.py  # Builds/tests RAG logic (embed, search, retrieve)
│   ├── multimodal_agent.py    # Handles image + voice integration code
│   └── quiz_agent.py          # Creates quizzes & checks understanding
├── tools/
│   ├── doc_fetcher.py         # Scrapes/loads AWS documentation
│   ├── code_validator.py      # Validates generated Python code (syntax + imports)
│   ├── file_writer.py         # Writes tutorials & code to disk
│   ├── progress_tracker.py    # Tracks learner state & day completion
│   ├── bedrock_invoker.py     # Calls Bedrock (Claude/Titan) for content generation
│   ├── embedding_tool.py      # Generates embeddings via Titan Embeddings
│   ├── opensearch_tool.py     # Indexes/queries OpenSearch for RAG
│   ├── comprehend_tool.py     # Extracts entities, sentiment, PII from text
│   ├── rekognition_tool.py    # Analyzes images (labels, text, faces)
│   ├── voice_tool.py          # Polly (TTS) + Transcribe (STT) wrappers
│   └── s3_tool.py             # Upload/download documents and assets
├── memory/
│   ├── vector_store.py        # RAG vector database management (OpenSearch/FAISS)
│   ├── learner_profile.py     # Learner preferences, scores & history
│   ├── knowledge_base.py      # Accumulated AWS GenAI knowledge
│   └── conversation_store.py  # DynamoDB-backed conversation history
├── output/
│   ├── day_01_bedrock/
│   │   ├── tutorial.md        # Generated tutorial (Bedrock + GenAI concepts)
│   │   ├── code.py            # Day's BrainBot code increment
│   │   └── quiz.md            # Comprehension quiz
│   ├── day_02_titan/
│   │   └── ...                # Titan embeddings + similarity search code
│   └── project/
│       └── brainbot/          # The incremental BrainBot project
│           ├── chat.py        # CLI chatbot (Day 1: Bedrock)
│           ├── embeddings.py  # Vector embeddings (Day 2: Titan)
│           ├── vision.py      # Image analysis (Day 3: Rekognition)
│           ├── nlp.py         # Text analysis (Day 4: Comprehend)
│           ├── dialog.py      # Conversational bot (Day 5: Lex)
│           ├── voice.py       # Voice I/O (Day 6-7: Polly + Transcribe)
│           ├── rag.py         # RAG pipeline (Days 9-13: S3 + OpenSearch + Kendra)
│           ├── api.py         # REST API handlers (Day 18: API Gateway)
│           ├── pipeline.py    # Step Functions orchestration (Day 22)
│           └── config.py      # Secrets + config (Days 24-25: KMS + Secrets Manager)
├── config/
│   ├── learning_plan.yaml     # 30-day plan configuration (service per day)
│   ├── prompts.yaml           # Agent prompt templates (per service category)
│   └── services.yaml          # Service metadata (category, dependencies, difficulty)
├── main.py                    # Entry point — run the daily lesson
├── requirements.txt
└── README.md
```

---

## 4. How Each Agent Works

### 4.1 — Orchestrator Agent (`orchestrator.py`)

**Role:** The brain — decides what happens each day, selects which agents to invoke based on the service category, and ensures BrainBot code integrates properly.

**Agentic Concept:** *Planning & Task Decomposition*

```python
# Pseudocode
class OrchestratorAgent:
    def run_daily_lesson(self, day_number):
        # 1. PLAN — Determine today's service and category
        service = self.get_service_for_day(day_number)  # e.g., Day 1 = "Bedrock"
        category = service.category  # e.g., "AI_ML", "STORAGE_RAG", "COMPUTE", etc.
        plan = self.create_plan(service, category)

        # 2. DELEGATE — Send tasks to specialist agents
        docs = self.research_agent.fetch_docs(service)
        tutorial = self.content_agent.write_tutorial(service, docs, category)
        code = self.code_agent.generate_brainbot_code(service, day_number)

        # 3. CATEGORY-SPECIFIC agents
        if category == "AI_ML":
            # Days 1-8: Also invoke multimodal agent for voice/vision integration
            integration = self.multimodal_agent.generate_integration(service, code)
        elif category == "STORAGE_RAG":
            # Days 9-13: Also invoke RAG pipeline agent
            rag_code = self.rag_pipeline_agent.build_rag_step(service, code)

        # 4. REFLECT — Review outputs for quality and integration
        self.review_and_improve(tutorial, code)

        # 5. DELIVER — Save and present to learner
        quiz = self.quiz_agent.create_quiz(service, tutorial)
        self.save_outputs(day_number, tutorial, code, quiz)
```

### 4.2 — Research Agent (`research_agent.py`)

**Role:** Finds and processes AWS documentation for the day's service, with special depth for GenAI services.

**Agentic Concepts:** *Tool Use*, *RAG (Retrieval-Augmented Generation)*

**How it works:**
1. **Fetch** — Loads AWS docs, API references, blog posts, and GenAI-specific guides
2. **Chunk** — Splits documents into manageable pieces
3. **Embed** — Converts chunks into vectors using Titan Embeddings
4. **Store** — Saves in OpenSearch (or local FAISS) for content agent to query
5. **Enrich** — Runs Comprehend to extract key entities and tag the chunks
6. **Summarize** — Creates a condensed knowledge brief

```python
class ResearchAgent:
    def fetch_docs(self, service):
        # Tool: doc_fetcher — loads AWS docs for the service
        raw_docs = self.doc_fetcher.load(service.name)

        # RAG: chunk and embed with Titan Embeddings
        chunks = self.text_splitter.split(raw_docs)
        embeddings = self.embedding_tool.embed_batch(chunks)  # Titan Embeddings
        self.vector_store.index_documents(chunks, embeddings)  # OpenSearch

        # NLP enrichment: extract entities and key phrases
        for chunk in chunks:
            entities = self.comprehend_tool.detect_entities(chunk.text)
            chunk.metadata["entities"] = entities

        # Summarize key points via Bedrock
        summary = self.bedrock_invoker.summarize(raw_docs)
        return summary
```

### 4.3 — Content Writer Agent (`content_agent.py`)

**Role:** Transforms raw knowledge into a friendly, easy-to-understand tutorial focused on how this service fits into a GenAI application.

**Agentic Concepts:** *Prompt Engineering*, *Self-Critique / Reflection*

**How it works:**
1. Receives the research summary + RAG context
2. Generates a tutorial using category-specific prompt templates (AI/ML, Storage, Compute, etc.)
3. **Self-reviews**: checks clarity, GenAI relevance, and BrainBot integration
4. Rewrites sections that don't meet quality bar

```python
class ContentAgent:
    def write_tutorial(self, service, docs, category):
        # RAG: retrieve most relevant chunks
        context = self.vector_store.similarity_search(service.name)

        # Select category-specific prompt template
        template = self.prompt_templates[category]  # AI_ML, STORAGE_RAG, etc.

        # Generate tutorial with structured prompt via Bedrock
        tutorial = self.bedrock_invoker.generate(
            prompt=template,
            context=context,
            service=service,
            brainbot_context="How this service powers BrainBot's capabilities"
        )

        # REFLECTION: Self-critique — tailored to GenAI learning
        critique = self.bedrock_invoker.evaluate(tutorial, criteria=[
            "Is the GenAI use case clearly explained?",
            "Does it show how this service connects to BrainBot?",
            "Are analogies beginner-friendly?",
            "Is the boto3 code example correct and runnable?"
        ])

        if critique.needs_improvement:
            tutorial = self.bedrock_invoker.improve(tutorial, critique.feedback)

        return tutorial
```

### 4.4 — Code Generator Agent (`code_agent.py`)

**Role:** Writes the incremental BrainBot project code for each day, ensuring each new file integrates with previous days.

**Agentic Concepts:** *Tool Use (code validation)*, *Memory (builds on previous days' BrainBot code)*

**How it works:**
1. Reads the existing BrainBot codebase (memory of what's been built)
2. Knows which day and service category to determine the code target
3. Generates the new code additions (e.g., Day 3 adds `vision.py` that imports from Day 1's `chat.py`)
4. **Validates** the code (syntax check, import check, integration check)
5. Ensures it integrates cleanly with all previous days' code

```python
class CodeAgent:
    def generate_brainbot_code(self, service, day_number):
        # MEMORY: Read existing BrainBot project code
        existing_code = self.file_reader.read_project("output/project/brainbot/")
        previous_services = self.progress_tracker.get_completed()

        # Determine what file to create/update
        # Day 1: chat.py, Day 2: embeddings.py, Day 3: vision.py, etc.
        target_file = self.service_to_file_map[service.name]

        # Generate new code that builds on existing BrainBot modules
        new_code = self.bedrock_invoker.generate(
            prompt=self.code_prompt_template,
            service=service,
            existing_code=existing_code,
            target_file=target_file,
            day_number=day_number,
            instructions=f"Import and build on: {[s.file for s in previous_services]}"
        )

        # TOOL USE: Validate the code
        is_valid = self.code_validator.check(new_code)
        if not is_valid:
            new_code = self.bedrock_invoker.fix_code(new_code, self.code_validator.errors)

        return new_code
```

### 4.5 — RAG Pipeline Agent (`rag_pipeline_agent.py`) — NEW

**Role:** Specifically handles the RAG-related code generation for Days 9–13 (S3, DynamoDB, Aurora, OpenSearch, Kendra). Ensures the embedding → indexing → retrieval → generation pipeline is built correctly and incrementally.

**Agentic Concepts:** *Domain Expertise*, *Integration Testing*

```python
class RAGPipelineAgent:
    def build_rag_step(self, service, existing_code):
        # Each day adds one piece of the RAG pipeline:
        # Day 9  (S3):         Document upload + storage
        # Day 10 (DynamoDB):   Metadata tracking + conversation memory
        # Day 11 (Aurora):     pgvector embeddings storage + SQL search
        # Day 12 (OpenSearch): k-NN vector search + hybrid retrieval
        # Day 13 (Kendra):     Enterprise search alternative

        rag_stage = self.service_to_rag_stage[service.name]

        code = self.bedrock_invoker.generate(
            prompt=self.rag_pipeline_template,
            stage=rag_stage,
            existing_rag_code=existing_code.get("rag.py"),
            embeddings_module=existing_code.get("embeddings.py"),  # Day 2 Titan code
        )

        # Validate: does the RAG pipeline actually return relevant results?
        test_result = self.code_validator.test_rag_retrieval(code)
        return code
```

### 4.6 — Multimodal Agent (`multimodal_agent.py`) — NEW

**Role:** Handles the voice and vision integration code for Days 3–7 (Rekognition, Comprehend, Lex, Polly, Transcribe). Ensures BrainBot's multimodal loop (see → hear → think → speak) works end-to-end.

**Agentic Concepts:** *Cross-Service Integration*, *Multimodal Orchestration*

```python
class MultimodalAgent:
    def generate_integration(self, service, existing_code):
        # Build the multimodal pipeline piece by piece:
        # Day 3 (Rekognition): Image → Labels → Bedrock describes
        # Day 4 (Comprehend):  Text → Sentiment + Entities → Enriched metadata
        # Day 5 (Lex):         Structured dialog → Intent routing → Bedrock fallback
        # Day 6 (Polly):       Text response → Audio output
        # Day 7 (Transcribe):  Audio input → Text → Full voice loop

        modality = self.service_to_modality[service.name]

        code = self.bedrock_invoker.generate(
            prompt=self.multimodal_template,
            modality=modality,
            chat_module=existing_code.get("chat.py"),        # Day 1 Bedrock code
            existing_vision=existing_code.get("vision.py"),  # Day 3+
            existing_voice=existing_code.get("voice.py"),    # Day 6+
        )
        return code
```

### 4.7 — Quiz Agent (`quiz_agent.py`)

**Role:** Creates comprehension checks and interactive exercises.

**Agentic Concepts:** *Adaptive Difficulty*, *Learner Modeling*

---

## 5. Key Agentic AI Concepts — Deep Dive

### 5.1 — RAG (Retrieval-Augmented Generation)

**What:** Instead of relying solely on the LLM's training data, we fetch fresh, relevant documents and inject them into the prompt.

**Why:** AWS services (especially GenAI ones like Bedrock) evolve rapidly. RAG ensures tutorials contain the latest information. This is also **the core pattern BrainBot teaches** — learners build RAG from scratch over Days 9–13.

**Implementation (using the actual AWS services from the 30-day plan):**
```
AWS Docs → S3 (Day 9) → Text Splitter → Titan Embeddings (Day 2)
                                                    ↓
                                        OpenSearch k-NN Index (Day 12)
                                                    ↓
User Query → Titan Embed → OpenSearch Similarity Search → Top-K Chunks
                                                    ↓
                                    Bedrock (Claude) Prompt + Context → Answer
```

**Alternative RAG path (Day 13):**
```
AWS Docs → S3 → Kendra Data Source → Natural Language Query → Kendra Results → Bedrock → Answer
```

**Python libraries:** `boto3` (Bedrock, OpenSearch, Kendra), `opensearch-py`, `langchain`, `langchain-aws`

> 💡 **Meta-level:** The tutor system itself uses RAG to write tutorials. BrainBot also uses RAG as its core feature. The learner builds RAG while being taught by RAG!

### 5.2 — Memory Systems

| Type | Purpose | Implementation |
|------|---------|----------------|
| **Conversation Memory** | Remember what was discussed in current session | **DynamoDB** table (matches Day 10 — learner builds this for BrainBot too) |
| **Episodic Memory** | Remember past days' lessons and interactions | JSON/SQLite store of daily summaries + what BrainBot code was generated |
| **Semantic Memory** | Accumulated AWS GenAI knowledge base | **OpenSearch** vector store with all processed docs (matches Day 12) |
| **Codebase Memory** | The current state of BrainBot's source code | File system scan of `output/project/brainbot/` — read before each generation |
| **Learner Profile** | Track progress, strengths, weaknesses per service category | YAML file with scores by category (AI/ML, Storage, Compute, etc.) |

### 5.3 — Tool Use / Function Calling

The agent has access to these tools:

| Tool | Purpose | AWS Service Used |
|------|---------|------------------|
| `fetch_aws_docs(service)` | Load official AWS documentation | Web scraping / S3 cached docs |
| `invoke_bedrock(prompt, model)` | Generate text via LLM | **Bedrock** (Claude/Titan) |
| `embed_text(chunks)` | Generate vector embeddings | **Titan Embeddings** via Bedrock |
| `search_vectors(query, index)` | RAG similarity search | **OpenSearch** k-NN |
| `search_kendra(query)` | Enterprise document search | **Kendra** |
| `detect_entities(text)` | Extract named entities from text | **Comprehend** |
| `detect_sentiment(text)` | Analyze text sentiment | **Comprehend** |
| `analyze_image(s3_key)` | Detect labels/text in images | **Rekognition** |
| `synthesize_speech(text)` | Convert text to audio | **Polly** |
| `transcribe_audio(s3_key)` | Convert audio to text | **Transcribe** |
| `write_file(path, content)` | Save tutorials and code to disk | Local filesystem / S3 |
| `validate_python(code)` | Check Python code for syntax errors | Local `ast.parse()` |
| `run_python(code)` | Execute Python code in sandbox | Subprocess / Lambda |
| `read_brainbot_codebase()` | Read all existing BrainBot files | Local filesystem |
| `get_learner_progress()` | Read learner's progress state | YAML / DynamoDB |
| `update_progress(day, status)` | Mark a day as complete | YAML / DynamoDB |

### 5.4 — Planning & Task Decomposition (ReAct Pattern)

The orchestrator uses the **ReAct** (Reasoning + Acting) pattern:

```
Thought: Today is Day 12 (OpenSearch). The learner has completed Bedrock, Titan
         Embeddings, Rekognition, Comprehend, and S3+DynamoDB storage.
         I need to teach OpenSearch vector search and build the core RAG retrieval
         for BrainBot.
Action:  Call research_agent.fetch_docs("OpenSearch")
Observation: Got 20 document chunks covering domains, k-NN, hybrid search, mappings.
Thought: Good. The tutorial should connect OpenSearch to the Titan Embeddings from
         Day 2 and the S3 document storage from Day 9. This is where RAG comes alive.
Action:  Call content_agent.write_tutorial("OpenSearch", docs, category="STORAGE_RAG")
Observation: Tutorial generated. It uses a "library with GPS coordinates" analogy for
             vector search. Explains k-NN clearly.
Thought: Now generate the BrainBot code. Must update rag.py to:
         1. Create an OpenSearch index with k-NN
         2. Index the Titan embeddings from embeddings.py (Day 2)
         3. Implement similarity_search() that retrieves relevant chunks
         4. Wire it into chat.py so Bedrock gets RAG context
Action:  Call rag_pipeline_agent.build_rag_step("OpenSearch", existing_code)
Observation: rag.py updated with OpenSearchRetriever class. Imports from embeddings.py.
Action:  Call code_agent.generate_brainbot_code("OpenSearch", day=12)
Observation: chat.py updated to call rag.similarity_search() before invoking Bedrock.
             BrainBot now answers questions using document context!
...
```

### 5.5 — Multi-Agent Collaboration

Agents communicate through a **shared state** (not direct messaging):

```python
# Shared state pattern
class SharedState:
    def __init__(self):
        self.current_day = 0
        self.current_service = ""
        self.service_category = ""         # AI_ML, STORAGE_RAG, COMPUTE, etc.
        self.research_output = None
        self.tutorial_output = None
        self.code_output = None
        self.learner_profile = {}
        self.knowledge_base = VectorStore()  # OpenSearch or FAISS

        # BrainBot-specific state
        self.brainbot_codebase = {}          # {filename: code_content}
        self.rag_pipeline_state = {}         # Which RAG stage is built
        self.multimodal_state = {            # Which modalities are wired
            "vision": False,     # Rekognition (Day 3)
            "nlp": False,        # Comprehend (Day 4)
            "dialog": False,     # Lex (Day 5)
            "tts": False,        # Polly (Day 6)
            "stt": False,        # Transcribe (Day 7)
        }
        self.completed_days = []             # Progress tracking
```

For more advanced orchestration, use **LangGraph** to define agent workflows as a directed graph with conditional edges. The graph structure naturally maps to the 30-day plan's service categories — different graph paths for AI days vs. Storage days vs. Compute days.

### 5.6 — Reflection & Self-Improvement

After generating content, the system runs a **critic loop**:

```
Generate → Evaluate → Score → If score < threshold → Regenerate with feedback
```

Evaluation criteria:
- **GenAI Relevance:** Does the tutorial clearly explain the GenAI use case?
- **BrainBot Integration:** Does the code build on previous days and integrate into BrainBot?
- **Clarity:** Would a beginner understand this?
- **Accuracy:** Are AWS API calls and parameters correct?
- **Code Quality:** Does the code run? Does it import from previous days' modules correctly?
- **RAG Coherence:** (Days 9-13) Does the RAG pipeline produce relevant retrievals?
- **Multimodal Completeness:** (Days 3-7) Does the voice/vision loop work end-to-end?

---

## 6. Implementation Roadmap

### Phase 1 — Core AI (Week 1 of development)
- [ ] Set up project structure (agents/, tools/, memory/, output/, config/)
- [ ] Implement Bedrock invoker tool (`bedrock_invoker.py`) — call Claude/Titan
- [ ] Implement Titan Embeddings tool (`embedding_tool.py`)
- [ ] Set up vector store (FAISS locally for dev, OpenSearch for production)
- [ ] Implement document fetcher tool (AWS docs loader)
- [ ] Build basic Research Agent with RAG pipeline
- [ ] Build basic Content Agent with GenAI-focused prompt templates
- [ ] Test with Day 1 (Bedrock) as proof of concept

### Phase 2 — BrainBot Code Generation (Week 2 of development)
- [ ] Build Code Generator Agent with BrainBot codebase awareness
- [ ] Implement code validation tool (syntax + import checking)
- [ ] Build RAG Pipeline Agent (for Days 9–13 code generation)
- [ ] Build Multimodal Agent (for Days 3–7 voice/vision code)
- [ ] Implement service-to-file mapping (`Bedrock → chat.py`, `Titan → embeddings.py`, etc.)
- [ ] Build Orchestrator Agent to chain all agents based on service category
- [ ] Generate Days 1–8 (AI/ML week) and review quality

### Phase 3 — Intelligence & Integration (Week 3 of development)
- [ ] Add memory system (learner profile, DynamoDB conversation store, codebase memory)
- [ ] Implement reflection/self-critique loop with GenAI-specific criteria
- [ ] Add Comprehend tool for NLP enrichment of docs
- [ ] Add Rekognition tool for image-related tutorial validation
- [ ] Build Quiz Agent with category-specific questions
- [ ] Build CLI interface with `rich`
- [ ] Add interactive Q&A mode (learner asks follow-ups, tutor uses RAG to answer)

### Phase 4 — Full Pipeline & Polish (Week 4 of development)
- [ ] Add daily scheduler (auto-generate next lesson via EventBridge or cron)
- [ ] Implement adaptive difficulty (easier/harder based on quiz scores per category)
- [ ] Add progress dashboard (which services done, BrainBot completeness %)
- [ ] Test full 30-day generation end-to-end (all 30 services → all 30 tutorials + BrainBot grows)
- [ ] Verify BrainBot's final codebase works as a complete application
- [ ] Documentation and README

---

## 7. Getting Started — Quick Setup

```bash
# Clone the project
cd MyAWSTutorBuddy

# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install dependencies
pip install boto3 langchain langchain-community langchain-aws \
            opensearch-py faiss-cpu chromadb rich pyyaml python-dotenv \
            numpy soundfile

# Set up environment variables
cat > .env << 'EOF'
AWS_PROFILE=default
AWS_REGION=us-east-1
BEDROCK_MODEL_ID=anthropic.claude-3-sonnet-20240229-v1:0
TITAN_EMBED_MODEL_ID=amazon.titan-embed-text-v2:0
OPENSEARCH_ENDPOINT=https://your-domain.us-east-1.es.amazonaws.com
EOF

# IMPORTANT: Enable Bedrock model access in AWS Console first!
# Go to: AWS Console → Bedrock → Model access → Request access to Claude + Titan

# Run your first lesson (Day 1: Bedrock)
python main.py --day 1

# Run a specific day
python main.py --day 12  # OpenSearch + RAG

# Interactive Q&A after a lesson
python main.py --day 1 --interactive
```

---

## 8. Key Python Libraries

| Library | Purpose |
|---------|---------|
| `boto3` | AWS SDK — interact with all 30 AWS services (Bedrock, Rekognition, Polly, etc.) |
| `langchain` + `langchain-aws` | Agent framework — chains, tools, memory, Bedrock integration |
| `langgraph` | Multi-agent orchestration as directed graphs with conditional routing |
| `opensearch-py` | OpenSearch client — index and query vectors for RAG |
| `faiss-cpu` | Local vector similarity search (dev/testing before OpenSearch) |
| `chromadb` | Local vector database alternative |
| `numpy` | Cosine similarity for embeddings, numerical operations |
| `rich` | Beautiful terminal output for CLI tutor interface |
| `pyyaml` | Configuration and learning plan file parsing |
| `python-dotenv` | Environment variable management (.env files) |
| `beautifulsoup4` | Web scraping AWS documentation pages |
| `soundfile` / `pydub` | Audio file handling for Polly (TTS) and Transcribe (STT) |
| `psycopg2` | PostgreSQL/Aurora connection for pgvector (Day 11) |

---

## 9. Summary of Agentic Concepts

| # | Concept | Where It's Used |
|---|---------|-----------------|
| 1 | **Autonomous Agents** | Orchestrator plans & executes daily lessons without manual intervention |
| 2 | **Tool Use** | Agents call Bedrock, Titan Embeddings, Comprehend, Rekognition, Polly, Transcribe, OpenSearch, S3, file I/O |
| 3 | **RAG** | Research agent embeds AWS docs with Titan → stores in OpenSearch → retrieves for content generation |
| 4 | **Planning (ReAct)** | Orchestrator reasons step-by-step, routes to different agents based on service category |
| 5 | **Memory** | Tracks progress, BrainBot codebase state, conversation history (DynamoDB), accumulated knowledge (OpenSearch) |
| 6 | **Reflection** | Content agent self-critiques tutorials for GenAI clarity, code integration, and accuracy |
| 7 | **Multi-Agent** | 6 specialized agents (Research, Content, Code, RAG Pipeline, Multimodal, Quiz) collaborate via shared state |
| 8 | **Prompt Engineering** | Category-specific templates (AI/ML prompts differ from Storage prompts differ from Compute prompts) |
| 9 | **Adaptive Learning** | System adjusts difficulty based on quiz scores, tracked per service category |
| 10 | **State Management** | Shared state tracks BrainBot codebase, multimodal readiness, RAG pipeline stage |
| 11 | **Multimodal Processing** | Agents coordinate vision (Rekognition), voice (Polly+Transcribe), NLP (Comprehend), and reasoning (Bedrock) |
| 12 | **Incremental Code Gen** | Code agent reads existing BrainBot files and generates code that imports/extends previous days' work |

---

*This system is itself a GenAI application built on the same AWS services you're learning — the tutor uses Bedrock, Titan, OpenSearch, and Comprehend to teach you how to build BrainBot with Bedrock, Titan, OpenSearch, and Comprehend.* 🧠

---

*This system is itself a great demonstration of AWS + AI — you can deploy it on AWS using the same services you're learning!* 🧠
