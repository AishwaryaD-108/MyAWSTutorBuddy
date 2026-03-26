# 30-Day AWS Learning Plan — Python Edition

> **Goal:** Go from zero to confident with core AWS services in 30 days.
> Each day you learn **one AWS service**, read a bite-sized tutorial, and add a small piece of Python code to an **incremental project** that grows from a simple S3 file uploader into a full-stack serverless AI-powered web application.

---

## The Incremental Project — "CloudSnap"

**CloudSnap** is an AI-powered image analysis platform where users upload images, the system stores them, processes them with AI, serves results via an API, and delivers a fast front-end — all on AWS with Python.

By Day 30 you will have built:
- A serverless REST API
- Image upload & storage pipeline
- AI-powered image analysis (Rekognition + Bedrock)
- Real-time notifications
- CI/CD pipeline
- Monitoring & observability dashboard

---

## Week 1 — Foundations (Identity, Compute, Storage, Networking)

### Day 1 — IAM (Identity & Access Management)
| | |
|---|---|
| **What it is** | AWS's bouncer — controls *who* can do *what* on your account. |
| **Core concepts** | Users, Groups, Roles, Policies (JSON permission docs). |
| **Main usage** | Granting least-privilege access to every other AWS service. |
| **Project step** | Create a dedicated IAM user & role for CloudSnap with programmatic access. Write a Python script using `boto3` to list IAM users and attach a policy. |
| **Code focus** | `boto3.client('iam')`, `create_user()`, `attach_user_policy()` |

### Day 2 — S3 (Simple Storage Service)
| | |
|---|---|
| **What it is** | Infinite object storage — think of it as a giant cloud hard drive. |
| **Core concepts** | Buckets, Objects, Keys, Versioning, Bucket Policies. |
| **Main usage** | Storing files (images, backups, static assets, data lakes). |
| **Project step** | Create the `cloudsnap-uploads` bucket. Write Python to upload, list, and download images. |
| **Code focus** | `s3.create_bucket()`, `s3.upload_file()`, `s3.list_objects_v2()` |

### Day 3 — EC2 (Elastic Compute Cloud)
| | |
|---|---|
| **What it is** | Virtual servers in the cloud — your own computers on-demand. |
| **Core concepts** | Instances, AMIs, Instance Types, Security Groups, Key Pairs. |
| **Main usage** | Running applications, APIs, dev environments — any general compute. |
| **Project step** | Launch a t2.micro instance with a Security Group. Write Python to start/stop/describe instances. |
| **Code focus** | `ec2.run_instances()`, `ec2.describe_instances()`, `ec2.stop_instances()` |

### Day 4 — VPC (Virtual Private Cloud)
| | |
|---|---|
| **What it is** | Your own private network inside AWS — like building walls and doors around your cloud resources. |
| **Core concepts** | Subnets (public/private), Route Tables, Internet Gateway, NAT Gateway. |
| **Main usage** | Network isolation, controlling traffic flow between services. |
| **Project step** | Create a VPC with public + private subnets for CloudSnap. Python script to create and describe VPC components. |
| **Code focus** | `ec2.create_vpc()`, `ec2.create_subnet()`, `ec2.create_internet_gateway()` |

### Day 5 — Route 53 (DNS Service)
| | |
|---|---|
| **What it is** | AWS's phone book — translates domain names (myapp.com) to IP addresses. |
| **Core concepts** | Hosted Zones, Record Sets (A, CNAME, Alias), Routing Policies. |
| **Main usage** | Domain registration, DNS routing, health checks. |
| **Project step** | Create a hosted zone for CloudSnap. Python script to manage DNS records. |
| **Code focus** | `route53.create_hosted_zone()`, `route53.change_resource_record_sets()` |

