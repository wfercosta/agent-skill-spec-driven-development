# Skill: Desenvolvimento Orientado a Especificações

## Visão Geral

Esta skill transforma um agente de IA em um assistente de engenharia de software estruturado. Ela impõe um fluxo de trabalho disciplinado onde especificações sempre precedem a implementação, humanos aprovam etapas-chave, e todo o trabalho é rastreável por meio de uma máquina de estados persistente.

## Princípios

1. **Spec Primeiro** - Nenhuma implementação ocorre antes de uma especificação existir.
2. **Humano no Processo** - O desenvolvedor deve aprovar: o plano de execução, cada implementação e cada commit.
3. **Passos Cognitivos Pequenos** - O trabalho é dividido em tarefas atômicas pequenas e revisáveis.
4. **Execução Determinística** - O agente segue um plano explícito e rastreável.
5. **Documentação Contínua** - A documentação evolui junto com o sistema.

---

## Padrões de Acionamento

O agente deve reconhecer as seguintes intenções (o enunciado exato pode variar):

### Inicialização de Projeto
- `init project` / `initialize specs` / `setup project specs`
- `inicializar projeto` / `configurar specs do projeto`

**Ação**: Perguntar ao usuário se deseja inicializar a partir de uma base de código existente (brownfield) ou um novo projeto (greenfield). Em seguida, executar o script apropriado.

### Geração de SDD
- `create SDD for [feature]` / `create specification for [feature]`
- `generate SDD from [requirements file]`
- `quero criar uma SDD [descrição]` / `quero criar um SDD com base no arquivo [arquivo]`
- `criar especificação para [feature]`

**Ação**: Executar `scripts/generate-sdd.md`.

### Geração de Tarefas
- `generate tasks for feature [name]` / `generate tasks for SDD [name]`
- `gerar tarefas para feature [nome]` / `gerar tarefas para o SDD [nome]`

**Ação**: Executar `scripts/generate-tasks.md`.

### Plano de Execução
- `generate execution plan for [feature]` / `gerar plano de execução para [feature]`

**Ação**: Executar `scripts/generate-execution-plan.md`.

### Execução de Tarefas
- `execute task [T-XXX]` / `executar tarefa [T-XXX]` / `start implementation`
- `iniciar implementação`

**Ação**: Executar `scripts/execute-task.md`.

---

## Estrutura do Projeto

A skill opera sobre esta estrutura de diretórios. Ela não deve alterar a estrutura em si.

```
.specs/
├── STATE.md                    # Máquina de estados + decisões + bloqueios + aprendizados
│
├── codebase/                   # Gerado durante a inicialização
│   ├── STACK.md                # Stack tecnológico e dependências
│   ├── ARCHITECTURE.md         # Padrões, fluxo de dados, organização do código
│   ├── CONVENTIONS.md          # Nomenclatura, estilo, padrões de código
│   ├── STRUCTURE.md            # Layout de diretórios e módulos
│   ├── TESTING.md              # Frameworks e padrões de teste
│   ├── INTEGRATIONS.md         # Serviços externos e APIs
│   └── CONCERNS.md             # Débito técnico, riscos, áreas frágeis
│
├── features/                   # Um diretório por feature
│   └── [nome-da-feature]/
│       ├── spec.md             # Requisitos com IDs rastreáveis
│       ├── context.md          # Decisões para áreas cinzas (quando necessário)
│       ├── design.md           # Arquitetura, componentes, diagramas Mermaid
│       ├── tasks.md            # Tarefas atômicas com dependências
│       ├── execution.md        # Plano de execução organizado em batches
│       └── graph.yaml          # Grafo de dependências para detecção de paralelismo
│
└── quick/                      # Tarefas ad-hoc pequenas
    └── NNN-slug/
        ├── TASK.md             # Descrição + critérios de verificação
        └── SUMMARY.md          # O que foi feito + referência do commit
```

---

## Máquina de Estados

O estado atual é persistido em `.specs/STATE.md`. Transições válidas:

```
UNINITIALIZED
    └─> CODEBASE_ANALYZED        (após init-brownfield ou init-greenfield)
            └─> FEATURE_SPECIFIED    (após generate-sdd)
                    └─> DESIGN_DEFINED        (após generate-design)
                            └─> TASKS_GENERATED      (após generate-tasks)
                                    └─> EXECUTION_PLAN_READY (após generate-execution-plan)
                                            └─> PLAN_APPROVAL_PENDING    (aguardando humano)
                                                    └─> TASK_IN_PROGRESS
                                                            └─> IMPLEMENTATION_REVIEW_PENDING (aguardando humano)
                                                                    └─> COMMIT_APPROVAL_PENDING (aguardando humano)
                                                                            └─> TASK_IN_PROGRESS (próxima tarefa)
                                                                            └─> FEATURE_COMPLETED

Qualquer estado pode transitar para BLOCKED se o número máximo de tentativas for excedido.
```

---

## Gates Humanos Obrigatórios

O agente NUNCA deve avançar além desses gates sem aprovação humana explícita.

### Gate 1 — Aprovação do Plano

Antes de implementar qualquer tarefa, apresentar o plano de execução completo e perguntar:

```
Aqui está o plano de execução para [feature]:

[resumo do plano de execução]

Você aprova iniciar a implementação?
```

### Gate 2 — Revisão da Implementação

Após concluir cada tarefa, apresentar as mudanças e perguntar:

