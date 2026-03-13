# Base Design Registry

This file serves as the **Single Source of Truth** for core technical components.

## 1. Services & Repositories

| Service Name | Repository | Protocol/Port | Description |
|---|---|---|---|
| `academy-service` | `github-project-playground-be` | gRPC/HTTP | Manages lessons, roadmaps, and progress. |
| `tracking-service` | `github-project-playground-be` | gRPC/HTTP | Manages expense logs, OCR processing, and budgets. |
| `community-service` | `github-project-playground-be` | gRPC/HTTP | Manages groups, leaderboards, and social interactions. |
| `ai-mentor-service` | `github-project-playground-be` | gRPC/HTTP | Interface for LLM (Gemini) summarization and tone conversion. |
| `profile-service` | `github-project-playground-be` | gRPC/HTTP | Manages user onboarding data, preferences, and biometrics. |

---

## 2. Database Schema (Tables)

### 2.1 Academy Module
- `lessons`: Stores content for 7-day roadmaps and tips.
- `user_lessons`: Junction table for tracking user progress (id, user_id, lesson_id, status, completed_at).

### 2.2 Tracking Module
- `expenses`: User spending logs (id, user_id, amount, category, merchant, receipt_url, date).
- `budgets`: Monthly/Weekly budget settings (id, user_id, category, amount, currency).

### 2.3 Community Module
- `community_groups`: Registry of goal-based groups (id, name, tag, target_amount, deadline).
- `user_groups`: Membership mapping (id, user_id, group_id, personal_target, joined_at).

### 2.4 Profile Module
- `users`: Core user data (id, workspace_id, email, password_hash, biometric_enabled, capital_level).

---

## 3. API Routes Registry

| Module | Route | Method | Description |
|---|---|---|---|
| Academy | `/api/v1/roadmaps/:id` | GET | Get full roadmap content. |
| Tracking | `/api/v1/ocr/scan` | POST | Upload receipt for OCR processing. |
| Tracking | `/api/v1/expenses` | POST | Manually add or confirm expense. |
| AI | `/api/v1/ai/summarize` | POST | Send article URL for AI summarization. |
| Community | `/api/v1/groups/join` | POST | Join a goal-based group. |

---

## 4. Kafka / Event Bus Registry

| Event Topic | Producer | Consumer | Description |
|---|---|---|---|
| `expense.created` | `tracking-service` | `ai-mentor-service` | Trigger FOMO alert check if threshold is near. |
| `lesson.completed` | `academy-service` | `profile-service` | Update user achievement stats. |
| `group.goal_reached` | `community-service` | `profile-service` | Award badges for collective achievements. |

---

## 5. External Integrations

| Provider | Purpose | Usage Level |
|---|---|---|
| Google Gemini API | AI Summarization / Tone | Critical |
| Tesseract.js / MLKit | OCR Extraction | Critical |
| Firebase (FCM) | Push Notifications | Core |
| Local Auth | Biometric Security | Core |