### Day 6 — CloudWatch (Monitoring & Logging)
| | |
|---|---|
| **What it is** | AWS's security camera system — watches everything and alerts you when things go wrong. |
| **Core concepts** | Metrics, Alarms, Logs, Log Groups, Dashboards. |
| **Main usage** | Monitoring resource health, setting alarms, centralized logging. |
| **Project step** | Set up CloudWatch alarms for the EC2 instance. Python script to push custom metrics and create alarms. |
| **Code focus** | `cloudwatch.put_metric_data()`, `cloudwatch.put_metric_alarm()` |

### Day 7 — KMS (Key Management Service)
| | |
|---|---|
| **What it is** | AWS's locksmith — creates and manages encryption keys to protect your data. |
| **Core concepts** | CMKs (Customer Master Keys), Encryption/Decryption, Key Policies. |
| **Main usage** | Encrypting S3 objects, database data, secrets at rest. |
| **Project step** | Create a KMS key. Encrypt the S3 bucket for CloudSnap. Python script to encrypt/decrypt data. |
| **Code focus** | `kms.create_key()`, `kms.encrypt()`, `kms.decrypt()` |

---

## Week 2 — Serverless & APIs

### Day 8 — Lambda (Serverless Functions)
| | |
|---|---|
| **What it is** | Run code without managing servers — upload a function, AWS handles the rest. |
| **Core concepts** | Functions, Triggers, Layers, Execution Role, Cold Starts. |
| **Main usage** | Event-driven processing, microservices, automation scripts. |
| **Project step** | Create a Lambda function triggered when an image is uploaded to S3. It logs the event. |
| **Code focus** | `lambda_client.create_function()`, handler function, S3 trigger |

### Day 9 — API Gateway
| | |
|---|---|
| **What it is** | The front door to your backend — creates, publishes and manages REST/HTTP APIs. |
| **Core concepts** | REST APIs, Resources, Methods, Stages, API Keys. |
| **Main usage** | Exposing Lambda functions as HTTP endpoints. |
| **Project step** | Create a REST API with endpoints: `POST /upload` and `GET /images`. Wire to Lambda. |
| **Code focus** | `apigateway.create_rest_api()`, `put_method()`, `put_integration()` |

### Day 10 — DynamoDB (NoSQL Database)
| | |
|---|---|
| **What it is** | A lightning-fast NoSQL database — stores data as key-value pairs or documents. |
| **Core concepts** | Tables, Items, Partition Key, Sort Key, GSI (Global Secondary Index). |
| **Main usage** | High-speed reads/writes for web apps, gaming, IoT. |
| **Project step** | Create an `images` table to store metadata (filename, upload date, status, analysis results). |
| **Code focus** | `dynamodb.create_table()`, `table.put_item()`, `table.query()` |

### Day 11 — SNS (Simple Notification Service)
| | |
|---|---|
| **What it is** | AWS's megaphone — sends messages to many subscribers at once (email, SMS, Lambda, etc.). |
| **Core concepts** | Topics, Subscriptions, Publishers, Message Filtering. |
| **Main usage** | Sending alerts, fan-out messaging, push notifications. |
| **Project step** | Create an SNS topic. When image processing completes, publish a notification. Subscribe your email. |
| **Code focus** | `sns.create_topic()`, `sns.subscribe()`, `sns.publish()` |

### Day 12 — SQS (Simple Queue Service)
| | |
|---|---|
| **What it is** | A message queue — a waiting line where tasks are held until a worker picks them up. |
| **Core concepts** | Standard Queues, FIFO Queues, Visibility Timeout, Dead-Letter Queues. |
| **Main usage** | Decoupling microservices, buffering requests, async processing. |
| **Project step** | Add an SQS queue between S3 upload events and the processing Lambda. Implement retry with a dead-letter queue. |
| **Code focus** | `sqs.create_queue()`, `sqs.send_message()`, `sqs.receive_message()` |

