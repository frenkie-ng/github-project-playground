# Epic: Non-Functional Requirements

**ID**: EPIC-000

## 1. Security & Privacy
- **Data Encryption**: All user financial data must be encrypted at rest and in transit.
- **Biometric Authentication**: App must support FaceID/Fingerprint for sensitive data access.
- **Privacy First**: No personal bank account credentials should be stored directly unless integrated via secure providers (Plaid/GoCardless).

## 2. Performance
- **Micro-learning Speed**: Content must load in under 2 seconds even on slow 3G connections.
- **OCR Accuracy**: Receipt scanning should process in under 5 seconds with >80% accuracy.

## 3. Compliance
- **Disclaimer**: Every financial tool must include a clear disclaimer that it is not professional investment advice.
- **Terms of Service**: Users must agree to terms before accessing "Check-var" or "AI Mentor" features.
