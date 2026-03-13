# Database Design: Global ERD

This document visualizes the data relationships across all modules of the platform.

## 1. Entity Relationship Diagram (ERD)

```mermaid
erDiagram
    %% accTitle: Global Database ERD
    USER ||--o{ USER_LESSONS : "tracks progress"
    USER ||--o{ EXPENSES : "logs"
    USER ||--o{ USER_GROUPS : "belongs to"
    USER ||--o{ USER_ACHIEVEMENTS : "earns"
    
    LESSONS ||--o{ USER_LESSONS : "referenced by"
    COMMUNITY_GROUPS ||--o{ USER_GROUPS : "contains"
    
    USER {
        uuid id PK
        string email
        string password_hash
        boolean biometric_enabled
        string capital_level "GEN_Z_START | EXPLORER | INVESTOR"
        int total_xp
        int level
        timestamp created_at
    }

    LESSONS {
        uuid id PK
        string epic_id
        string roadmap_id
        int day_number
        string title
        text content
        jsonb checklist_items
    }

    USER_LESSONS {
        uuid id PK
        uuid user_id FK
        uuid lesson_id FK
        boolean is_completed
        jsonb checklist_state
        timestamp completed_at
    }

    EXPENSES {
        uuid id PK
        uuid user_id FK
        decimal amount
        string category
        string merchant
        string receipt_url
        timestamp logged_at
    }

    COMMUNITY_GROUPS {
        uuid id PK
        string name
        string tag
        decimal target_amount
        timestamp deadline
    }

    USER_GROUPS {
        uuid id PK
        uuid user_id FK
        uuid group_id FK
        decimal personal_target
    }
```

## 2. Core Conventions

- **Primary Keys**: Always `uuid` with `gen_random_uuid()` default.
- **Audit**: All tables include `created_at` and `updated_at`.
- **Soft Delete**: Critical tables (Expenses, Groups) include `deleted_at`.
- **Privacy**: `user_id` is mandatory for all user-scoped data to facilitate RLS.

## 3. Indexing Strategy

- **B-Tree**: On all Foreign Keys (`user_id`, `lesson_id`).
- **GIN**: On `lessons.checklist_items` and `user_lessons.checklist_state` for JSON search if needed.
- **Compound**: `(user_id, logged_at)` on `expenses` for efficient history fetching.