### Day 13 — EventBridge (Event Bus)
| | |
|---|---|
| **What it is** | A smart event router — listens for events and routes them to the right targets based on rules. |
| **Core concepts** | Event Bus, Rules, Targets, Event Patterns, Schedules. |
| **Main usage** | Event-driven architectures, scheduled tasks (cron), cross-service integration. |
| **Project step** | Create an EventBridge rule: every night at midnight, trigger a Lambda that generates a daily report of uploaded images. |
| **Code focus** | `events.put_rule()`, `events.put_targets()`, schedule expressions |

### Day 14 — Step Functions (Workflow Orchestration)
| | |
|---|---|
| **What it is** | A visual workflow engine — chains Lambda functions into a state machine with branching, retries, and parallel steps. |
| **Core concepts** | State Machines, States (Task, Choice, Parallel, Wait), ASL (Amazon States Language). |
| **Main usage** | Orchestrating multi-step processes, order processing, ETL pipelines. |
| **Project step** | Build a Step Function for the image pipeline: Upload → Validate → Analyze → Store Results → Notify. |
| **Code focus** | `stepfunctions.create_state_machine()`, ASL JSON definition |

---

## Week 3 — Data, AI & Content Delivery

### Day 15 — RDS (Relational Database Service)
| | |
|---|---|
| **What it is** | Managed relational databases — MySQL, PostgreSQL, etc. without the admin headaches. |
| **Core concepts** | DB Instances, Multi-AZ, Read Replicas, Snapshots, Parameter Groups. |
| **Main usage** | Traditional SQL databases for structured data. |
| **Project step** | Create a PostgreSQL RDS instance. Store user accounts and detailed analytics in a relational schema. Connect via Python. |
| **Code focus** | `rds.create_db_instance()`, `psycopg2` for queries |

### Day 16 — Secrets Manager
| | |
|---|---|
| **What it is** | A vault for your passwords and API keys — stores, rotates, and retrieves secrets securely. |
| **Core concepts** | Secrets, Rotation, Versions, Resource Policies. |
| **Main usage** | Storing DB passwords, API keys, tokens — never hardcode secrets again. |
| **Project step** | Store the RDS credentials in Secrets Manager. Update Lambda to fetch secrets at runtime. |
| **Code focus** | `secretsmanager.create_secret()`, `secretsmanager.get_secret_value()` |

### Day 17 — Rekognition (Image AI)
| | |
|---|---|
| **What it is** | AWS's eyes — an AI service that can detect objects, faces, text, and scenes in images. |
| **Core concepts** | DetectLabels, DetectFaces, DetectText, Moderation. |
| **Main usage** | Image analysis, content moderation, facial recognition. |
| **Project step** | Integrate Rekognition into the Lambda pipeline. Analyze uploaded images and store detected labels in DynamoDB. |
| **Code focus** | `rekognition.detect_labels()`, `rekognition.detect_text()` |

### Day 18 — Bedrock (Generative AI)
| | |
|---|---|
| **What it is** | AWS's gateway to foundation models (Claude, Titan, etc.) — build generative AI apps without training models. |
| **Core concepts** | Foundation Models, Inference, Prompts, Model IDs, InvokeModel API. |
| **Main usage** | Text generation, summarization, chatbots, content creation. |
| **Project step** | Use Bedrock to generate a creative description/caption for each analyzed image. Store the AI-generated text alongside labels. |
| **Code focus** | `bedrock_runtime.invoke_model()`, prompt engineering, JSON payloads |

### Day 19 — CloudFront (CDN)
| | |
|---|---|
| **What it is** | AWS's content delivery network — caches your content at edge locations worldwide for blazing-fast delivery. |
| **Core concepts** | Distributions, Origins, Edge Locations, Cache Behaviors, Invalidations. |
| **Main usage** | Serving static websites, APIs, and media with low latency globally. |
| **Project step** | Create a CloudFront distribution in front of S3 to serve processed images and the static front-end. |
| **Code focus** | `cloudfront.create_distribution()`, origin config, cache policies |

