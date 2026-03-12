# Script: generate-execution-plan

> Construir um plano de execução em batches a partir do grafo de dependências de tarefas e solicitar aprovação humana antes de iniciar a implementação.

---

## Objetivo

Ler `graph.yaml`, realizar uma ordenação topológica, agrupar as tarefas em batches executáveis que maximizem o paralelismo, produzir `execution.md`, atualizar STATE.md e apresentar o plano ao usuário para aprovação. Não iniciar nenhuma implementação até receber aprovação.

---

## Entradas

- `.specs/features/[feature-name]/tasks.md`
- `.specs/features/[feature-name]/graph.yaml`

---

## Pré-condições

1. `tasks.md` e `graph.yaml` devem existir.
2. `STATE.md` deve estar em `TASKS_GENERATED` ou posterior.
3. O grafo de dependências deve ser um DAG válido (sem ciclos).

---

## Passos

### Passo 1 — Ler Entradas

Ler `graph.yaml` e `tasks.md`.

Extrair para cada tarefa:
- ID da tarefa, título, estimativa.
- Lista `depends_on`.
- Flag `can_parallelize`.

### Passo 2 — Validar Grafo de Dependências

Verificar:
- **Ciclos**: se a tarefa A depende de B e B depende de A (direta ou transitivamente), abortar e reportar o ciclo ao usuário.
- **Referências ausentes**: se uma tarefa lista uma dependência que não existe no grafo, reportá-la.
- **Auto-dependências**: uma tarefa não pode depender de si mesma.

Se alguma validação falhar, não prosseguir. Reportar o problema e aguardar o usuário corrigir `graph.yaml` ou `tasks.md`.

### Passo 3 — Ordenação Topológica

Realizar uma ordenação topológica (algoritmo de Kahn ou baseado em DFS) no grafo de dependências para produzir uma ordem de execução válida.

### Passo 4 — Agrupar em Batches

Um batch é um conjunto de tarefas que podem todas iniciar ao mesmo tempo porque suas dependências são satisfeitas pelos batches anteriores.

Algoritmo:
1. Batch 1 = todas as tarefas com `depends_on: []`.
2. Batch N = todas as tarefas cujas dependências estão todas nos Batches 1..N-1.
3. Repetir até que todas as tarefas sejam atribuídas.

Dentro de cada batch, identificar tarefas que podem ser executadas em paralelo (tarefas com `can_parallelize: true` e sem dependência entre si dentro do mesmo batch).

### Passo 5 — Calcular Estimativas

Para cada batch:
- **Estimativa sequencial**: soma de todas as estimativas das tarefas.
- **Estimativa paralela**: estimativa da tarefa mais longa do batch (caminho crítico).

Estimativas totais:
- **Esforço total (sequencial)**: soma de todas as estimativas das tarefas.
- **Tempo mínimo de parede (com paralelismo total)**: soma do caminho crítico pelos batches.

### Passo 6 — Escrever `execution.md`

Usando `references/execution-template.md` como base, preencher:
- Resumo do grafo de dependências (reproduzido de graph.yaml para referência rápida).
- Cada batch com suas tarefas e notas de paralelismo.
- Tabela de estimativas.
- Rastreador de progresso (todas as tarefas iniciam como `Pendente`).
- A mensagem do gate de aprovação.

Escrever em: `.specs/features/[feature-name]/execution.md`

### Passo 7 — Atualizar `STATE.md` para `EXECUTION_PLAN_READY`

Chamar `scripts/update-state.md` com:
```
Status: EXECUTION_PLAN_READY
Feature: [feature-name]
Updated At: [timestamp atual]
```

### Passo 8 — Apresentar Plano e Solicitar Aprovação (GATE OBRIGATÓRIO)

Apresentar o plano ao usuário e aguardar aprovação explícita. NÃO iniciar nenhuma implementação sem aprovação.

```
Plano de execução para "[feature-name]":

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Batch 1 — Paralelo (podem ser executadas simultaneamente):
  • T-001: [título] (~[Xh])
  • T-002: [título] (~[Xh])

Batch 2 — Sequencial (requer conclusão do Batch 1):
  • T-003: [título] (~[Xh]) — depende de T-001, T-002

[... continuar para todos os batches ...]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Estimativa total de esforço: ~[Xh]
Estimativa com paralelismo:  ~[Xh]
Total de tarefas:            [N]

Você aprova iniciar a implementação?
```

### Passo 9 — Lidar com a Resposta de Aprovação

**Se aprovado:**
- Atualizar `STATE.md` para `PLAN_APPROVAL_PENDING` momentaneamente, depois imediatamente para `TASK_IN_PROGRESS`.
- Confirmar:
  ```
  Plano aprovado. Iniciando implementação com o Batch 1.
  ```
- Começar com o primeiro batch. Se o batch tiver tarefas paralelas e sub-agentes estiverem disponíveis, agir como Orquestrador e instanciar sub-agentes.

**Se não aprovado:**
- Perguntar quais mudanças o usuário deseja:
  ```
  Entendido. O que você gostaria de ajustar no plano?
  (Ex.: reorganizar tarefas, ajustar estimativas, dividir ou mesclar tarefas)
  ```
- Aplicar as mudanças solicitadas, atualizar `tasks.md`, `graph.yaml` e `execution.md`, e então reapresentar para aprovação.

**Se o usuário solicitar mudanças de escopo:**
- Voltar a `generate-sdd.md` ou `generate-tasks.md` conforme apropriado.

---

## Saídas

- `.specs/features/[feature-name]/execution.md`
- `.specs/STATE.md` atualizado (para `EXECUTION_PLAN_READY`, depois para `TASK_IN_PROGRESS` após aprovação)

---

## Tratamento de Erros

- Ciclo no grafo de dependências: abortar, reportar o ciclo com os IDs das tarefas envolvidas, aguardar correção.
- Tarefas referenciadas como dependências que não existem: abortar, reportar os IDs ausentes, aguardar correção.
- Usuário não aprova e não fornece feedback: perguntar novamente com um prompt para preocupações específicas.
- O plano de execução fica inválido após mudanças de escopo: regenerar tarefas e grafo antes de executar este script novamente.
