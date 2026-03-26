# Building "MyAWSTutorBuddy" — The Agentic AI System

> A guide to building the AI-powered system that **generates**, **teaches**, and **adapts** the 30-day AWS learning plan automatically.

---

## 1. What Are We Building?

An **agentic AI system** — an AI that doesn't just answer questions, but **autonomously plans, researches, creates content, writes code, and adapts** to the learner's progress.

**The system will:**
1. Absorb AWS documentation and resources for one service each day
2. Generate an easy-to-understand tutorial
3. Produce incremental project code (Python + boto3)
4. Track learner progress and adapt difficulty
5. Answer follow-up questions about the day's topic

---

## 2. Agentic AI Concepts Used

### 2.1 — Agent Architecture

| Concept | What It Means | How We Use It |
|---------|---------------|---------------|
| **Autonomous Agent** | An AI that can plan and execute multi-step tasks independently | The tutor agent decides what to teach, generates content, and writes code without manual prompting each step |
| **Tool Use / Function Calling** | The agent can call external tools (APIs, search, file I/O) | Agent calls AWS docs, runs code validators, reads/writes tutorial files |
| **Planning & Reasoning** | The agent breaks a goal into sub-steps and reasons about order | Agent decomposes "teach S3" into: read docs → extract key concepts → write tutorial → generate code → create quiz |
| **Memory (Short-term & Long-term)** | The agent remembers past interactions and accumulated knowledge | Tracks what the learner has covered, their progress, and personalizes future lessons |
| **Reflection / Self-Critique** | The agent reviews its own output and improves it | After generating a tutorial, the agent checks: Is it clear? Is the code correct? Does it build on yesterday's lesson? |
| **Retrieval-Augmented Generation (RAG)** | Pulling relevant documents into context before generating answers | Agent retrieves the latest AWS docs for each service before writing the tutorial |
| **Multi-Agent Orchestration** | Multiple specialized agents collaborate on a task | Separate agents for: Research, Content Writing, Code Generation, Quiz Creation |

### 2.2 — Core Design Patterns

```
┌─────────────────────────────────────────────┐
│              ORCHESTRATOR AGENT              │
│  (Plans the day, delegates to sub-agents)   │
└──────────┬──────────┬──────────┬────────────┘
           │          │          │
     ┌─────▼───┐ ┌────▼────┐ ┌──▼──────────┐
     │RESEARCH │ │ CONTENT │ │    CODE      │
     │ AGENT   │ │ WRITER  │ │  GENERATOR   │
     │         │ │ AGENT   │ │   AGENT      │
     └─────┬───┘ └────┬────┘ └──┬───────────┘
           │          │          │
     ┌─────▼──────────▼──────────▼────────────┐
     │           SHARED MEMORY / STATE         │
     │  (Learner profile, progress, content)   │
     └────────────────────────────────────────┘
```

---

## 3. System Components — How to Build It

### 3.1 — Tech Stack

| Component | Technology |
|-----------|------------|
| Language | Python 3.11+ |
| LLM Provider | AWS Bedrock (Claude) or OpenAI API |
| Agent Framework | LangChain + LangGraph (or build from scratch) |
| Vector Store (RAG) | FAISS (local) or ChromaDB |
| Document Loader | LangChain document loaders (Web, PDF, Markdown) |
| Storage | Local filesystem + optional S3 |
| Scheduling | Python `schedule` lib or cron |
| CLI Interface | `rich` library for beautiful terminal output |

### 3.2 — Project Structure

