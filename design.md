# Design Document: Hospital-Setu

## 1. High-Level Architecture
The system utilizes a **Serverless, Event-Driven Microservices Architecture** on AWS to ensure scalability, low maintenance, and pay-per-use cost efficiency.

**Core Data Flow:**
`User (WhatsApp)` ↔ `Meta Webhook` ↔ `Amazon API Gateway` ↔ `AWS Lambda (Orchestrator)` ↔ `AWS Bedrock (Intelligence)`



## 2. Tech Stack & Infrastructure Strategy

### 2.1 Compute & Interface
- **Frontend:** WhatsApp Business API (Managed via Meta/Twilio).
- **API Layer:** **Amazon API Gateway** (REST API) to handle webhook verification, throttling, and routing.
- **Compute:** **AWS Lambda** (Node.js 20.x) for stateless execution of business logic.

### 2.2 AI & Logic Layer
- **Speech Services:**
  - **Amazon Transcribe:** Automatic Language Identification (Identify `te-IN` vs `hi-IN`) + Speech-to-Text.
  - **Amazon Polly:** Neural Text-to-Speech (Engine: `Aditi` or `Kajal`) for lifelike vernacular audio.
- **Reasoning Engine:** **AWS Bedrock (Claude 3.5 Sonnet)**.
  - *Role:* Intent classification, Multi-turn reasoning, and Vision analysis.
- **Knowledge Base (RAG):** **AWS Bedrock Knowledge Bases** (using OpenSearch Serverless vector store) connected to an **S3 Bucket** (Data Source).

### 2.3 Data & State Management
- **Session Store:** **Amazon DynamoDB**.
  - *Purpose:* Stores `session_id`, `user_phone`, and `last_5_turns` of conversation to enable contextual answers (e.g., resolving "How much does **it** cost?").
- **Asset Store:** **Amazon S3**.
  - *Purpose:* Stores hospital PDF rulebooks (Ingestion) and temporary audio buffers (Processing).

## 3. Detailed Data Flows

### 3.1 Voice Query Pipeline (The "Happy Path")
1.  **Ingest:** WhatsApp posts a binary media object to API Gateway.
2.  **Processing:** Lambda downloads the `.ogg` audio file to ephemeral storage (`/tmp`).
3.  **Transcribe:** Lambda triggers `TranscribeService` -> Returns text: *"Where is the blood bank?"*
4.  **Context Fetch:** Lambda pulls recent chat history from **DynamoDB**.
5.  **Reasoning (RAG):**
    - Lambda calls Bedrock Agent with Text + History.
    - Bedrock queries Knowledge Base (Vector Search) for "Blood Bank Location".
    - Bedrock synthesizes answer: *"Block B, Ground Floor."*
6.  **Synthesis:** Lambda sends text to **Polly** -> Receives `.mp3` stream.
7.  **Response:** Lambda uploads `.mp3` to WhatsApp Media Endpoint and sends the audio message to user.

### 3.2 Knowledge Ingestion Pipeline (Admin Flow)
1.  **Admin** uploads `Hospital_Rules_v2.pdf` to S3 Bucket.
2.  **S3 Event Notification** triggers a helper Lambda.
3.  **Sync:** Lambda calls `StartIngestionJob` on Bedrock Knowledge Base.
4.  **Indexing:** Bedrock chunks the PDF, creates vector embeddings, and updates the search index automatically.

## 4. Security & Privacy Design

### 4.1 Data Privacy
- **Stateless Vision:** Patient images (prescriptions) are processed in volatile memory (`RAM`) and never persisted to disk/S3.
- **PII Redaction:** Transcribe jobs are configured with PII redaction enabled to mask names/phone numbers in logs.

### 4.2 Access Control
- **IAM Least Privilege:** The Main Lambda function has restricted permissions:
  - `s3:GetObject` (Read Only)
  - `bedrock:InvokeModel` (Inference Only)
  - `dynamodb:PutItem` (Session Write)
- **Encryption:**
  - Data at Rest: S3 and DynamoDB encrypted via **AWS KMS**.
  - Data in Transit: TLS 1.3 enforced on API Gateway.

## 5. Scalability Considerations
- **Concurrency:** Lambda concurrency limits set to prevent runaway costs during traffic spikes.
- **Caching:** API Gateway Caching enabled for static assets (hospital maps/images) to reduce latency.