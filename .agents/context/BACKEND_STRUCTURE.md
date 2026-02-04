# Backend Structure

> Database schema, API architecture, and authentication logic.

---

## Database Schema

### Users Table
```sql
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

### [Entity] Table
```sql
-- Add your tables here
```

---

## Entity Relationships

```
User
├── has many: [Entity]
├── has one: [Entity]
└── belongs to: [Entity]
```

---

## Authentication

### Strategy
- **Method**: [Session / JWT / OAuth]
- **Provider**: [e.g., Laravel Breeze / NextAuth.js / Supabase Auth]

### Protected Routes
- `/dashboard/*` - Requires authentication
- `/admin/*` - Requires admin role

---

## API Endpoints

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| GET | `/api/users` | List users | Yes |
| POST | `/api/users` | Create user | No |
| GET | `/api/users/:id` | Get user | Yes |

---

## Storage

- **Provider**: [Local / S3 / Cloudinary]
- **Usage**: [Profile images, documents, etc.]

---

> Run `.agents/prompts/01_project_setup.md` to populate this file with AI assistance.