### Day 20 — Cognito (User Authentication)
| | |
|---|---|
| **What it is** | AWS's identity manager for apps — handles sign-up, sign-in, and access control for your users. |
| **Core concepts** | User Pools, Identity Pools, Tokens (JWT), Hosted UI. |
| **Main usage** | Adding authentication to web/mobile apps without building it from scratch. |
| **Project step** | Create a Cognito User Pool. Add sign-up/sign-in to CloudSnap. Protect API Gateway endpoints with a Cognito authorizer. |
| **Code focus** | `cognito.create_user_pool()`, `cognito.sign_up()`, `cognito.initiate_auth()` |

### Day 21 — Elastic Load Balancing (ELB)
| | |
|---|---|
| **What it is** | A traffic distributor — spreads incoming requests across multiple targets so no single server gets overwhelmed. |
| **Core concepts** | ALB (Application), NLB (Network), Target Groups, Health Checks, Listeners. |
| **Main usage** | High availability, scaling web apps, SSL termination. |
| **Project step** | Create an ALB in front of the EC2 instance. Configure health checks and target groups. |
| **Code focus** | `elbv2.create_load_balancer()`, `create_target_group()`, `register_targets()` |

---

## Week 4 — Infrastructure, DevOps & Advanced Services

### Day 22 — ECS (Elastic Container Service)
| | |
|---|---|
| **What it is** | Run Docker containers on AWS without managing the underlying servers (with Fargate). |
| **Core concepts** | Clusters, Tasks, Services, Task Definitions, Fargate vs EC2 launch type. |
| **Main usage** | Running containerized microservices, batch jobs. |
| **Project step** | Containerize a Python image-processing worker. Deploy it as a Fargate service in ECS. |
| **Code focus** | `ecs.create_cluster()`, `ecs.register_task_definition()`, `ecs.create_service()`, Dockerfile |

### Day 23 — ECR (Elastic Container Registry)
| | |
|---|---|
| **What it is** | A private Docker image warehouse — stores, manages, and deploys container images. |
| **Core concepts** | Repositories, Images, Tags, Lifecycle Policies. |
| **Main usage** | Storing Docker images for ECS, EKS, Lambda containers. |
| **Project step** | Push the CloudSnap container image to ECR. Update ECS to pull from ECR. |
| **Code focus** | `ecr.create_repository()`, Docker build + push commands, `ecr.get_login_password()` |

### Day 24 — CloudFormation (Infrastructure as Code)
| | |
|---|---|
| **What it is** | Define your entire AWS infrastructure in a YAML/JSON file — deploy it all with one click. |
| **Core concepts** | Templates, Stacks, Resources, Parameters, Outputs, Change Sets. |
| **Main usage** | Reproducible infrastructure, environment cloning, disaster recovery. |
| **Project step** | Write a CloudFormation template for the CloudSnap S3 + DynamoDB + Lambda stack. Deploy it. |
| **Code focus** | `cloudformation.create_stack()`, YAML template authoring |

### Day 25 — CDK (Cloud Development Kit)
| | |
|---|---|
| **What it is** | Infrastructure as code using *real programming languages* — define AWS resources in Python instead of YAML. |
| **Core concepts** | Apps, Stacks, Constructs (L1/L2/L3), Synth, Deploy. |
| **Main usage** | Same as CloudFormation but more developer-friendly. |
| **Project step** | Rewrite the CloudFormation template from Day 24 as a CDK app in Python. Compare the experience. |
| **Code focus** | `aws_cdk.Stack`, `aws_s3.Bucket()`, `aws_lambda.Function()`, `cdk deploy` |

### Day 26 — CodePipeline & CodeBuild (CI/CD)
| | |
|---|---|
| **What it is** | Automated build and deploy pipelines — push code, and AWS tests, builds, and deploys it for you. |
| **Core concepts** | Pipelines, Stages, Actions, Build Specs, Artifacts. |
| **Main usage** | Continuous integration/delivery, automated testing and deployment. |
| **Project step** | Create a CI/CD pipeline: GitHub push → CodeBuild (run tests, build Docker image) → Deploy to ECS. |
| **Code focus** | `codepipeline.create_pipeline()`, `buildspec.yml`, `codebuild.create_project()` |

