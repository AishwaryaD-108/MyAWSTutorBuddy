# 30-Day AWS GenAI Learning Plan — Python Edition

> **Goal:** Go from zero to building production-ready **Generative AI applications** on AWS in 30 days.
> Each day you learn **one AWS service**, read a bite-sized tutorial, and add a small piece of Python code to an **incremental project** that grows from a simple chatbot into a full-stack, multimodal AI assistant.
>
> **Priority order:** AI/ML services first → Storage for RAG → Serverless compute → Integration → Security → Data pipelines → Frontend.

---

## The Incremental Project — "BrainBot"

**BrainBot** is a **multimodal AI knowledge assistant** — a RAG-powered chatbot that can:

- **Read** documents you upload (PDFs, text files) and answer questions about them
- **See** images you share and describe/analyze them
- **Listen** to your voice and respond with speech
- **Search** an enterprise knowledge base intelligently
- **Learn** from new data you feed it through automated pipelines

Think of it as building your own mini-Alexa that's powered by LLMs and actually understands your documents.

**By Day 30 you will have built:**
- A RAG chatbot powered by Bedrock (Claude/Titan)
- Document ingestion → vector embedding → semantic search pipeline
- Image + voice understanding (multimodal)
- A conversational voice assistant (Lex + Polly + Transcribe)
- Automated data processing pipelines (Glue + Athena)
- A deployed web app with Amplify + CloudFront

---

## Service Categories at a Glance

| Category | Services | Days |
|----------|----------|------|
| 🤖 **AI & ML** | Bedrock, Titan, Rekognition, Comprehend, Lex, Polly, Transcribe, SageMaker | 1–8 |
| 💾 **Data Storage for RAG** | S3, DynamoDB, Aurora, OpenSearch, Kendra | 9–13 |
| ⚡ **Compute & Serverless** | Lambda, EC2, ECS, Fargate | 14–17 |
| 🔗 **Integration & Events** | API Gateway, SQS, SNS, EventBridge, Step Functions | 18–22 |
| 🔐 **Security & Access** | IAM, KMS, Secrets Manager | 23–25 |
| 📊 **Data Pipelines** | Glue, Athena, Redshift | 26–28 |
| 🌐 **Frontend & Delivery** | CloudFront, Amplify | 29–30 |

---

## Week 1 — AI & ML Services (The Brains)

> *This week you build the intelligence layer. By the end, BrainBot can think, see, read, listen, and speak.*

### Day 1 — Amazon Bedrock (Foundation Models)
| | |
|---|---|
| **What it is** | AWS's gateway to powerful LLMs (Claude, Titan, Llama, etc.) — use best-in-class AI models without training anything yourself. Like ordering from a menu of the world's smartest AIs. |
| **Core concepts** | Foundation Models, Model IDs, InvokeModel API, Prompts, Temperature, Tokens, Streaming. |
| **Main usage** | Text generation, summarization, Q&A, chatbots, code generation — the brain of any GenAI app. |
| **Why first** | 🔥 **Highest ROI** — this is the core of everything you'll build. Every other service feeds into or out of Bedrock. |
| **Project step** | Set up Bedrock access. Write a Python script that sends a prompt to Claude via Bedrock and gets a response. Build a simple CLI chatbot with conversation memory. |
| **Code focus** | `bedrock_runtime.invoke_model()`, `invoke_model_with_response_stream()`, JSON payloads, prompt templates |

### Day 2 — Amazon Titan (Embeddings & Native Models)
| | |
|---|---|
| **What it is** | AWS's own family of AI models — includes text generation AND text embeddings (turning words into numbers for search). Think of embeddings as giving every sentence a GPS coordinate so you can find similar ones. |
| **Core concepts** | Titan Text, Titan Embeddings, Vector Representations, Embedding Dimensions, Similarity. |
| **Main usage** | Generating text embeddings for RAG/vector search, text generation as an alternative to Claude. |
| **Why today** | You need embeddings to build RAG (the killer feature of GenAI apps). Titan Embeddings is the most cost-effective way to do it on AWS. |
| **Project step** | Use Titan Embeddings to convert text chunks into vectors. Build a local similarity search — given a query, find the most relevant text chunk from a small document set. |
| **Code focus** | `bedrock_runtime.invoke_model()` with Titan Embed model, `numpy` cosine similarity, vector storage |

