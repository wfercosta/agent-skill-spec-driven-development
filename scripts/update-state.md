# Script: update-state

> Atualizar `.specs/STATE.md` com o estado atual do fluxo de trabalho e metadados opcionais.

---

## Objetivo

Manter um registro preciso e com timestamp do estado de desenvolvimento atual em `.specs/STATE.md`. Este script é chamado por outros scripts — não deve ser chamado diretamente pelo usuário a menos que precise corrigir manualmente o estado.

---

## Entradas

| Parâmetro | Obrigatório | Descrição |
|-----------|-------------|-----------|
| `status` | Sim | Novo valor de estado (ver estados válidos abaixo) |
| `feature` | Não | Nome da feature atualmente em desenvolvimento |
| `current_task` | Não | ID da tarefa atualmente em andamento |
| `note` | Não | Uma nota breve para adicionar ao histórico de status |
| `blocked_task` | Não | ID da tarefa a marcar como bloqueada (usado com status `BLOCKED`) |
| `blocked_reason` | Não | Motivo do bloqueio |

---

## Estados Válidos

```
UNINITIALIZED
CODEBASE_ANALYZED
FEATURE_SPECIFIED
DESIGN_DEFINED
TASKS_GENERATED
EXECUTION_PLAN_READY
PLAN_APPROVAL_PENDING
TASK_IN_PROGRESS
IMPLEMENTATION_REVIEW_PENDING
COMMIT_APPROVAL_PENDING
FEATURE_COMPLETED
BLOCKED
```

---

## Passos

### Passo 1 — Ler STATE.md Atual

Ler `.specs/STATE.md`. Se o arquivo não existir, criá-lo usando `references/state-template.md` como base.

### Passo 2 — Validar Transição

Verificar se o novo status é um estado válido da lista acima. Se não for, registrar um erro e não atualizar o arquivo.

Opcional: avisar se a transição pular estados intermediários esperados (ex.: saltar de `UNINITIALIZED` diretamente para `TASK_IN_PROGRESS`), mas não bloquear a atualização — o usuário pode ter uma razão válida.

### Passo 3 — Atualizar Campos do Cabeçalho

Atualizar os campos de nível superior:

```markdown
**Status**: [novo status]
**Feature**: [nome da feature ou valor atual se não fornecido]
**Current Task**: [ID da tarefa ou '-' se não aplicável]
**Updated At**: [YYYY-MM-DD HH:MM]
```

### Passo 4 — Acrescentar ao Histórico de Status

Adicionar uma nova linha à tabela de Histórico de Status:

```markdown
| [YYYY-MM-DD HH:MM] | [novo status] | [nota se fornecida, caso contrário '-'] |
```

### Passo 5 — Lidar com Estado BLOCKED

Se o novo status for `BLOCKED`:

1. Adicionar ou atualizar a seção de Bloqueios:
   ```markdown
   ## Bloqueios

   BLOCKED_TASK: [ID da tarefa]
   BLOCKED_REASON: [motivo]
   BLOCKED_AT: [YYYY-MM-DD HH:MM]
   ```

### Passo 6 — Lidar com Estado FEATURE_COMPLETED

Se o novo status for `FEATURE_COMPLETED`:

1. Limpar o campo `Current Task`: `-`.
2. Adicionar uma nota ao histórico de status: `Feature [feature-name] concluída`.
3. Opcionalmente adicionar um aprendizado à seção de Aprendizados se um insight notável foi obtido durante a implementação.

### Passo 7 — Escrever STATE.md Atualizado

Escrever o conteúdo atualizado de volta em `.specs/STATE.md`.

---

## Saídas

- `.specs/STATE.md` atualizado com novo status, timestamp e entrada de histórico.

---

## Exemplo de STATE.md Após Atualização

```markdown
# Estado do Projeto

**Status**: TASK_IN_PROGRESS
**Feature**: payment-retry
**Current Task**: T-002
**Updated At**: 2026-03-12 14:30

---

## Histórico de Status

| Timestamp | Status | Notas |
|-----------|--------|-------|
| 2026-03-10 09:00 | UNINITIALIZED | Projeto inicializado |
| 2026-03-10 09:05 | CODEBASE_ANALYZED | Análise brownfield concluída |
| 2026-03-11 10:00 | DESIGN_DEFINED | SDD para payment-retry aprovada |
| 2026-03-11 15:00 | TASKS_GENERATED | 5 tarefas geradas |
| 2026-03-12 09:00 | EXECUTION_PLAN_READY | Plano apresentado para aprovação |
| 2026-03-12 09:10 | TASK_IN_PROGRESS | T-001 concluída, iniciando T-002 |

---

## Feature Atual

payment-retry

...
```

---

## Tratamento de Erros

- Se `STATE.md` não puder ser escrito (erro de permissão), reportar imediatamente e interromper o script chamador.
- Se o valor de status não estiver na lista de estados válidos, não atualizar e reportar o valor inválido.
- Nunca engolir silenciosamente falhas de atualização — sempre reportá-las.
