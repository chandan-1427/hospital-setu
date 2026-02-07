# Hospital-Setu (Health-Bridge)

A WhatsApp-based AI assistant that helps patients navigate hospital services through voice, text, and image interactions in Telugu and Hindi.

## Overview

Hospital-Setu is a serverless, event-driven healthcare navigation assistant built on AWS. It provides patients with instant access to hospital information through WhatsApp, eliminating the need for app downloads or complex registration processes.

### Key Features

- **Multi-modal Input**: Voice notes, text messages, and prescription images
- **Multilingual Support**: Telugu and Hindi language processing
- **No Installation Required**: Accessible via QR code or deep link
- **Privacy-First**: No data persistence, automatic session cleanup
- **Grounded AI**: Responses based only on verified hospital documentation
- **Medical Safety**: Built-in guardrails prevent diagnostic advice

## Architecture

Hospital-Setu follows a secure, serverless microservices architecture optimized for:

- **Low Latency**: < 8 seconds end-to-end response time
- **High Reliability**: 99% availability during hospital hours
- **Data Privacy**: Encrypted data, no image persistence
- **Cost Efficiency**: Pay-per-use Lambda scaling

### Core Components

- **WhatsApp Business API**: Primary user interface
- **AWS Lambda**: Serverless compute orchestration
- **Amazon Transcribe**: Speech-to-text processing
- **Amazon Polly**: Text-to-speech synthesis
- **Claude 3.5 Sonnet**: Vision and language processing
- **AWS Bedrock**: Knowledge base and RAG retrieval
- **DynamoDB**: Session management
- **OpenSearch**: Vector storage for hospital documents

## Getting Started

### Prerequisites

- AWS Account with appropriate permissions
- WhatsApp Business API access
- Hospital documentation in PDF format

### Quick Start

1. **Setup Infrastructure**: Deploy AWS resources using provided CloudFormation templates
2. **Configure WhatsApp**: Set up webhook endpoints and verify business account
3. **Upload Documentation**: Add hospital PDFs to S3 for knowledge base ingestion
4. **Test Integration**: Verify end-to-end functionality with sample interactions

## Documentation

- **[Requirements](requirements.md)**: Detailed functional and non-functional requirements
- **[Design](design.md)**: Complete system architecture and component specifications

## Usage Examples

### Voice Interaction
```
User: [Voice note in Telugu] "Where is the cardiology department?"
Bot: [Voice response] "Cardiology department is on the 3rd floor, Block A. Take elevator 2."
```

### Prescription Processing
```
User: [Uploads prescription image]
Bot: "I found: CBC Test, X-Ray Chest. Is this correct?"
User: "Yes"
Bot: "CBC Test: Lab on Ground Floor. X-Ray: Radiology, 2nd Floor."
```

### Text Navigation
```
User: "What are the visiting hours?"
Bot: "General visiting hours: 4 PM - 6 PM daily. ICU visiting: 11 AM - 12 PM, 4 PM - 5 PM."
```

## Safety & Compliance

- **Medical Guardrails**: Refuses diagnostic or treatment advice
- **Content Moderation**: Blocks abusive or inappropriate content
- **Data Protection**: GDPR/HIPAA compliant data handling
- **Session Security**: 24-hour auto-deletion of user data

## Performance Metrics

- **Target Response Time**: < 8 seconds for voice interactions
- **Availability**: 99% uptime during hospital operating hours
- **Cost Efficiency**: ₹0.80 - ₹1.50 per interaction
- **Impact Goals**:
  - 30% reduction in helpdesk queries
  - 85% navigation success rate
  - 40% reduction in patient confusion time

## Technology Stack

- **Cloud Platform**: AWS
- **Runtime**: Node.js 20.x
- **AI/ML**: Amazon Bedrock, Claude 3.5, Transcribe, Polly
- **Storage**: DynamoDB, S3, OpenSearch Serverless
- **Security**: WAF, Shield, API Gateway
- **Monitoring**: CloudWatch, X-Ray

## Contributing

This project follows healthcare compliance standards. Please ensure all contributions maintain:

- Patient data privacy
- Medical safety guardrails
- Performance requirements
- Security best practices

## Support

For technical support or questions about Hospital-Setu implementation, please refer to the documentation or contact the development team.

---

*Hospital-Setu: Bridging the gap between patients and healthcare navigation through intelligent, multilingual AI assistance.*