```
MyAWSTutorBuddy/
├── agents/
│   ├── orchestrator.py        # Master agent — plans & delegates
│   ├── research_agent.py      # Fetches & processes AWS docs
│   ├── content_agent.py       # Writes tutorials
│   ├── code_agent.py          # Generates project code
│   └── quiz_agent.py          # Creates quizzes & checks understanding
├── tools/
│   ├── doc_fetcher.py         # Scrapes/loads AWS documentation
│   ├── code_validator.py      # Validates generated Python code
│   ├── file_writer.py         # Writes tutorials & code to disk
│   └── progress_tracker.py    # Tracks learner state
├── memory/
│   ├── vector_store.py        # RAG vector database management
│   ├── learner_profile.py     # Learner preferences & history
│   └── knowledge_base.py      # Accumulated AWS knowledge
├── output/
│   ├── day_01/
│   │   ├── tutorial.md        # Generated tutorial
│   │   ├── code.py            # Day's project code
│   │   └── quiz.md            # Comprehension quiz
│   ├── day_02/
│   │   └── ...
│   └── project/
│       └── cloudsnap/         # The incremental project
├── config/
│   ├── learning_plan.yaml     # 30-day plan configuration
│   └── prompts.yaml           # Agent prompt templates
├── main.py                    # Entry point — run the daily lesson
├── requirements.txt
└── README.md
```

---

## 4. How Each Agent Works

### 4.1 — Orchestrator Agent (`orchestrator.py`)

**Role:** The brain — decides what happens each day and coordinates sub-agents.

**Agentic Concept:** *Planning & Task Decomposition*

```python
# Pseudocode
class OrchestratorAgent:
    def run_daily_lesson(self, day_number):
        # 1. PLAN — Break today's goal into tasks
        service = self.get_service_for_day(day_number)
        plan = self.create_plan(service)
        # Plan: [research, write_tutorial, generate_code, create_quiz]

        # 2. DELEGATE — Send tasks to specialist agents
        docs = self.research_agent.fetch_docs(service)
        tutorial = self.content_agent.write_tutorial(service, docs)
        code = self.code_agent.generate_code(service, day_number)
        quiz = self.quiz_agent.create_quiz(service, tutorial)

        # 3. REFLECT — Review the outputs
        self.review_and_improve(tutorial, code, quiz)

        # 4. DELIVER — Save and present to learner
        self.save_outputs(day_number, tutorial, code, quiz)
```

### 4.2 — Research Agent (`research_agent.py`)

**Role:** Finds and processes AWS documentation for the day's service.

**Agentic Concepts:** *Tool Use*, *RAG (Retrieval-Augmented Generation)*

**How it works:**
1. **Fetch** — Loads AWS docs, blog posts, and tutorials for the service
2. **Chunk** — Splits documents into manageable pieces
3. **Embed** — Converts chunks into vector embeddings
4. **Store** — Saves in the vector store for the content agent to query
5. **Summarize** — Creates a condensed knowledge brief

```python
class ResearchAgent:
    def fetch_docs(self, service_name):
        # Tool: doc_fetcher
        raw_docs = self.doc_fetcher.load(service_name)

        # RAG: chunk and embed
        chunks = self.text_splitter.split(raw_docs)
        self.vector_store.add_documents(chunks)

        # Summarize key points
        summary = self.llm.summarize(raw_docs)
        return summary
```

### 4.3 — Content Writer Agent (`content_agent.py`)

**Role:** Transforms raw knowledge into a friendly, easy-to-understand tutorial.

**Agentic Concepts:** *Prompt Engineering*, *Self-Critique / Reflection*

**How it works:**
1. Receives the research summary + RAG context
2. Generates a tutorial using a structured prompt template
3. **Self-reviews**: checks clarity, accuracy, and completeness
4. Rewrites sections that don't meet quality bar

```python
class ContentAgent:
    def write_tutorial(self, service, docs):
        # RAG: retrieve most relevant chunks
        context = self.vector_store.similarity_search(service)

        # Generate tutorial with structured prompt
        tutorial = self.llm.generate(
            prompt=self.tutorial_prompt_template,
            context=context,
            service=service
        )

        # REFLECTION: Self-critique
        critique = self.llm.evaluate(tutorial, criteria=[
            "Is it beginner-friendly?",
            "Are analogies used?",
            "Is the code example correct?"
        ])

        if critique.needs_improvement:
            tutorial = self.llm.improve(tutorial, critique.feedback)

        return tutorial
```

