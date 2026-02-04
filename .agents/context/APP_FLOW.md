# Application Flow

> This file documents all user journeys and navigation paths.

---

## Core User Flows

### Flow 1: [Primary User Journey]
**Trigger**: [What starts this flow?]

1. User [action]
2. System [response]
3. User [action]
4. **Success**: [What happens on success]
5. **Error**: [What happens on error]

### Flow 2: [Secondary User Journey]
**Trigger**: [What starts this flow?]

1. User [action]
2. System [response]
3. **Success**: [What happens on success]
4. **Error**: [What happens on error]

---

## Screen Inventory

| Screen | Route | Purpose |
|--------|-------|---------|
| Home | `/` | Landing page |
| Login | `/login` | Authentication |
| Dashboard | `/dashboard` | Main user area |

---

## Navigation Map

```
Home
├── Login → Dashboard
├── Register → Dashboard
└── Public Pages
    ├── About
    └── Contact

Dashboard (authenticated)
├── Profile
├── Settings
└── [Feature Areas]
```

---

> Run `.agents/prompts/01_project_setup.md` to populate this file with AI assistance.