### Day 3 — Amazon Rekognition (Image AI)
| | |
|---|---|
| **What it is** | AWS's eyes — an AI that can detect objects, faces, text, scenes, and even inappropriate content in images. Like giving your app the ability to see. |
| **Core concepts** | DetectLabels, DetectFaces, DetectText, RecognizeCelebrities, Content Moderation. |
| **Main usage** | Image analysis, content moderation, visual search, OCR from images. |
| **Why today** | Makes BrainBot multimodal — it can now understand images, not just text. |
| **Project step** | Add image understanding to BrainBot. Upload an image → Rekognition detects labels → Bedrock generates a natural language description of the image. |
| **Code focus** | `rekognition.detect_labels()`, `rekognition.detect_text()`, piping results to Bedrock |

### Day 4 — Amazon Comprehend (NLP)
| | |
|---|---|
| **What it is** | AWS's reading comprehension — understands the meaning, sentiment, entities, and language of text. Like hiring an analyst to read all your documents. |
| **Core concepts** | Sentiment Analysis, Entity Recognition (NER), Key Phrases, Language Detection, PII Detection. |
| **Main usage** | Analyzing customer feedback, extracting entities from documents, detecting PII, content classification. |
| **Why today** | BrainBot can now understand the *tone* and *key information* in documents before answering questions about them. |
| **Project step** | Before storing documents, run them through Comprehend to extract entities and sentiment. Tag and enrich the document metadata. Add a "summarize sentiment" feature to BrainBot. |
| **Code focus** | `comprehend.detect_sentiment()`, `comprehend.detect_entities()`, `comprehend.detect_key_phrases()` |

### Day 5 — Amazon Lex (Conversational AI)
| | |
|---|---|
| **What it is** | The same tech behind Alexa — builds conversational chatbots with structured intents, slots, and dialog management. Like giving your bot a conversation script with flexibility. |
| **Core concepts** | Bots, Intents, Slots, Utterances, Fulfillment, Session Management. |
| **Main usage** | Customer service bots, order-taking, FAQ bots, voice assistants. |
| **Why today** | Lex adds structured conversation flow to BrainBot — handling specific intents (upload doc, ask question, analyze image) before falling back to free-form Bedrock chat. |
| **Project step** | Create a Lex bot with intents: `AskQuestion`, `UploadDocument`, `AnalyzeImage`, `GetSummary`. Wire fulfillment to call your existing Bedrock/Rekognition logic. |
| **Code focus** | `lexv2.create_bot()`, `create_intent()`, `recognize_text()`, fulfillment Lambda |

### Day 6 — Amazon Polly (Text-to-Speech)
| | |
|---|---|
| **What it is** | Turns text into lifelike speech — your app can literally talk to users with natural-sounding voices. Like hiring a voice actor that works instantly. |
| **Core concepts** | Voices (Neural/Standard), SSML (Speech Synthesis Markup), Output Formats (MP3, OGG), Lexicons. |
| **Main usage** | Voice assistants, accessibility, audio content generation, reading documents aloud. |
| **Why today** | BrainBot can now *speak* its answers — turning it from a text chatbot into a voice assistant. |
| **Project step** | After BrainBot generates a text response, pipe it through Polly to create an audio file. Play the response. Add SSML for natural pauses and emphasis. |
| **Code focus** | `polly.synthesize_speech()`, SSML tags, saving/playing MP3 output |

### Day 7 — Amazon Transcribe (Speech-to-Text)
| | |
|---|---|
| **What it is** | The reverse of Polly — converts audio/speech into text. Like having a perfect note-taker that never misses a word. |
| **Core concepts** | Transcription Jobs, Real-time Streaming, Custom Vocabularies, Speaker Identification. |
| **Main usage** | Meeting transcription, voice input, call center analytics, subtitle generation. |
| **Why today** | BrainBot can now *listen* — users speak into a mic, Transcribe converts to text, then Bedrock answers, then Polly speaks back. Full voice loop! |
| **Project step** | Add voice input to BrainBot. Record audio → Transcribe to text → Send to Bedrock → Get response → Polly speaks it. Also: transcribe a meeting recording and summarize it with Bedrock. |
| **Code focus** | `transcribe.start_transcription_job()`, streaming transcription, audio file handling |

