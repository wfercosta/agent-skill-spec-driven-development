# Script: init-brownfield

> Analyze an existing codebase and generate the `.specs/codebase/` documentation set.

---

## Objective

Produce a complete and accurate snapshot of the existing system by reading the codebase, then populating all documents under `.specs/codebase/` using the corresponding reference templates. At the end, the STATE.md must reflect `CODEBASE_ANALYZED`.

---

## Inputs

- Existing source code in the current working directory.
- Optionally: a `README.md`, `package.json`, `docker-compose.yml`, or any configuration files at the root.

---

## Pre-conditions

1. Check whether `.specs/` already exists.
   - If it does, warn the user:
     ```
     Um diretório .specs/ já existe neste projeto.
     Deseja sobrescrever os documentos existentes em .specs/codebase/?
     (Isso não afetará .specs/features/ ou .specs/quick/)
     ```
   - Wait for explicit confirmation before proceeding.
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

### Step 2 — Codebase Exploration

Systematically read the codebase to gather information for each document. Recommended reading order:

1. Root config files: `package.json`, `tsconfig.json`, `pom.xml`, `go.mod`, `Cargo.toml`, `Gemfile`, etc.
2. `docker-compose.yml`, `Dockerfile`, `.env.example`, `Makefile`.
3. `README.md` or any top-level documentation.
4. Directory structure (top 3 levels).
5. Sample source files from each major module/package (2–3 files per module).
6. Test files to understand testing patterns.
7. CI/CD configuration: `.github/workflows/`, `Jenkinsfile`, `.gitlab-ci.yml`.

### Step 3 — Generate `STACK.md`

Using `references/stack-template.md` as the base, fill in:
- Language, runtime, and package manager with detected versions.
- Core frameworks and libraries from dependency files.
- Testing libraries.
- Build scripts extracted from the project configuration.
- Infrastructure components identified from Docker/compose files.
- Environment variables from `.env.example` or documentation.

Write to: `.specs/codebase/STACK.md`

### Step 4 — Generate `ARCHITECTURE.md`

Using `references/architecture-template.md` as the base, document:
- The architectural style (e.g., layered, hexagonal, MVC, microservices).
- How the source code is organized into layers or modules.
- The main data flow through the application.
- Key components and their responsibilities.
- Dependency direction rules observed.
- Cross-cutting concerns (logging, error handling, auth).

Write to: `.specs/codebase/ARCHITECTURE.md`

### Step 5 — Generate `CONVENTIONS.md`

Using `references/conventions-template.md` as the base, document:
- File and directory naming patterns observed.
- Class, function, variable, and constant naming conventions.
- Import organization patterns.
- Error handling approach.
- Commit message convention (check git log if accessible).
- Code style settings from linter/formatter config files.
- Patterns to avoid based on any existing linting rules.

Write to: `.specs/codebase/CONVENTIONS.md`

### Step 6 — Generate `STRUCTURE.md`

Using `references/structure-template.md` as the base, document:
- Root directory layout.
- `src/` (or equivalent) module breakdown with descriptions.
- `test/` or test directory organization.
- Key files and their purposes.
- Module boundary rules observed.

Write to: `.specs/codebase/STRUCTURE.md`

### Step 7 — Generate `TESTING.md`

Using `references/testing-template.md` as the base, document:
- Test frameworks and libraries in use.
- How to run tests (commands from package scripts or Makefile).
- Test file organization (co-located vs. parallel directory).
- Mock and stub patterns observed.
- Coverage configuration if found.
- Any existing TDD or BDD conventions.

Write to: `.specs/codebase/TESTING.md`

### Step 8 — Generate `INTEGRATIONS.md`

Using `references/integrations-template.md` as the base, document:
- External services identified (databases, message brokers, payment gateways, email providers, cloud storage, etc.).
- Authentication methods used for each integration.
- Adapter or wrapper patterns observed.
- Local development substitutes (Docker services, mocks).

Write to: `.specs/codebase/INTEGRATIONS.md`

### Step 9 — Generate `CONCERNS.md`

Using `references/concerns-template.md` as the base, document observed issues:
- Missing or low test coverage areas.
- Complex code without documentation.
- Deprecated dependencies.
- Known TODO/FIXME comments in the code.
- Potential security vulnerabilities (hardcoded credentials, missing validation).
- Performance bottlenecks (N+1 patterns, missing indexes, large payloads).

Write to: `.specs/codebase/CONCERNS.md`

### Step 10 — Initialize `STATE.md`

Using `references/state-template.md` as the base, create:

```
Status: CODEBASE_ANALYZED
Feature: -
Current Task: -
Updated At: [current timestamp]
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
Inicialização brownfield concluída.

Os seguintes documentos foram gerados em .specs/codebase/:
- STACK.md
- ARCHITECTURE.md
- CONVENTIONS.md
- STRUCTURE.md
- TESTING.md
- INTEGRATIONS.md
- CONCERNS.md

Status atual: CODEBASE_ANALYZED

Próximos passos:
- Revise os documentos gerados e corrija qualquer imprecisão.
- Quando estiver pronto, crie uma SDD com: "create SDD for [nome da feature]"
```

---

## Error Handling

- If the codebase is too large to read completely, prioritize entry points, configuration files, and one representative file per module.
- If a document cannot be fully populated due to missing information, fill what is possible and add a `<!-- TODO: verify -->` comment on uncertain sections.
- Never fabricate information — if unsure, document the uncertainty explicitly.
