# Script: retry-task

> Retentar uma tarefa falhada com backoff exponencial. Marcar a tarefa como BLOCKED após exceder o número máximo de tentativas.

---

## Objetivo

Lidar com falhas de tarefas de forma elegante retentando até 3 vezes com backoff exponencial. Se todas as tentativas forem esgotadas, atualizar STATE.md para `BLOCKED` e notificar o usuário com um relatório detalhado de falha.

---

## Entradas

- ID da tarefa (ex.: `T-003`).
- Descrição da falha (o que falhou, mensagens de erro, detalhes da última tentativa).
- `.specs/features/[feature-name]/tasks.md`
- `.specs/features/[feature-name]/execution.md`
- `.specs/STATE.md`

---

## Política de Retentativa

```yaml
retry_policy:
  max_attempts: 3
  backoff:
    attempt_1: imediata     # retentar imediatamente
    attempt_2: 30 segundos  # aguardar antes da segunda retentativa
    attempt_3: 120 segundos # aguardar antes da terceira retentativa
  on_exhaustion: BLOCKED
```

---

## Passos

### Passo 1 — Ler Histórico de Retentativas

Ler `execution.md` e encontrar o log de retentativas da tarefa alvo.

Determinar `current_attempt_count`:
- Se não houver entrada no log de retentativas: `current_attempt_count = 0`.
- Caso contrário: ler o contador do log de retentativas.

### Passo 2 — Verificar Contador de Tentativas

```
Se current_attempt_count >= 3:
    → Ir para o Passo 7 (Marcar como BLOCKED)
Senão:
    → Continuar para o Passo 3
```

### Passo 3 — Registrar a Falha

Acrescentar ao log de retentativas em `execution.md`:

```markdown
| [ID da tarefa] | [tentativa N] | [timestamp] | [motivo da falha] | [timestamp da próxima tentativa] |
```

### Passo 4 — Diagnosticar a Falha

Antes de retentar, analisar a falha:

1. Ler as mensagens de erro e stack traces da tentativa anterior.
2. Determinar a categoria da falha:
   - **Falha transiente** (ex.: timeout de rede, teste instável, condição de corrida): retentar com a mesma abordagem.
   - **Falha de lógica** (ex.: implementação errada, requisito mal entendido): ajustar a abordagem antes de retentar.
   - **Falha de ambiente** (ex.: dependência ausente, ferramenta quebrada): resolver o problema de ambiente primeiro.
   - **Lacuna na especificação** (ex.: requisito ambíguo, design incompleto): não retentar — escalar para o usuário.

3. Se a falha for uma Lacuna na Especificação, ir diretamente para o Passo 6 (escalar).

### Passo 5 — Retentar com Backoff

Aplicar o atraso de backoff apropriado para a tentativa atual:

| Tentativa | Backoff |
|-----------|---------|
| 1ª retentativa | Imediata |
| 2ª retentativa | 30 segundos |
| 3ª retentativa | 120 segundos |

Incrementar `current_attempt_count` em 1.

Atualizar o log de retentativas de `execution.md` com o horário de início da nova tentativa.

Re-executar a tarefa usando `scripts/execute-task.md` com quaisquer ajustes identificados no Passo 4.

Se a retentativa for bem-sucedida: marcar a tarefa como `Concluída` em `execution.md` e continuar normalmente.

Se a retentativa falhar: voltar ao Passo 2 com o contador incrementado.

### Passo 6 — Escalar Lacuna na Especificação

Se a falha for devido a uma especificação ambígua ou design incompleto:

```
A tarefa [T-XXX] falhou devido a uma lacuna na especificação:

Problema identificado: [descrição]

Ação necessária:
- Revisar .specs/features/[feature-name]/spec.md — [seção específica]
- OU revisar .specs/features/[feature-name]/design.md — [seção específica]

O que gostaria de fazer?
1. Atualizar a especificação e re-gerar as tarefas afetadas.
2. Fornecer esclarecimentos diretamente para que eu possa prosseguir.
3. Pular esta tarefa e continuar com as próximas (marcar como bloqueada).
```

Aguardar resposta do usuário antes de tomar qualquer ação.

### Passo 7 — Marcar como BLOCKED (tentativas esgotadas)

Após 3 tentativas falhadas:

**Atualizar `execution.md`:**
Marcar a tarefa como `Bloqueada`.

```markdown
| T-XXX | Bloqueada | [timestamp] | [motivo da falha final] | - |
```

**Chamar `scripts/update-state.md`:**
```
Status: BLOCKED
Feature: [feature-name]
Current Task: T-XXX
blocked_task: T-XXX
blocked_reason: [resumo do motivo da falha]
```

**Notificar o usuário:**

```
A tarefa [T-XXX]: "[título da tarefa]" foi bloqueada após 3 tentativas.

Histórico de falhas:
  Tentativa 1: [timestamp] — [motivo]
  Tentativa 2: [timestamp] — [motivo]
  Tentativa 3: [timestamp] — [motivo]

Causa provável: [diagnóstico]

Tarefas bloqueadas como consequência (dependem de T-XXX):
  - [T-YYY]: "[título]"
  - [T-ZZZ]: "[título]"

Opções:
1. Fornecer mais contexto ou uma abordagem alternativa para que eu tente novamente.
2. Resolver o bloqueio manualmente e me informar quando estiver pronto.
3. Remover a dependência em T-XXX e continuar com outras tarefas.
4. Revisar a especificação ou design desta tarefa.
```

Aguardar orientação do usuário.

---

## Saídas

- Log de retentativas de `execution.md` atualizado.
- `STATE.md` atualizado (de volta para `TASK_IN_PROGRESS` em caso de sucesso, ou `BLOCKED` em caso de esgotamento).
- Notificação ao usuário com relatório de falha e opções.

---

## Tratamento de Erros

- Se `execution.md` ou `tasks.md` não puderem ser lidos, reportar o problema e interromper.
- Se o temporizador de backoff não for aplicável no contexto atual (sessão interativa), simplesmente indicar o tempo de espera recomendado e prosseguir quando o usuário confirmar.
- Nunca engolir silenciosamente falhas de retentativa — sempre atualizar o log de retentativas e notificar o usuário.
