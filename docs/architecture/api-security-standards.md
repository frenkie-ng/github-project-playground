# API & Security Standards

This document defines the protocols, authentication flows, and security measures for the platform.

## 1. Authentication Flow

We use **Supabase Auth** (GoTrue) for user management.

### 1.1 Web Login Flow
1.  Frontend calls `supabase.auth.signInWithPassword()`.
2.  Supabase returns a **JWT (Access Token)** and a Refresh Token.
3.  Frontend stores the JWT in memory (or secure HttpOnly cookie if handled by middleware).
4.  Subsequent requests to NestJS include `Authorization: Bearer <JWT>`.

### 1.2 Biometric Enrollment
1.  User authenticates normally (Step 1.1).
2.  User enables Biometrics in App Settings.
3.  Frontend calls native API to register credential.
4.  Frontend stores a signature/token locally for subsequent "Quick Unlock".

## 2. API Communication

### 2.1 Supabase PostgREST (Data Read)
- **Use for**: List views, details, academy content, profile metadata.
- **Convention**: Use the Supabase JS Client.
- **Security**: Enforced by RLS based on `auth.uid()`.

### 2.2 NestJS (Business Logic & Mutations)
- **Use for**: OCR processing, AI summarization, completing lessons (XP logic), joining groups.
- **Auth**: NestJS validates the Supabase JWT using a custom `SupabaseAuthGuard`.
- **Response Format**: Standard JSON.
  ```json
  {
    "success": true,
    "data": { ... },
    "error": null
  }
  ```

## 3. Row Level Security (RLS) Rules

Default policy for all user-owned tables:
```sql
ALTER TABLE [table_name] ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Own data only" ON [table_name]
  FOR ALL
  USING (auth.uid() = user_id);
```

## 4. Input Validation

- All NestJS endpoints must use `class-validator` and `class-transformer`.
- Sanitize all HTML/Markdown inputs to prevent XSS.
- Validate all file uploads (Receipt images) for MIME-type and size.

## 5. AI Safety (Gemini)

- **Prompt Guard**: AI Mentor Service must include system-level instructions to avoid financial advice liability (Disclaimer injection).
- **Data Privacy**: No PII (Personally Identifiable Information) should be sent to the Gemini API unless necessary for the specific feature (and encrypted).
