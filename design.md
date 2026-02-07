# Design Document: Hospital-Setu

## 1. High-Level Architecture

Hospital-Setu follows a Secure, Serverless, Event-Driven Microservices Architecture on AWS, optimized for:

- Low latency (< 8s end-to-end)
- High reliability
- Strict data privacy
- Controlled LLM grounding
- Cost-efficient scaling

### Core Data Flow

```
User (WhatsApp)
   ↓
Meta Webhook
   ↓
Amazon API Gateway (WAF + Throttling)
   ↓
Lambda Orchestrator (Intent Router)
   ↓
Parallel Processing Layer
   ├── Speech Pipeline (Transcribe)
   ├── Vision Pipeline (Claude 3.5 Vision)
   ├── RAG Retrieval (Bedrock KB)
   ↓
Response Synthesizer (Claude 3.5 Sonnet)
   ↓
Amazon Polly (TTS)
   ↓
WhatsApp Media API
```

## 2. Component Architecture

### 2.1 Interface Layer

**WhatsApp Business API (Cloud API)**
- Entry via QR Code / Deep Link
- No login required
- First interaction includes:
  - Consent notice
  - Usage disclaimer
  - Guardrail information

### 2.2 Edge & Security Layer

**Amazon API Gateway**
- Request validation
- Schema validation
- Throttling
- API key verification

**AWS WAF**
- Prevents abuse
- Blocks malicious payloads

**AWS Shield**
- DDoS protection

### 2.3 Orchestration Layer

**Lambda Orchestrator (Node.js 20.x)**

Responsibilities:
- Detect input type (Voice/Text/Image)
- Route to appropriate pipeline
- Fetch session memory
- Enforce guardrails
- Call RAG layer
- Build final response

*Provisioned Concurrency enabled for demo stability.*

## 3. AI Processing Pipelines

### 3.1 Voice Pipeline (Optimized for Latency)

**Process Flow:**
1. WhatsApp sends .ogg
2. Lambda downloads to /tmp
3. Convert to PCM (if required)
4. Amazon Transcribe Streaming
   - Automatic language detection (te-IN, hi-IN)
   - PII redaction enabled
5. Extracted text → Intent Router

**Latency optimization:**
- Streaming mode reduces wait time
- Parallel RAG fetch while transcription completes

### 3.2 Vision Pipeline (Prescription Decoder)

**Process:**
- Image passed to Claude 3.5 Sonnet (Multimodal)
- Extract:
  - Test Names
  - Doctor notes
  - Department references

**Safety Step:**
- Extracted text is echoed back for user confirmation
- Example: "I found: CBC Test, X-Ray Chest. Is this correct?"

**Security:**
- No image persistence
- Processed fully in-memory (RAM)

### 3.3 RAG Layer (Grounded Intelligence)

**Infrastructure:**
- AWS Bedrock Knowledge Base
- Vector Store: OpenSearch Serverless
- Data Source: S3 Bucket (Hospital PDFs)

**Grounding Rules:**
- Model must answer ONLY using retrieved context
- If confidence < threshold → "I cannot find that information."
- All responses include:
  - Source document name
  - Page number (citation)

**Hallucination Prevention Strategy:**
System prompt enforces:
- "Do not answer beyond retrieved content."
- "If unknown, say I do not know."

### 3.4 Session Memory Strategy

**DynamoDB stores:**
- `session_id`
- `user_phone`
- `rolling_summary`
- `last_user_intent`
- `preferred_language`

**Context Handling Strategy:**
- After 5 turns → Summarize conversation
- Store compressed summary
- Avoid token explosion

## 4. Guardrails & Safety Design

### 4.1 Medical Guardrails

**If user asks:**
- Diagnosis
- Prognosis
- Treatment advice

**System Response:**
"I am a navigation assistant. Please consult a doctor."

*Intent classifier runs before LLM invocation.*

### 4.2 Abuse Prevention

**Moderation Layer:**
- AWS Comprehend for toxicity
- Block harmful or suspicious queries
- Log flagged interactions (without PII)

### 4.3 Consent & Privacy

**First interaction:**
- Usage disclaimer
- Data processing notice
- Explicit "Reply YES to continue"

**Images:**
- Never stored
- Deleted from memory after processing

**Data Retention:**
- Session expires after 24 hours
- Auto-delete via DynamoDB TTL

## 5. Scalability & Cost Optimization

### 5.1 Concurrency Control

- Lambda concurrency cap
- Rate limiting per phone number
- Circuit breaker for Bedrock failures

### 5.2 Caching Strategy

**API Gateway cache for:**
- Static hospital maps
- Frequently asked costs
- Reduces LLM calls by ~30%

### 5.3 Estimated Cost Model (Realistic)

**Per Interaction:**
- Transcribe (~10 sec audio)
- Claude 3.5 tokens
- Polly Neural
- WhatsApp API fee

**Estimated:** ₹0.80 – ₹1.50 per interaction (optimized usage)

## 6. Observability & Monitoring

- CloudWatch Logs
- CloudWatch Alarms
- X-Ray tracing
- Bedrock invocation metrics
- Failure alert to admin email