### Day 27 — X-Ray (Distributed Tracing)
| | |
|---|---|
| **What it is** | AWS's detective tool — traces requests as they flow through your distributed system to find bottlenecks. |
| **Core concepts** | Traces, Segments, Subsegments, Service Map, Annotations. |
| **Main usage** | Debugging distributed systems, performance analysis, error tracking. |
| **Project step** | Instrument CloudSnap Lambdas and API Gateway with X-Ray. Visualize the request flow. |
| **Code focus** | `aws_xray_sdk`, `xray_recorder.begin_segment()`, Lambda layer |

### Day 28 — SES (Simple Email Service)
| | |
|---|---|
| **What it is** | AWS's email service — send and receive emails at scale (transactional, marketing, notifications). |
| **Core concepts** | Verified Identities, Sending Quota, Templates, Configuration Sets. |
| **Main usage** | Transactional emails, newsletters, email notifications. |
| **Project step** | Add email notifications to CloudSnap: send users a beautifully formatted email with their image analysis results. |
| **Code focus** | `ses.verify_email_identity()`, `ses.send_email()`, HTML templates |

### Day 29 — Systems Manager (SSM) & Parameter Store
| | |
|---|---|
| **What it is** | A control center for managing your AWS resources — run commands on instances, store configuration, automate operations. |
| **Core concepts** | Parameter Store (config values), Run Command, Session Manager, Automation. |
| **Main usage** | Centralized config management, remote instance management, operational automation. |
| **Project step** | Move CloudSnap feature flags and config values into Parameter Store. Update Lambdas to read config at runtime. |
| **Code focus** | `ssm.put_parameter()`, `ssm.get_parameter()`, SecureString type |

### Day 30 — Putting It All Together 🎉
| | |
|---|---|
| **What it is** | Integration day — connect all the dots and review the full architecture. |
| **Core concepts** | System design, architecture review, cost optimization, best practices. |
| **Main usage** | Final polish, testing end-to-end, cleanup. |
| **Project step** | Run the complete CloudSnap pipeline end-to-end. Create an architecture diagram. Review security, costs, and clean up resources. |
| **Code focus** | End-to-end test script, architecture documentation, `boto3` resource cleanup |

---

## CloudSnap — Architecture Summary

```
User → CloudFront → S3 (Static Site)
         ↓
     API Gateway (+ Cognito Auth)
         ↓
      Lambda → S3 (Image Upload)
         ↓
      SQS Queue
         ↓
      Step Functions:
        ├─ Lambda: Validate Image
        ├─ Lambda: Rekognition (Label Detection)
        ├─ Lambda: Bedrock (AI Caption)
        ├─ Lambda: Store in DynamoDB
        └─ Lambda: SNS + SES Notification
         ↓
      DynamoDB (Metadata) + RDS (Analytics)
         ↓
      CloudWatch + X-Ray (Monitoring)
         ↓
      CodePipeline + CodeBuild (CI/CD)
         ↓
      ECS Fargate (Container Workers)
         ↓
      All infra managed by CDK (Python)
```

---

## Daily Routine

| Step | Time | Activity |
|------|------|----------|
| 1 | 15 min | Read the day's tutorial/concepts |
| 2 | 15 min | Explore the AWS Console for that service |
| 3 | 30 min | Write the day's Python code |
| 4 | 10 min | Test & verify it works |
| 5 | 5 min | Commit code to git |
| 6 | 5 min | Review what you learned |

**Total: ~80 minutes/day**

---

## Prerequisites

- Python 3.9+ installed
- AWS account (Free Tier eligible)
- `boto3` installed (`pip install boto3`)
- AWS CLI configured (`aws configure`)
- A code editor (VS Code recommended)
- Git for version control

---

*Happy learning! One service a day, and in 30 days you'll have a portfolio-worthy AWS project.* 🚀