### 4.4 — Code Generator Agent (`code_agent.py`)

**Role:** Writes the incremental project code for each day.

**Agentic Concepts:** *Tool Use (code validation)*, *Memory (builds on previous days)*

**How it works:**
1. Reads the existing project codebase (memory of what's been built)
2. Generates the new code additions for today's service
3. **Validates** the code (syntax check, import check)
4. Ensures it integrates with previous days' code

```python
class CodeAgent:
    def generate_code(self, service, day_number):
        # MEMORY: Read what's been built so far
        existing_code = self.file_reader.read_project()
        previous_services = self.progress_tracker.get_completed()

        # Generate new code that builds on existing
        new_code = self.llm.generate(
            prompt=self.code_prompt_template,
            service=service,
            existing_code=existing_code,
            day_number=day_number
        )

        # TOOL USE: Validate the code
        is_valid = self.code_validator.check(new_code)
        if not is_valid:
            new_code = self.llm.fix_code(new_code, errors)

        return new_code
```

### 4.5 — Quiz Agent (`quiz_agent.py`)

**Role:** Creates comprehension checks and interactive exercises.

**Agentic Concepts:** *Adaptive Difficulty*, *Learner Modeling*

---

## 5. Key Agentic AI Concepts — Deep Dive

### 5.1 — RAG (Retrieval-Augmented Generation)

**What:** Instead of relying solely on the LLM's training data, we fetch fresh, relevant documents and inject them into the prompt.

**Why:** AWS services change frequently. RAG ensures tutorials contain the latest information.

**Implementation:**
```
AWS Docs → Document Loader → Text Splitter → Embedding Model → Vector Store
                                                                    ↓
User Query → Embedding → Similarity Search → Top-K Chunks → LLM Prompt → Answer
```

**Python libraries:** `langchain`, `faiss-cpu`, `chromadb`, `sentence-transformers`

### 5.2 — Memory Systems

| Type | Purpose | Implementation |
|------|---------|----------------|
| **Conversation Memory** | Remember what was discussed in current session | LangChain `ConversationBufferMemory` |
| **Episodic Memory** | Remember past days' lessons and interactions | JSON/SQLite store of daily summaries |
| **Semantic Memory** | Accumulated AWS knowledge base | Vector store with all processed docs |
| **Learner Profile** | Track progress, strengths, weaknesses | YAML file with scores and preferences |

### 5.3 — Tool Use / Function Calling

The agent has access to these tools:

| Tool | Purpose |
|------|---------|
| `fetch_aws_docs(service)` | Load official AWS documentation |
| `write_file(path, content)` | Save tutorials and code to disk |
| `validate_python(code)` | Check Python code for syntax errors |
| `run_python(code)` | Execute Python code in sandbox |
| `search_knowledge_base(query)` | RAG similarity search |
| `get_learner_progress()` | Read learner's progress state |
| `update_progress(day, status)` | Mark a day as complete |

### 5.4 — Planning & Task Decomposition (ReAct Pattern)

The orchestrator uses the **ReAct** (Reasoning + Acting) pattern:

```
Thought: Today is Day 10 (DynamoDB). The learner has completed S3, Lambda, and API Gateway.
         I need to teach DynamoDB and show how to store image metadata.
Action:  Call research_agent.fetch_docs("DynamoDB")
Observation: Got 15 document chunks covering tables, items, queries, and indexes.
Thought: Good. Now I need to write a tutorial that connects DynamoDB to the existing
         Lambda + API Gateway setup from Days 8-9.
Action:  Call content_agent.write_tutorial("DynamoDB", docs, context=previous_lessons)
Observation: Tutorial generated. It uses a restaurant menu analogy for key-value pairs. Good.
Thought: Now generate the code. It should add a DynamoDB table and update the Lambda
         to store image metadata after upload.
Action:  Call code_agent.generate_code("DynamoDB", day=10)
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
        self.research_output = None
        self.tutorial_output = None
        self.code_output = None
        self.learner_profile = {}
        self.knowledge_base = VectorStore()
```

For more advanced orchestration, use **LangGraph** to define agent workflows as a directed graph with conditional edges.

### 5.6 — Reflection & Self-Improvement

After generating content, the system runs a **critic loop**:

```
Generate → Evaluate → Score → If score < threshold → Regenerate with feedback
```

Evaluation criteria:
- **Clarity:** Would a beginner understand this?
- **Accuracy:** Are AWS details correct?
- **Continuity:** Does it build on previous days?
- **Code Quality:** Does the code run? Is it idiomatic Python?

---

## 6. Implementation Roadmap

### Phase 1 — Foundation (Week 1 of development)
- [ ] Set up project structure
- [ ] Implement document fetcher tool (AWS docs loader)
- [ ] Set up vector store (FAISS/ChromaDB)
- [ ] Build basic Research Agent with RAG
- [ ] Build basic Content Agent with prompt templates
- [ ] Test with Day 1 (IAM) as proof of concept

### Phase 2 — Code Generation (Week 2 of development)
- [ ] Build Code Generator Agent
- [ ] Implement code validation tool
- [ ] Add incremental project awareness (reads existing code)
- [ ] Build Orchestrator Agent to chain all agents
- [ ] Generate Days 1-7 and review quality

### Phase 3 — Intelligence (Week 3 of development)
- [ ] Add memory system (learner profile, episodic memory)
- [ ] Implement reflection/self-critique loop
- [ ] Add Quiz Agent
- [ ] Build CLI interface with `rich`
- [ ] Add interactive Q&A mode for follow-up questions

### Phase 4 — Polish (Week 4 of development)
- [ ] Add daily scheduler (auto-generate next lesson)
- [ ] Implement adaptive difficulty
- [ ] Add progress dashboard
- [ ] Test full 30-day generation end-to-end
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
pip install boto3 langchain langchain-community langchain-aws faiss-cpu \
            chromadb rich pyyaml python-dotenv

# Set up environment variables
echo "AWS_PROFILE=default" > .env
echo "BEDROCK_MODEL_ID=anthropic.claude-3-sonnet-20240229-v1:0" >> .env

# Run your first lesson
python main.py --day 1
```

---

## 8. Key Python Libraries

| Library | Purpose |
|---------|---------|
| `boto3` | AWS SDK — interact with all AWS services |
| `langchain` | Agent framework — chains, tools, memory, RAG |
| `langgraph` | Multi-agent orchestration as graphs |
| `faiss-cpu` | Vector similarity search (local) |
| `chromadb` | Vector database (alternative to FAISS) |
| `rich` | Beautiful terminal output |
| `pyyaml` | Configuration file parsing |
| `python-dotenv` | Environment variable management |
| `sentence-transformers` | Text embeddings for RAG |
| `beautifulsoup4` | Web scraping AWS docs |

---

## 9. Summary of Agentic Concepts

| # | Concept | Where It's Used |
|---|---------|-----------------|
| 1 | **Autonomous Agents** | Orchestrator plans & executes without manual intervention |
| 2 | **Tool Use** | Agents call doc fetcher, code validator, file writer |
| 3 | **RAG** | Research agent loads AWS docs into vector store for context |
| 4 | **Planning (ReAct)** | Orchestrator reasons step-by-step before acting |
| 5 | **Memory** | Tracks progress, past lessons, accumulated knowledge |
| 6 | **Reflection** | Content agent self-critiques and improves tutorials |
| 7 | **Multi-Agent** | Specialized agents (research, content, code, quiz) collaborate |
| 8 | **Prompt Engineering** | Structured templates for each agent's task |
| 9 | **Adaptive Learning** | System adjusts difficulty based on quiz scores |
| 10 | **State Management** | Shared state keeps all agents synchronized |

---

*This system is itself a great demonstration of AWS + AI — you can deploy it on AWS using the same services you're learning!* 🧠
