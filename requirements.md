# Requirements: Hospital-Setu (Health-Bridge)

## 1. Project Overview
Hospital-Setu is a WhatsApp-based, voice-first hospital concierge designed to assist semi-literate and non-English speaking patients in India. It uses Generative AI (AWS Bedrock) to provide navigation, procedural guidance, and cost information in vernacular languages, bridging the gap between clinical care and operational access.

## 2. Functional Requirements

### 2.1 User Interface (WhatsApp)
- **FR-01:** System must be accessible via a deep link/QR code that opens a WhatsApp chat.
- **FR-02:** System must accept Voice Notes in Indian languages (focus: Telugu, Hindi) and process them via Amazon Transcribe.
- **FR-03:** System must accept Text Messages for literate users.
- **FR-04:** System must accept Image uploads (Prescriptions/Signage) for analysis.

### 2.2 Core AI Processing
- **FR-05 (Audio Pipeline):** System must orchestrate an Audio-to-Text-to-LLM-to-Audio pipeline for voice users.
- **FR-06 (Vision Analysis):** System must use Multimodal LLMs (Claude 3.5 Sonnet) to extract specific intent (Test Name, Room Number) from uploaded images.
- **FR-07 (Reasoning & Context):** System must retrieve answers from a dedicated Knowledge Base while maintaining conversation context (resolving pronouns based on previous turns).
- **FR-08 (Vernacular Output):** System must convert generated English responses back into the user's input language using Amazon Polly.

### 2.3 Output Handling & Logic
- **FR-09 (Mode Switching):** System must output Audio + Visual Landmarks for Voice inputs, and Text only for Text inputs.
- **FR-10 (Medical Guardrails):** System must strictly refuse to answer clinical/diagnostic questions (e.g., "Is this cancer?") and redirect to a human doctor.
- **FR-11 (Fallback Mechanism):** If intent is unclear or confidence is low, the system must prompt the user to retry rather than guessing.

### 2.4 Administrative Functions
- **FR-12 (Data Sync):** System must support "Drop-and-Go" updates where uploading a new PDF to S3 automatically updates the AI's knowledge base.

## 3. Non-Functional Requirements
- **NFR-01 (Latency):** End-to-end voice response latency should not exceed 8 seconds on 4G networks.
- **NFR-02 (Reliability):** RAG responses must include a citation or reference to the source document to ensure factual grounding.
- **NFR-03 (Accessibility):** The solution must require no app installation or login credentials.
- **NFR-04 (Privacy):** Patient images (prescriptions) must be processed in-memory for intent extraction and strictly discarded immediately after processing (Stateless Architecture).

## 4. User Stories
- **US-1:** As an illiterate patient, I want to speak in Telugu to find the blood test lab so that I don't have to ask rude staff.
- **US-2:** As a patient with a handwritten prescription, I want to take a photo of it so I can know exactly which room to go to.
- **US-3:** As a hospital administrator, I want to update the PDF rulebook so that the AI automatically gives the new prices without code changes.
- **US-4:** As a patient waiting in a queue, I want to know "How much longer" based on the current average wait times.