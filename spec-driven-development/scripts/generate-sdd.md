# Script: generate-sdd

> Generate a Software Design Document (SDD) through iterative refinement with the user.

---

## Objective

Produce a complete and unambiguous specification for a feature by asking targeted questions to remove ambiguity, then generating `spec.md`, `context.md` (if needed), and `design.md` for the feature. Update STATE.md to reflect `DESIGN_DEFINED`.

---

## Inputs

- Feature description from the user's command (e.g., `"create SDD for payment retry"`) OR
- A requirements file provided by the user (e.g., `"generate SDD from requirements.md"`).
- Existing `.specs/codebase/` documents (ARCHITECTURE.md, STACK.md, CONVENTIONS.md).

---

## Pre-conditions

1. `.specs/codebase/` must exist. If not, instruct the user to run project initialization first:
   ```
   O projeto ainda não foi inicializado.
   Execute: "init project" para começar.
   ```
2. Read `.specs/STATE.md` to understand current state.
3. Derive the `feature-name` slug from the user's description (kebab-case, lowercase, max 4 words).
   - Example: `"payment retry logic"` → `payment-retry`
4. Check if `.specs/features/[feature-name]/` already exists.
   - If yes, ask:
     ```
     Já existe uma SDD para "[feature-name]".
     Deseja atualizá-la ou criar uma nova versão?
     ```

---

## Steps

### Step 1 — Extract Initial Description

If a requirements file was referenced, read it. Otherwise, extract the feature description from the user's command.

Summarize your understanding back to the user:
```
Entendi que você deseja especificar: [feature description].

Vou fazer algumas perguntas para detalhar a especificação e remover ambiguidades.
```

### Step 2 — Clarifying Questions

Ask the following questions. Adapt or skip questions that are clearly already answered by the user's input or existing documentation. Group questions logically.

**Problem and Context:**
```
1. Qual problema específico essa funcionalidade resolve?
2. Quem são os usuários ou sistemas que utilizarão essa funcionalidade?
3. Existe alguma funcionalidade similar já implementada que devemos considerar ou reutilizar?
```

**Functional Requirements:**
```
4. Quais são os comportamentos esperados do sistema (o que ele deve fazer)?
5. Quais são os casos de uso principais? Descreva o fluxo do ponto de vista do usuário.
6. Existem casos de uso alternativos ou fluxos de exceção importantes?
```

**Non-Functional Requirements:**
```
7. Existem requisitos de performance? (ex.: tempo de resposta, throughput)
8. Existem requisitos de segurança? (ex.: autenticação, autorização, criptografia)
9. Existe algum SLA ou requisito de disponibilidade?
10. A funcionalidade precisa ser escalável horizontalmente?
```

**Acceptance Criteria:**
```
11. Como saberemos que a funcionalidade está correta e completa?
    Liste os critérios de aceitação mais importantes.
```

**Dependencies and Constraints:**
```
12. Quais partes do sistema existente essa funcionalidade depende?
13. Existem integrações externas envolvidas?
14. Existem restrições técnicas (ex.: não pode usar X, deve manter compatibilidade com Y)?
15. Existem restrições de prazo ou de escopo?
```

**Risks and Open Questions:**
```
16. Quais são os principais riscos ou incertezas que você identifica nessa funcionalidade?
17. Existe alguma decisão de design que está em aberto e precisa ser tomada?
```

### Step 3 — Iterative Refinement

After receiving the answers:
1. Identify any remaining ambiguities or conflicting information.
2. Ask follow-up questions for unresolved items only — do not re-ask what was already answered.
3. Repeat until the agent has enough information to write a complete, unambiguous spec.

Before writing the documents, confirm:
```
Tenho as informações necessárias para gerar a SDD.

Resumo do que entendi:
- Funcionalidade: [summary]
- Requisitos principais: [list]
- Critérios de aceitação: [list]
- Dependências: [list]

Posso prosseguir com a geração dos documentos?
```

### Step 4 — Create Feature Directory

Create the directory: `.specs/features/[feature-name]/`

### Step 5 — Generate `spec.md`

Using `references/sdd-template.md` as the base, populate all sections:
- Assign a feature ID: `FEAT-[NNN]` (increment from existing features).
- Assign traceable requirement IDs: `FEAT-[NNN]-REQ-001`, `FEAT-[NNN]-REQ-002`, etc.
- Fill Functional Requirements from Step 2 answers.
- Fill Non-Functional Requirements from Step 2 answers.
- Fill Acceptance Criteria from Step 2 answers.
- Fill Dependencies and Constraints.
- Fill Traceability Matrix.
- Document any open questions that remain.

Write to: `.specs/features/[feature-name]/spec.md`

### Step 6 — Generate `context.md` (if needed)

Create this file ONLY if there are gray-area decisions, trade-offs, or assumptions that were made during the specification process that are not obvious from the spec itself.

Content should include:
- Key decisions made and their rationale.
- Alternatives considered and why they were rejected.
- Assumptions made due to missing information.

Write to: `.specs/features/[feature-name]/context.md`

### Step 7 — Generate `design.md`

Execute `scripts/generate-design.md` with the generated spec.md as input.

### Step 8 — Update `STATE.md`

Call `scripts/update-state.md` with:
```
Status: DESIGN_DEFINED
Feature: [feature-name]
Updated At: [current timestamp]
```

---

## Outputs

```
.specs/features/[feature-name]/
├── spec.md
├── context.md    (only if needed)
└── design.md
```

`.specs/STATE.md` updated to `DESIGN_DEFINED`.

---

## Completion Message

```
SDD gerada com sucesso para "[feature-name]".

Documentos criados:
- .specs/features/[feature-name]/spec.md
- .specs/features/[feature-name]/design.md
[- .specs/features/[feature-name]/context.md]

Status atual: DESIGN_DEFINED

Próximos passos:
- Revise os documentos gerados.
- Para gerar as tarefas de implementação, execute: "generate tasks for [feature-name]"
```

---

## Error Handling

- If the user cannot answer critical questions, document the gap as an open question in spec.md and flag it in the completion message.
- Never proceed to write spec.md with critical ambiguities unresolved — always ask.
- If a requirements file is referenced but cannot be read, inform the user and ask them to provide the requirements directly.