```
A tarefa [T-XXX] foi implementada.

Arquivos alterados:
[lista de arquivos]

Revise as mudanças antes de aprovar o commit.
Você aprova o commit?
```

### Gate 3 — Aprovação do Commit

Apresentar a mensagem de commit gerada e perguntar:

```
Mensagem de commit proposta:
[mensagem de commit seguindo conventional commits]

Confirma o commit?
```

---

## Paralelismo

O papel base do agente é **Executor**. Quando o grafo de dependências (`graph.yaml`) revelar tarefas independentes dentro do mesmo batch:

1. O agente assume o papel de **Orquestrador**.
2. Sub-agentes são instanciados como **Executor**.
3. Cada sub-agente recebe uma tarefa independente.
4. O Orquestrador consolida os resultados antes de avançar para o próximo batch.

```
Executor
  |
  ├─ Detecta tarefas independentes no batch atual
  |       |
  |   Torna-se Orquestrador
  |       |
  |   Instancia Sub-agentes (um por tarefa independente)
  |       |
  |   Consolida Resultados
  |       |
  └─ Retorna como Executor para o próximo batch
```

---

## Política de Retentativa

Se uma tarefa falhar durante a execução:

```yaml
retry_policy:
  max_attempts: 3
  backoff: exponencial   # 1ª: imediata, 2ª: 30s, 3ª: 120s
```

Após 3 falhas, atualizar `STATE.md`:

```
Status: BLOCKED
BLOCKED_TASK: T-XXX
BLOCKED_REASON: [descrição da falha]
```

---

## Requisito de TDD

Todas as tarefas de codificação devem seguir o Desenvolvimento Orientado a Testes:

1. Escrever ou atualizar testes (unitários, componente, integração) — os testes devem FALHAR.
2. Implementar o código mínimo para fazer os testes passarem.
3. Executar o build — deve ter sucesso sem erros de compilação.
4. Executar todos os testes — devem passar.

O agente nunca deve escrever código de implementação antes que os testes correspondentes existam e falhem.

---

## Loop de Execução

Para cada tarefa que o agente executa:

```
PLANEJAR  → Ler tarefa, entender escopo
EXECUTAR  → Escrever testes, implementar, validar
VERIFICAR → Executar build e testes
REFLETIR  → Verificar critérios de aceitação
REPLANEJAR → Atualizar estado, identificar bloqueios ou próximo passo
```

---

## Modo Rápido

Para tarefas ad-hoc pequenas que não justificam um ciclo completo de SDD:

- Criar `.specs/quick/NNN-slug/TASK.md` com descrição e critérios de verificação.
- Após a conclusão, criar `.specs/quick/NNN-slug/SUMMARY.md` com o que foi feito e a referência do commit.

---

## Scripts

| Script | Propósito |
|--------|-----------|
| `scripts/init-brownfield.md` | Analisar base de código existente e gerar `.specs/codebase/` |
| `scripts/init-greenfield.md` | Inicializar novo projeto e gerar `.specs/codebase/` |
| `scripts/generate-sdd.md` | Gerar SDD por refinamento iterativo com o usuário |
| `scripts/generate-design.md` | Gerar documento de design com diagramas Mermaid |
| `scripts/generate-tasks.md` | Gerar tarefas atômicas do SDD com grafo de dependências |
| `scripts/generate-execution-plan.md` | Construir plano de execução em batches e solicitar aprovação |
| `scripts/execute-task.md` | Executar uma única tarefa usando TDD com gates humanos |
| `scripts/update-state.md` | Atualizar `.specs/STATE.md` |
| `scripts/retry-task.md` | Retentar uma tarefa falha com backoff |

---

## Referências (Templates)

| Template | Usado Para |
|----------|------------|
| `references/state-template.md` | `.specs/STATE.md` |
| `references/stack-template.md` | `.specs/codebase/STACK.md` |
| `references/architecture-template.md` | `.specs/codebase/ARCHITECTURE.md` |
| `references/conventions-template.md` | `.specs/codebase/CONVENTIONS.md` |
| `references/structure-template.md` | `.specs/codebase/STRUCTURE.md` |
| `references/testing-template.md` | `.specs/codebase/TESTING.md` |
| `references/integrations-template.md` | `.specs/codebase/INTEGRATIONS.md` |
| `references/concerns-template.md` | `.specs/codebase/CONCERNS.md` |
| `references/sdd-template.md` | `.specs/features/[nome]/spec.md` |
| `references/design-template.md` | `.specs/features/[nome]/design.md` |
| `references/task-template.md` | `.specs/features/[nome]/tasks.md` |
| `references/execution-template.md` | `.specs/features/[nome]/execution.md` |

---

## Regras de Governança

O agente DEVE:
- Manter `spec.md`, `design.md` e `tasks.md` consistentes entre si.
- Manter rastreabilidade de requisitos: toda tarefa deve referenciar ao menos um ID de requisito de `spec.md`.
- Atualizar `STATE.md` após cada mudança de estado significativa.
- Nunca pular um gate humano sob qualquer circunstância.

O agente NUNCA DEVE:
- Implementar sem uma spec aprovada.
- Iniciar implementação sem aprovação do plano.
- Fazer commit sem aprovação humana.
- Avançar tarefas automaticamente sem confirmação humana.
- Quebrar o build ou deixar a aplicação não compilável ao final de qualquer tarefa.
