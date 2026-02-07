# Requirements: Hospital-Setu (Health-Bridge)

## 1. Functional Requirements

### 1.1 User Interface

- **FR-01:** Must be accessible via QR/Deep Link (WhatsApp)
- **FR-02:** Must require no login or app install
- **FR-03:** Must capture user consent before processing data
- **FR-04:** Must support voice notes (Telugu, Hindi)
- **FR-05:** Must support text input
- **FR-06:** Must support image uploads

### 1.2 AI Processing

- **FR-07:** Must perform Speech-to-Text using Transcribe Streaming
- **FR-08:** Must perform Text-to-Speech using Polly Neural voices
- **FR-09:** Must perform Vision extraction via multimodal LLM
- **FR-10:** Must retrieve answers from grounded Knowledge Base only
- **FR-11:** Must include citation in every RAG response
- **FR-12:** Must summarize context after 5 turns

### 1.3 Safety & Guardrails

- **FR-13:** Must refuse clinical diagnostic questions
- **FR-14:** Must block abusive or malicious content
- **FR-15:** Must require confirmation for extracted prescription text

### 1.4 Administrative

- **FR-16:** Must auto-ingest new PDFs uploaded to S3
- **FR-17:** Must support hospital-specific data isolation

## 2. Non-Functional Requirements

- **NFR-01:** Voice response latency < 8 seconds (4G)
- **NFR-02:** 99% availability during hospital hours
- **NFR-03:** Session data auto-deleted after 24 hours
- **NFR-04:** All data encrypted at rest and transit
- **NFR-05:** No image persistence
- **NFR-06:** Horizontal scalability via Lambda

## 3. Impact Requirements

- **IR-01:** Reduce helpdesk queries by ≥30%
- **IR-02:** Improve navigation success rate to ≥85%
- **IR-03:** Reduce average patient confusion time by ≥40%