### Day 8 — Amazon SageMaker (ML Platform)
| | |
|---|---|
| **What it is** | AWS's full ML workshop — build, train, and deploy custom machine learning models. Like having a complete data science lab in the cloud. |
| **Core concepts** | Notebooks, Training Jobs, Endpoints, Model Registry, Pipelines, Feature Store. |
| **Main usage** | Custom model training, fine-tuning, ML experiments, deploying models at scale. |
| **Why today** | Understand when you'd go beyond Bedrock — for custom models, fine-tuning, or specialized ML tasks. |
| **Project step** | Launch a SageMaker notebook. Fine-tune a small text classification model on sample data. Deploy it as an endpoint. Compare calling SageMaker endpoint vs. Bedrock for a classification task. |
| **Code focus** | `sagemaker.create_notebook_instance()`, `Estimator.fit()`, `predictor.predict()` |

---

## Week 2 — Data Storage for RAG (The Memory)

> *This week you build the knowledge layer. BrainBot gets a long-term memory — documents, databases, and search indexes that make RAG work.*

### Day 9 — Amazon S3 (Object Storage)
| | |
|---|---|
| **What it is** | Infinite object storage — your giant cloud hard drive. Every file, document, image, and model artifact lives here. |
| **Core concepts** | Buckets, Objects, Keys, Versioning, Lifecycle Policies, Event Notifications. |
| **Main usage** | Storing documents for RAG, model artifacts, images, training data, static website files. |
| **Why today** | 🔥 **High ROI** — S3 is the foundation of your data pipeline. Every document users upload to BrainBot starts here. |
| **Project step** | Create buckets: `brainbot-documents` (raw uploads), `brainbot-processed` (chunked + embedded docs). Write Python to upload PDFs/text docs and trigger processing on upload. |
| **Code focus** | `s3.create_bucket()`, `s3.upload_file()`, `s3.list_objects_v2()`, S3 Event Notifications |

### Day 10 — Amazon DynamoDB (NoSQL Database)
| | |
|---|---|
| **What it is** | A lightning-fast NoSQL database — stores data as key-value pairs with single-digit millisecond reads. Like a super-fast filing cabinet with instant lookup. |
| **Core concepts** | Tables, Items, Partition Key, Sort Key, GSI (Global Secondary Index), TTL. |
| **Main usage** | Chat history, session state, user profiles, document metadata — anything needing fast reads/writes. |
| **Why today** | BrainBot needs to remember conversations and track document metadata. DynamoDB is perfect for this. |
| **Project step** | Create tables: `conversations` (chat history) and `documents` (uploaded doc metadata + processing status). Store BrainBot's conversation history so it can reference past messages. |
| **Code focus** | `dynamodb.create_table()`, `table.put_item()`, `table.query()`, conversation memory pattern |

### Day 11 — Amazon Aurora (Relational Database)
| | |
|---|---|
| **What it is** | AWS's supercharged relational database — MySQL/PostgreSQL compatible but 5x faster. Now also supports **vector storage** with pgvector! |
| **Core concepts** | Clusters, Instances, Reader/Writer Endpoints, Serverless v2, pgvector extension. |
| **Main usage** | Structured data storage, and with pgvector — a vector database for RAG embeddings right inside PostgreSQL. |
| **Why today** | Aurora + pgvector = you can store both structured data AND vector embeddings in one place. Great for RAG. |
| **Project step** | Create an Aurora Serverless PostgreSQL cluster. Enable pgvector. Store document embeddings. Write a Python similarity search function that queries Aurora for the most relevant chunks. |
| **Code focus** | `rds.create_db_cluster()` (Aurora), `psycopg2`, pgvector SQL queries, `SELECT ... ORDER BY embedding <=> query_vec` |

