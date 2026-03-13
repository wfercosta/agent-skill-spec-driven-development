# Plano de Execução: [NOME DA FEATURE]

> Plano de execução em batches derivado do grafo de dependências.

**Feature ID**: [FEAT-XXX]
**Gerado Em**: [YYYY-MM-DD]
**Status**: Aguardando Aprovação | Aprovado | Em Andamento | Concluído

---

## Resumo do Grafo de Dependências

```yaml
# resumo do graph.yaml
tasks:
  T-001:
    depends_on: []
  T-002:
    depends_on: []
  T-003:
    depends_on:
      - T-001
      - T-002
```

---

## Batches de Execução

### Batch 1 — Paralelo *(podem ser executadas simultaneamente)*

| Tarefa | Título | Estimativa | Pode Paralelizar |
|--------|--------|------------|-----------------|
| T-001 | [título] | [Xh] | Sim — independente |
| T-002 | [título] | [Xh] | Sim — independente |

**Nota de orquestração**: T-001 e T-002 não têm dependências. Se sub-agentes estiverem disponíveis, executar em paralelo.

---

### Batch 2 — Sequencial *(requer conclusão do Batch 1)*

| Tarefa | Título | Estimativa | Depende De |
|--------|--------|------------|------------|
| T-003 | [título] | [Xh] | T-001, T-002 |

---

## Estimativas

| Métrica | Valor |
|---------|-------|
| Total de tarefas | [N] |
| Esforço total estimado | [Xh] |
| Mínimo sequencial (caminho crítico) | [Xh] |
| Mínimo com paralelismo (todo paralelismo explorado) | [Xh] |

---

## Progresso da Execução

| Tarefa | Status | Iniciado Em | Concluído Em | Commit |
|--------|--------|------------|-------------|--------|
| T-001 | Pendente | - | - | - |
| T-002 | Pendente | - | - | - |
| T-003 | Pendente | - | - | - |

**Valores de status**: `Pendente` | `Em Andamento` | `Revisão Pendente` | `Concluído` | `Falhou` | `Bloqueado`

---

## Gate de Aprovação

```
Este é o plano de execução para [NOME DA FEATURE].

Batches:
  Batch 1 (paralelo): T-001, T-002
  Batch 2 (sequencial): T-003

Estimativa total: [Xh]
Estimativa com paralelismo: [Xh]

Você aprova iniciar a implementação?
```

---

## Log de Retentativas

| Tarefa | Tentativa | Falhou Em | Motivo | Próxima Tentativa |
|--------|-----------|----------|--------|------------------|
| - | - | - | - | - |

---

## Notas

<!-- Notas de implementação, riscos ou dependências descobertas durante o planejamento -->

-
