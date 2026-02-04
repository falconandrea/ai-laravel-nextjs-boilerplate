# API Contracts

> API endpoint specifications and contracts.

---

## Base URL
- **Development**: `http://localhost:3000/api` or `http://localhost/api`
- **Production**: `https://[domain]/api`

## Authentication
- **Type**: [Bearer Token / Session Cookie]
- **Header**: `Authorization: Bearer {token}`

---

## Endpoints

### Users

#### GET /api/users
List all users (paginated)

**Query Parameters:**
| Param | Type | Default | Description |
|-------|------|---------|-------------|
| page | int | 1 | Page number |
| limit | int | 10 | Items per page |

**Response:**
```json
{
  "data": [
    { "id": 1, "name": "John", "email": "john@example.com" }
  ],
  "meta": {
    "page": 1,
    "limit": 10,
    "total": 100
  }
}
```

#### POST /api/users
Create a new user

**Request Body:**
```json
{
  "name": "string (required)",
  "email": "string (required, email)",
  "password": "string (required, min: 8)"
}
```

**Response:** `201 Created`
```json
{
  "id": 1,
  "name": "John",
  "email": "john@example.com",
  "created_at": "2024-01-01T00:00:00Z"
}
```

---

## Error Responses

```json
{
  "error": "Error type",
  "message": "Human readable message",
  "details": {} 
}
```

| Status | Meaning |
|--------|---------|
| 400 | Bad Request - Invalid input |
| 401 | Unauthorized - Missing/invalid auth |
| 403 | Forbidden - Insufficient permissions |
| 404 | Not Found - Resource doesn't exist |
| 422 | Validation Error - Invalid data |
| 500 | Server Error - Something went wrong |

---

> Run `.agents/prompts/01_project_setup.md` to populate this file with AI assistance.