### Day 12 — Amazon OpenSearch Service (Search & Vector DB)
| | |
|---|---|
| **What it is** | A managed search engine that now supports **vector search** — find documents by meaning, not just keywords. Like upgrading from a dictionary to a mind-reader. |
| **Core concepts** | Domains, Indexes, k-NN (vector search), Hybrid Search (keyword + semantic), Mappings. |
| **Main usage** | 🔥 **Vector search for RAG** — the most popular way to store and retrieve embeddings on AWS. Also: log analytics, full-text search. |
| **Why today** | OpenSearch is the go-to vector database for production RAG on AWS. This is where BrainBot's knowledge lives. |
| **Project step** | Create an OpenSearch domain with k-NN enabled. Index document embeddings (from Day 2's Titan work). Build the full RAG pipeline: query → embed → search OpenSearch → inject context → Bedrock generates answer. |
| **Code focus** | `opensearch.create_domain()`, `opensearch-py` client, k-NN index mapping, hybrid search queries |

### Day 13 — Amazon Kendra (Intelligent Search)
| | |
|---|---|
| **What it is** | An AI-powered enterprise search engine — understands natural language questions and finds answers in your documents. Like having a librarian who's read every document in your company. |
| **Core concepts** | Indexes, Data Sources, FAQs, Document Enrichment, Relevance Tuning. |
| **Main usage** | Enterprise document search, FAQ systems, knowledge management — a higher-level alternative to OpenSearch for RAG. |
| **Why today** | Kendra provides an out-of-the-box RAG experience. Compare it with the OpenSearch approach from yesterday. |
| **Project step** | Create a Kendra index. Sync the S3 documents bucket as a data source. Query Kendra with natural language and feed results to Bedrock. Compare answer quality vs. OpenSearch RAG. |
| **Code focus** | `kendra.create_index()`, `kendra.batch_put_document()`, `kendra.query()`, Kendra + Bedrock integration |

---

## Week 3 — Compute, Serverless & Integration (The Backbone)

> *This week you build the infrastructure that makes BrainBot a real application — APIs, queues, event-driven processing, and orchestration.*

### Day 14 — AWS Lambda (Serverless Functions)
| | |
|---|---|
| **What it is** | Run code without managing servers — upload a function, AWS runs it when needed and charges you per millisecond. Like having an army of workers that appear on demand. |
| **Core concepts** | Functions, Triggers, Layers, Execution Roles, Cold Starts, Timeout, Memory. |
| **Main usage** | 🔥 **High ROI** — the backend logic for everything. Every API call, every event, every processing step runs as a Lambda. |
| **Why today** | Time to move BrainBot from local scripts to the cloud. Each capability becomes a Lambda function. |
| **Project step** | Create Lambda functions: `process_query` (Bedrock chat), `process_document` (S3 → chunk → embed → OpenSearch), `analyze_image` (Rekognition → Bedrock). Deploy and test each. |
| **Code focus** | `lambda_client.create_function()`, handler pattern, layers for dependencies, S3 triggers |

### Day 15 — Amazon EC2 (Virtual Servers)
| | |
|---|---|
| **What it is** | Virtual machines in the cloud — your own servers on-demand. Full control over the OS, networking, and software. |
| **Core concepts** | Instances, AMIs, Instance Types (GPU!), Security Groups, Key Pairs, User Data. |
| **Main usage** | Running custom ML inference (GPU instances), hosting services that need persistent connections, dev environments. |
| **Why today** | Understand when you'd use EC2 over Lambda — for GPU workloads, long-running processes, or hosting OpenSearch/model servers. |
| **Project step** | Launch a GPU-capable instance. Install Python + ML libraries. Run a local model inference to compare speed vs. Bedrock. Set up a self-hosted document processing server. |
| **Code focus** | `ec2.run_instances()`, `ec2.describe_instances()`, user data scripts, SSH + remote execution |

### Day 16 — Amazon ECS (Container Service)
| | |
|---|---|
| **What it is** | Run Docker containers on AWS — package your code + dependencies into containers and let AWS manage them. |
| **Core concepts** | Clusters, Tasks, Services, Task Definitions, Container Images, Fargate vs EC2 launch type. |
| **Main usage** | Running containerized microservices, batch processing workers, long-running API servers. |
| **Why today** | Some BrainBot tasks (document processing, heavy ML inference) need more than Lambda's 15-min timeout. |
| **Project step** | Containerize the document processing pipeline (chunking + embedding). Create a Dockerfile, build the image, push to ECR, deploy as an ECS service. |
| **Code focus** | `ecs.create_cluster()`, `ecs.register_task_definition()`, Dockerfile, `docker build` + `docker push` |

### Day 17 — AWS Fargate (Serverless Containers)
| | |
|---|---|
| **What it is** | Serverless Docker — run containers without managing servers. Combines the flexibility of containers with the simplicity of serverless. |
| **Core concepts** | Fargate launch type, vCPU/Memory config, Task networking, Auto-scaling. |
| **Main usage** | Running containers when you don't want to manage EC2 instances underneath. |
| **Why today** | Fargate is the preferred way to run BrainBot's container workloads — no server management needed. |
| **Project step** | Migrate the ECS service from Day 16 to Fargate launch type. Configure auto-scaling based on SQS queue depth (more documents = more containers). |
| **Code focus** | Fargate task definition, `ecs.create_service()` with Fargate, auto-scaling policies |

### Day 18 — Amazon API Gateway (Expose AI APIs)
| | |
|---|---|
| **What it is** | The front door to your backend — creates REST/HTTP APIs that the world (or your frontend) can call. |
| **Core concepts** | REST APIs, HTTP APIs, Resources, Methods, Stages, CORS, Usage Plans, API Keys. |
| **Main usage** | 🔥 **High ROI** — exposing Lambda functions as HTTP endpoints. Every BrainBot feature becomes an API call. |
| **Why today** | Turn BrainBot into a real service. Anyone can call your AI via HTTP. |
| **Project step** | Create a REST API with endpoints: `POST /chat` (Bedrock), `POST /upload` (document ingestion), `POST /analyze-image` (Rekognition), `GET /search` (OpenSearch RAG). Wire each to its Lambda. |
| **Code focus** | `apigateway.create_rest_api()`, `put_method()`, `put_integration()`, CORS setup |

### Day 19 — Amazon SQS (Message Queues)
| | |
|---|---|
| **What it is** | A message waiting line — tasks queue up and workers process them one by one. Prevents your system from being overwhelmed. |
| **Core concepts** | Standard vs FIFO Queues, Visibility Timeout, Dead-Letter Queues, Long Polling. |
| **Main usage** | Decoupling services, buffering requests, reliable async processing. |
| **Why today** | Document processing shouldn't block the API. Users upload → SQS queues the work → Lambda/Fargate processes in the background. |
| **Project step** | Add an SQS queue between document upload and processing. `POST /upload` → S3 → SQS → Lambda processes document → stores in OpenSearch. Add a dead-letter queue for failures. |
| **Code focus** | `sqs.create_queue()`, `sqs.send_message()`, `sqs.receive_message()`, Lambda SQS trigger |

### Day 20 — Amazon SNS (Notifications)
| | |
|---|---|
| **What it is** | AWS's megaphone — sends messages to many subscribers at once (email, SMS, Lambda, HTTP, etc.). |
| **Core concepts** | Topics, Subscriptions, Message Filtering, Fan-out pattern. |
| **Main usage** | Alerts, notifications, fan-out (one event triggers many actions). |
| **Why today** | Notify users when their document finishes processing. Alert admins on errors. Fan-out events to multiple services. |
| **Project step** | Create an SNS topic `document-processed`. When processing completes, publish a message. Subscribe: email notification + Lambda that updates DynamoDB status. |
| **Code focus** | `sns.create_topic()`, `sns.subscribe()`, `sns.publish()`, message filtering |

### Day 21 — Amazon EventBridge (Event Router)
| | |
|---|---|
| **What it is** | A smart event bus — listens for events from AWS services and your apps, then routes them to targets based on rules. |
| **Core concepts** | Event Bus, Rules, Event Patterns, Targets, Schedules (cron). |
| **Main usage** | Event-driven architectures, scheduled tasks, cross-service integration. |
| **Why today** | Automate BrainBot's maintenance — schedule re-indexing, nightly analytics, knowledge base refresh. |
| **Project step** | Create rules: (1) Nightly cron to re-embed any updated documents, (2) Route Bedrock invocation events to a logging Lambda, (3) When a new S3 object arrives, trigger the ingestion pipeline. |
| **Code focus** | `events.put_rule()`, `events.put_targets()`, schedule expressions, custom event patterns |

### Day 22 — AWS Step Functions (Workflow Orchestration)
| | |
|---|---|
| **What it is** | A visual workflow engine — chains Lambda functions into a state machine with branching, retries, parallel steps, and error handling. The conductor of your orchestra. |
| **Core concepts** | State Machines, States (Task, Choice, Parallel, Wait, Map), ASL (Amazon States Language), Error Handling. |
| **Main usage** | Orchestrating complex multi-step processes — exactly what a RAG document pipeline needs. |
| **Why today** | The document ingestion pipeline (upload → extract text → chunk → embed → index → notify) is a perfect Step Functions use case. |
| **Project step** | Build a state machine for document ingestion: S3 Upload → Extract Text (Lambda) → Detect Language (Comprehend) → Chunk Text (Lambda) → Generate Embeddings (Titan) → Index in OpenSearch (Lambda) → Notify (SNS). Add error handling and retries. |
| **Code focus** | `stepfunctions.create_state_machine()`, ASL JSON, `Choice` states for branching, `Parallel` for speed |

---

## Week 4 — Security, Data Pipelines & Frontend (Production-Ready)

> *This week you harden, analyze, and ship. BrainBot becomes a secure, data-driven, deployed application.*

### Day 23 — AWS IAM (Identity & Access Management)
| | |
|---|---|
| **What it is** | AWS's security guard — controls *who* can do *what* on your account. Every API call is checked against IAM policies. |
| **Core concepts** | Users, Roles, Policies (JSON), Least Privilege, Service Roles, Conditions. |
| **Main usage** | Securing every AWS service. Lambda needs a role to call Bedrock. API Gateway needs a role to invoke Lambda. Everything chains. |
| **Why today** | Lock down BrainBot. Create dedicated roles for each Lambda with only the permissions they need — no more, no less. |
| **Project step** | Audit and create least-privilege IAM roles: `brainbot-chat-role` (Bedrock only), `brainbot-ingest-role` (S3 + OpenSearch + Comprehend), `brainbot-image-role` (Rekognition + S3). Write Python to manage policies. |
| **Code focus** | `iam.create_role()`, `iam.put_role_policy()`, policy JSON documents, `sts.assume_role()` |

### Day 24 — AWS KMS (Key Management Service)
| | |
|---|---|
| **What it is** | AWS's locksmith — creates and manages encryption keys. Protects your data at rest and in transit. |
| **Core concepts** | KMS Keys (CMKs), Encrypt/Decrypt, Envelope Encryption, Key Policies, Automatic Rotation. |
| **Main usage** | Encrypting S3 buckets, DynamoDB tables, SQS messages, secrets — everything sensitive. |
| **Why today** | Encrypt BrainBot's data. User documents and conversation history must be protected. |
| **Project step** | Create a KMS key for BrainBot. Enable encryption on S3 buckets, DynamoDB tables, and SQS queues. Write Python to encrypt/decrypt sensitive data before storage. |
| **Code focus** | `kms.create_key()`, `kms.encrypt()`, `kms.decrypt()`, S3 SSE-KMS configuration |

### Day 25 — AWS Secrets Manager
| | |
|---|---|
| **What it is** | A secure vault for passwords, API keys, and tokens — stores, rotates, and retrieves secrets so you never hardcode them. |
| **Core concepts** | Secrets, Versions, Rotation, Resource Policies, Cross-account access. |
| **Main usage** | Storing database credentials, API keys, OAuth tokens — anything you'd never put in code. |
| **Why today** | Store Aurora DB credentials, API keys, and any external service tokens securely. Lambdas fetch secrets at runtime. |
| **Project step** | Store BrainBot's Aurora credentials and any API keys in Secrets Manager. Update all Lambdas to fetch secrets using `get_secret_value()` instead of environment variables. Add automatic rotation. |
| **Code focus** | `secretsmanager.create_secret()`, `secretsmanager.get_secret_value()`, rotation Lambda |

### Day 26 — AWS Glue (ETL & Data Catalog)
| | |
|---|---|
| **What it is** | A serverless ETL (Extract, Transform, Load) service — moves, cleans, and transforms data between storage systems. Also catalogs all your data so tools like Athena can query it. |
| **Core concepts** | Crawlers, Data Catalog, ETL Jobs (Spark), Schema Discovery, Classifiers. |
| **Main usage** | Transforming raw data into queryable formats, building data lakes, catalog discovery. |
| **Why today** | Process BrainBot's raw interaction logs into structured analytics data. Crawl the S3 document bucket to maintain a data catalog. |
| **Project step** | Create a Glue Crawler on the S3 documents bucket. Run an ETL job to transform raw chat logs (JSON in S3) into clean Parquet for analytics. Register tables in the Data Catalog. |
| **Code focus** | `glue.create_crawler()`, `glue.create_job()`, PySpark transformation script, `glue.start_crawler()` |

### Day 27 — Amazon Athena (Serverless SQL)
| | |
|---|---|
| **What it is** | Run SQL queries directly on data in S3 — no database setup needed. Like giving S3 a SQL brain. Pay per query. |
| **Core concepts** | Databases, Tables (from Glue Catalog), SQL queries, Partitions, Output location. |
| **Main usage** | Ad-hoc analytics on log files, data exploration, cost-effective querying of data lakes. |
| **Why today** | Analyze BrainBot's usage — most asked topics, average response times, popular documents, error rates — all with SQL. |
| **Project step** | Query the Glue-cataloged data. Write SQL to answer: "What are the top 10 questions users ask?", "Which documents are most referenced?", "What's the average response time per model?". Build an analytics dashboard. |
| **Code focus** | `athena.start_query_execution()`, `athena.get_query_results()`, SQL on S3 data |

### Day 28 — Amazon Redshift (Data Warehouse)
| | |
|---|---|
| **What it is** | A petabyte-scale data warehouse — for heavy analytics when Athena's not enough. Like Athena's bigger, faster sibling for huge datasets. |
| **Core concepts** | Clusters, Redshift Serverless, Schemas, COPY command, Distribution/Sort Keys, Spectrum. |
| **Main usage** | Large-scale analytics, BI dashboards, historical trend analysis, data warehousing. |
| **Why today** | Understand when Redshift > Athena. Load historical BrainBot data into Redshift Serverless for complex analytical queries. |
| **Project step** | Create a Redshift Serverless workgroup. COPY BrainBot analytics data from S3. Write complex analytical queries (usage trends over time, user cohort analysis). Compare performance vs. Athena. |
| **Code focus** | `redshift_serverless.create_workgroup()`, `redshift_data.execute_statement()`, `COPY` command, analytical SQL |

### Day 29 — Amazon CloudFront (CDN & Delivery)
| | |
|---|---|
| **What it is** | A global content delivery network — caches your content at 400+ edge locations worldwide for lightning-fast delivery. |
| **Core concepts** | Distributions, Origins, Edge Locations, Cache Behaviors, Invalidations, OAC (Origin Access Control). |
| **Main usage** | Serving static frontends, caching API responses, low-latency global delivery of any content. |
| **Why today** | BrainBot's frontend and API need to be fast for users everywhere. CloudFront makes it snappy. |
| **Project step** | Create a CloudFront distribution. Put it in front of S3 (for the static frontend) AND API Gateway (for the chat API). Configure caching for static assets but pass-through for API calls. |
| **Code focus** | `cloudfront.create_distribution()`, origin config (S3 + API GW), cache policies, OAC for S3 |

### Day 30 — AWS Amplify (Full-Stack Deployment) 🎉
| | |
|---|---|
| **What it is** | AWS's fastest way to deploy full-stack web apps — handles hosting, CI/CD, auth, and APIs out of the box. Like Vercel/Netlify but deeply integrated with AWS. |
| **Core concepts** | Amplify Hosting, Git-based deployments, Environment Variables, Custom Domains, Build Settings. |
| **Main usage** | Deploying web frontends, one-click CI/CD from Git, rapid prototyping of full-stack apps. |
| **Why today** | Ship it! Deploy BrainBot's web interface to the world. |
| **Project step** | Create a simple chat UI (HTML/JS or React). Deploy via Amplify connected to your Git repo. Wire the frontend to your API Gateway endpoints. BrainBot is now live on the internet! Final end-to-end test of the complete pipeline. |
| **Code focus** | Amplify CLI/Console, `amplify.create_app()`, frontend code, API integration, environment config |

---

## BrainBot — Final Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                        FRONTEND                               │
│  Amplify (Web UI) → CloudFront (CDN) → API Gateway (REST)    │
└─────────────────────────┬────────────────────────────────────┘
                          │
         ┌────────────────┼────────────────┐
         ▼                ▼                ▼
   POST /chat      POST /upload     POST /analyze-image
         │                │                │
         ▼                ▼                ▼
   ┌─────────┐    ┌──────────────┐   ┌───────────┐
   │ Lambda:  │    │ S3 → SQS →   │   │ Lambda:   │
   │ Chat     │    │ Step Function │   │ Image     │
   │          │    │               │   │           │
   │ Bedrock  │    │ ┌───────────┐│   │Rekognition│
   │ (Claude/ │    │ │Extract Txt││   │    +      │
   │  Titan)  │    │ │Comprehend ││   │ Bedrock   │
   │    +     │    │ │Chunk      ││   └───────────┘
   │OpenSearch│    │ │Titan Embed││
   │  (RAG)   │    │ │OpenSearch ││
   │    +     │    │ │SNS Notify ││
   │ DynamoDB │    │ └───────────┘│
   │ (Memory) │    └──────────────┘
   └─────────┘
         │               │                │
         ▼               ▼                ▼
   ┌──────────────────────────────────────────┐
   │              DATA LAYER                   │
   │  S3 (Documents) │ DynamoDB (Chat History) │
   │  Aurora+pgvector │ OpenSearch (Vectors)    │
   │  Kendra (Enterprise Search)               │
   └──────────────────────────────────────────┘
         │
         ▼
   ┌──────────────────────────────────────────┐
   │           ANALYTICS LAYER                 │
   │  Glue (ETL) → Athena (SQL) → Redshift    │
   └──────────────────────────────────────────┘
         │
         ▼
   ┌──────────────────────────────────────────┐
   │            SECURITY LAYER                 │
   │  IAM (Roles) │ KMS (Encryption)           │
   │  Secrets Manager (Credentials)            │
   └──────────────────────────────────────────┘
         │
         ▼
   ┌──────────────────────────────────────────┐
   │           VOICE LAYER (Optional)          │
   │  Lex (Conversation) + Transcribe (STT)    │
   │  + Polly (TTS) = Full Voice Assistant     │
   └──────────────────────────────────────────┘
```

---

## High-ROI Priority Guide

If time is limited, focus on these 10 services first (they cover 80% of any GenAI app):

| Priority | Service | Why |
|----------|---------|-----|
| 🥇 1 | **Bedrock** | The brain — LLMs + GenAI |
| 🥇 2 | **S3** | The storage — all data lives here |
| 🥇 3 | **Lambda** | The backend — runs all logic |
| 🥇 4 | **API Gateway** | The API — exposes everything |
| 🥇 5 | **OpenSearch** | The memory — vector search for RAG |
| 🥈 6 | **Titan** | The embeddings — makes RAG work |
| 🥈 7 | **DynamoDB** | The state — chat history + metadata |
| 🥈 8 | **Step Functions** | The orchestrator — chains everything |
| 🥈 9 | **IAM** | The security — locks it all down |
| 🥉 10 | **Comprehend** | The understanding — enriches data |

---

## Daily Routine

| Step | Time | Activity |
|------|------|----------|
| 1 | 15 min | Read the day's tutorial/concepts |
| 2 | 15 min | Explore the service in the AWS Console |
| 3 | 30 min | Write the day's Python code |
| 4 | 10 min | Test & verify it works |
| 5 | 5 min | Commit code to git |
| 6 | 5 min | Review what you learned |

**Total: ~80 minutes/day**

---

## Prerequisites

- Python 3.9+ installed
- AWS account (Free Tier eligible — ⚠️ some AI services have limited free tier, budget ~$20-50/month)
- `boto3` installed (`pip install boto3`)
- AWS CLI configured (`aws configure`)
- **Bedrock model access** enabled in the AWS Console (must request access to Claude/Titan)
- A code editor (VS Code recommended)
- Git for version control

---

*One service a day. By Day 30, you'll have a production-ready GenAI application in your portfolio.* 🚀
