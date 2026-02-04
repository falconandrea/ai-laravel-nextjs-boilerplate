# 🤖 AI Agent Development Template

A production-ready template repository for building Laravel 12 or Next.js 15+ projects with AI agents (Claude, ChatGPT, etc.). This template prevents **context drift** and ensures AI maintains project consistency across sessions.

## 🎯 What is This?

This is not just a repo template—it's an **Operating System for AI Agents**. It provides:

- ✅ **Persistent Memory** - AI remembers what was built and what went wrong
- ✅ **Context Management** - Clear documentation of tech stack, requirements, and patterns
- ✅ **Guided Workflows** - Ready-to-use prompts for common tasks
- ✅ **Best Practices** - Stack-specific guidelines (Laravel Sail, Next.js App Router)
- ✅ **Lesson Learning** - AI documents mistakes to avoid repeating them
- ✅ **Feature History** - Track every feature with PRD and task lists

## 🚀 Quick Start

### 1. Clone This Template

```bash
git clone <this-repo> my-new-project
cd my-new-project
rm -rf .git
git init
```

### 2. Choose Your Stack

Copy the appropriate tech stack template:

**For Laravel projects:**
```bash
cp .agents/templates/TECH_STACK_laravel.md .agents/context/TECH_STACK.md
```

**For Next.js projects:**
```bash
cp .agents/templates/TECH_STACK_nextjs.md .agents/context/TECH_STACK.md
```

### 3. Run Project Setup

Copy the setup prompt:

```bash
cat .agents/prompts/01_project_setup.md
```

Paste it into your AI chat (Claude, ChatGPT, etc.) and answer the questions. The AI will:
1. Ask discovery questions
2. Create all `.agents/context/` documentation files
3. Set up initial progress tracking
4. Switch to implementation mode

### 4. Start Building!

Your AI now has complete context. You can use commands like:

- "What's next?" → AI reads progress and suggests next steps
- "New feature: [description]" → AI uses feature workflow
- "Debug this: [bug]" → AI follows systematic debugging
- "Review my code" → AI performs thorough review

## 📁 Repository Structure

```
.
├── agents.md                 # Main AI index (read first)
│
└── .agents/
    ├── main/                 # Core documentation
    │   ├── HOW_TO_USE.md
    │   ├── QUICK_START.md
    │   └── SETUP_INTERROGATION.md
    │
    ├── context/              # Project documentation
    │   ├── TECH_STACK.md
    │   ├── PRD.md
    │   ├── APP_FLOW.md
    │   ├── FRONTEND_GUIDELINES.md
    │   ├── BACKEND_STRUCTURE.md
    │   ├── IMPLEMENTATION_PLAN.md
    │   ├── API_CONTRACTS.md
    │   ├── ARCHITECTURE_DECISIONS.md
    │   └── BEST_PRACTICES.md
    │
    ├── memory/               # AI persistent memory
    │   ├── progress.md
    │   ├── lessons.md
    │   ├── blockers.md
    │   ├── decisions-log.md
    │   └── scratching_pad.md
    │
    ├── features/             # Feature history
    │   ├── _TEMPLATE.md
    │   └── [feature-name]/   # One folder per feature
    │       ├── prd-*.md
    │       └── tasks-*.md
    │
    ├── prompts/              # Ready-to-use prompts
    │   ├── 01_project_setup.md
    │   ├── 02_create_prd.md
    │   ├── 03_generate_tasks.md
    │   ├── 04_bug_fix.md
    │   ├── 05_code_review.md
    │   ├── 06_refactoring.md
    │   └── 07_deployment.md
    │
    ├── guidelines/           # Stack-specific guides
    │   ├── laravel/
    │   │   └── sail-guidelines.md
    │   └── nextjs/
    │       └── app-router-guidelines.md
    │
    └── templates/            # Tech stack templates
        ├── TECH_STACK_laravel.md
        └── TECH_STACK_nextjs.md
```

## 🎯 How AI Uses This Repo

### Session Start
1. AI reads `agents.md` (operating instructions)
2. AI reads `.agents/memory/progress.md` (current state)
3. AI reads `.agents/memory/lessons.md` (past mistakes)
4. AI knows the context and picks up where it left off

### During Development
- AI checks `.agents/context/TECH_STACK.md` before using any library
- AI follows patterns in `.agents/context/BEST_PRACTICES.md`
- AI references `.agents/context/PRD.md` to verify feature scope
- AI updates `.agents/memory/progress.md` after completing tasks

### When Adding Features
- AI uses `.agents/prompts/02_create_prd.md` to create PRD
- AI uses `.agents/prompts/03_generate_tasks.md` to create task list
- Feature is documented in `.agents/features/[feature-name]/`

### When Fixing Bugs
- AI checks `.agents/memory/lessons.md` for similar past issues
- AI documents new learnings in `.agents/memory/lessons.md`
- AI prevents repeating the same mistakes

## 📝 Ready-to-Use Prompts

All prompts are in `.agents/prompts/` directory:

| Prompt | Purpose |
|--------|---------|
| `01_project_setup.md` | Initial project interrogation |
| `02_create_prd.md` | Create PRD for new feature |
| `03_generate_tasks.md` | Generate task list from PRD |
| `04_bug_fix.md` | Systematic debugging |
| `05_code_review.md` | Code review checklist |
| `06_refactoring.md` | Safe refactoring guide |
| `07_deployment.md` | Pre-deploy checklist |

## 🎓 AI Operating Modes

### 🧠 PLANNING MODE (Default)
- Asks questions, makes NO assumptions
- Proposes solutions without implementing
- Updates documentation first
- Gets approval before coding

**Trigger**: Start of session, new feature, architectural decision

### ⚡ ACTING MODE
- Writes code following all guidelines
- References documentation constantly
- Updates progress incrementally

**Trigger**: After planning approval, or for small changes

### 🔍 REVIEW MODE
- Runs through review checklist
- Checks for violations of lessons learned
- Proposes improvements

**Trigger**: Say "Review this" or "Code review"

### 🐛 DEBUG MODE
- Systematic debugging approach
- Checks past lessons first
- Documents solution

**Trigger**: Say "Debug this" or describe a bug

## 🆘 Troubleshooting

### AI Seems Lost or Confused
```
Say: "Read agents.md and follow Session Initialization Protocol"
```

### AI Made a Mistake
```
1. Add mistake to .agents/memory/lessons.md
2. Say: "Review lessons.md before continuing"
```

### AI Uses Wrong Versions
```
Say: "Check .agents/context/TECH_STACK.md for correct versions"
```

### Starting a New Session
```
Say: "Read progress.md and tell me what's done and what's next"
```

## 🔧 Supported Tech Stacks

### Laravel 12
- PHP 8.3
- Laravel Sail (Docker)
- MySQL 8.0 / PostgreSQL

**Guidelines**: `.agents/guidelines/laravel/sail-guidelines.md`

### Next.js 15+
- React 19
- TypeScript (strict mode)
- App Router (NO Pages Router)
- TailwindCSS + shadcn/ui

**Guidelines**: `.agents/guidelines/nextjs/app-router-guidelines.md`

## 📄 License

MIT License - Feel free to use this template for any project!

---

**Built with ❤️ to make AI-assisted development actually work**
