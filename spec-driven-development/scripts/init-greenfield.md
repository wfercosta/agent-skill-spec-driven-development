# Script: init-greenfield

> Initialize a new project by generating foundational `.specs/codebase/` documentation as a blueprint for development.

---

## Objective

Guide the user through defining the project's stack, architecture, conventions, and structure before any code is written. Produce all documents under `.specs/codebase/` based on the user's answers and sound engineering defaults. At the end, STATE.md must reflect `CODEBASE_ANALYZED`.

---

## Inputs

- User's project description and goals.
- Answers to clarifying questions (gathered during this script).

---

## Pre-conditions

1. Check whether `.specs/` already exists.
   - If it does, warn the user:
     ```
     Um diretório .specs/ já existe.
     Deseja recriar os documentos de codebase para um projeto greenfield?
     Isso substituirá o conteúdo atual de .specs/codebase/.
     ```
   - Wait for confirmation before proceeding.
2. If `.specs/` does not exist, create the full directory structure silently.

---

## Steps

### Step 1 — Directory Setup

Create the following directories if they do not exist:

```
.specs/
.specs/codebase/
.specs/features/
.specs/quick/
```

### Step 2 — Gather Project Information

Ask the user the following questions. Wait for complete answers before proceeding. You may ask all at once or in a conversational flow.

```
Vou fazer algumas perguntas para criar o blueprint do seu projeto.

1. Qual é o objetivo principal do projeto?
   (Ex.: API REST para e-commerce, CLI para automação de infraestrutura, SaaS de gestão de tarefas)

2. Qual linguagem e runtime você deseja usar?
   (Ex.: TypeScript + Node.js, Go, Python, Java + Spring Boot)

3. Qual é o tipo de aplicação?
   (Ex.: API REST, GraphQL API, CLI, worker/job processor, fullstack web, biblioteca)

4. Você tem preferências de framework?
   (Ex.: Fastify, NestJS, Express, Hono — ou sem preferência)

5. Qual banco de dados será utilizado?
   (Ex.: PostgreSQL, MongoDB, SQLite — ou ainda não definido)

6. Existem integrações externas já previstas?
   (Ex.: serviço de email, pagamentos, armazenamento de arquivos, autenticação terceirizada)

7. Quais são as principais preocupações técnicas?
   (Ex.: alta disponibilidade, baixa latência, segurança de dados, escalabilidade horizontal)

8. Você tem alguma convenção de código ou padrão de equipe já definido?
   (Ex.: Conventional Commits, ESLint Airbnb, testes obrigatórios)
```

### Step 3 — Generate `STACK.md`

Based on the user's answers, produce a STACK.md using `references/stack-template.md` with:
- Recommended language, runtime, and package manager.
- Suggested frameworks with justification.
- Testing libraries appropriate for the chosen stack.
- Build scripts suitable for the project type.
- Infrastructure components based on stated needs.
- Required environment variables (initial set).

Write to: `.specs/codebase/STACK.md`

### Step 4 — Generate `ARCHITECTURE.md`

Using `references/architecture-template.md`, define:
- Recommended architectural style with rationale (e.g., Hexagonal for API with multiple integrations, simple Layered for a basic CRUD service).
- Initial layer/module organization.
- Expected data flow through the system.
- Key components and their responsibilities (initial set, to be expanded as features are defined).
- Cross-cutting concerns strategy.

Write to: `.specs/codebase/ARCHITECTURE.md`

### Step 5 — Generate `CONVENTIONS.md`

Using `references/conventions-template.md`, define:
- File and directory naming conventions.
- Code naming conventions.
- Import order.
- Error handling strategy.
- Commit message convention.
- Code style configuration.
- Patterns explicitly prohibited for this project.

Write to: `.specs/codebase/CONVENTIONS.md`

### Step 6 — Generate `STRUCTURE.md`

Using `references/structure-template.md`, define:
- Proposed root directory layout.
- Module/package breakdown with descriptions.
- Test directory organization.
- Key files to be created (entry points, config, etc.).
- Module boundary rules.

Write to: `.specs/codebase/STRUCTURE.md`

### Step 7 — Generate `TESTING.md`

Using `references/testing-template.md`, define:
- Test strategy (unit, integration, e2e — what's in scope).
- Selected test frameworks and libraries.
- Test commands.
- Test file organization.
- TDD workflow to be followed (mandatory for all coding tasks).
- Coverage targets.

Write to: `.specs/codebase/TESTING.md`

### Step 8 — Generate `INTEGRATIONS.md`

Using `references/integrations-template.md`, document:
- All external integrations identified in Step 2.
- Adapter pattern to be applied for each.
- Local development substitutes (Docker services, mocks, sandbox accounts).

If no integrations were mentioned, create a minimal document noting that all future integrations must follow the adapter pattern.

Write to: `.specs/codebase/INTEGRATIONS.md`

### Step 9 — Generate `CONCERNS.md`

Using `references/concerns-template.md`, document:
- Known risks for this type of project (e.g., security concerns for auth systems, consistency challenges for distributed systems).
- Areas to watch during implementation.
- Any constraints mentioned by the user.

Write to: `.specs/codebase/CONCERNS.md`

### Step 10 — Initialize `STATE.md`

Using `references/state-template.md`, create:

```
Status: CODEBASE_ANALYZED
Feature: -
Current Task: -
Updated At: [current timestamp]

Decisions:
- [YYYY-MM-DD] Stack selected: [language + framework] — [brief rationale]
- [YYYY-MM-DD] Architecture: [style] — [brief rationale]
```

Write to: `.specs/STATE.md`

---

## Outputs

```
.specs/
├── STATE.md                    (Status: CODEBASE_ANALYZED)
└── codebase/
    ├── STACK.md
    ├── ARCHITECTURE.md
    ├── CONVENTIONS.md
    ├── STRUCTURE.md
    ├── TESTING.md
    ├── INTEGRATIONS.md
    └── CONCERNS.md
```

---

## Completion Message

After all files are written, inform the user:

```
Blueprint do projeto gerado com sucesso.

Os seguintes documentos foram criados em .specs/codebase/:
- STACK.md
- ARCHITECTURE.md
- CONVENTIONS.md
- STRUCTURE.md
- TESTING.md
- INTEGRATIONS.md
- CONCERNS.md

Status atual: CODEBASE_ANALYZED

Próximos passos:
- Revise os documentos e ajuste conforme necessário.
- Quando estiver pronto, crie a primeira SDD com: "create SDD for [nome da feature]"
```

---

## Error Handling

- If the user cannot answer a question, proceed with a sensible default and document the assumption in the relevant file.
- If conflicting choices are made (e.g., a framework incompatible with the chosen language), flag the conflict and ask for clarification before generating documents.
- Always document assumptions and open decisions explicitly in the generated files.
