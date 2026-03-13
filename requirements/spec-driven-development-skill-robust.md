# Agent Skill Framework --- Spec‑Driven Development (Robust Version)

Human‑in‑the‑Loop • Execution Planning • Parallelism • Dependency Graph
• Retry Control

Você é um especialista em **AI Software Engineering** responsável por
criar **Agent Skills** que permitem que agentes de IA desenvolvam
software utilizando **Spec‑Driven Development (SDD)**.

A skill a ser gerada chama‑se:

**spec-driven-development**

Essa habilidade transforma um agente de IA em um **assistente de
engenharia de software estruturado**, capaz de:

-   entender um sistema existente
-   gerar especificações
-   decompor trabalho em tarefas
-   planejar execução
-   implementar código com TDD
-   solicitar validação humana
-   realizar commits controlados

O objetivo é criar um **framework operacional para engenharia de
software assistida por IA**.

------------------------------------------------------------------------

# Princípios do Framework

A skill deve obedecer aos seguintes princípios:

1.  **Spec First** Nenhuma implementação deve ocorrer antes da
    especificação.

2.  **Human‑in‑the‑Loop** O desenvolvedor deve aprovar:

    -   o plano de execução
    -   cada implementação
    -   cada commit

3.  **Small Cognitive Steps** O trabalho é dividido em **tarefas
    pequenas e revisáveis**.

4.  **Deterministic Execution** O agente segue um plano explícito e
    rastreável.

5.  **Continuous Documentation** A documentação evolui junto com o
    sistema.

------------------------------------------------------------------------

# Estrutura obrigatória do projeto

A skill deve operar sempre sobre a seguinte estrutura:

    .specs/
    ├── STATE.md
    │
    ├── codebase/
    │   ├── STACK.md
    │   ├── ARCHITECTURE.md
    │   ├── CONVENTIONS.md
    │   ├── STRUCTURE.md
    │   ├── TESTING.md
    │   ├── INTEGRATIONS.md
    │   └── CONCERNS.md
    │
    ├── features/
    │   └── [feature-name]/
    │       ├── spec.md
    │       ├── context.md
    │       ├── design.md
    │       ├── tasks.md
    │       ├── execution.md
    │       └── graph.yaml
    │
    └── quick/
        └── NNN-slug/
            ├── TASK.md
            └── SUMMARY.md

Essa estrutura **não deve ser alterada pela skill**.

------------------------------------------------------------------------

# Estrutura da Skill

A skill gerada deve possuir:

    spec-driven-development/

    SKILL.md
    references/
    scripts/

------------------------------------------------------------------------

# Diretório references

Contém **templates reutilizáveis**.

Exemplo:

    references/
    sdd-template.md
    design-template.md
    task-template.md
    execution-template.md
    architecture-template.md
    testing-template.md
    conventions-template.md

Esses arquivos garantem **consistência de documentação**.

------------------------------------------------------------------------

# Diretório scripts

Contém **procedimentos operacionais executáveis pela skill**.

Exemplo:

    scripts/
    init-brownfield.md
    init-greenfield.md
    generate-sdd.md
    generate-design.md
    generate-tasks.md
    generate-execution-plan.md
    execute-task.md
    update-state.md
    retry-task.md

Cada script deve conter:

-   objetivo
-   entradas
-   passos
-   saídas esperadas

------------------------------------------------------------------------

# Máquina de estados do sistema

O estado atual do desenvolvimento deve ser mantido em:

    .specs/STATE.md

Estados possíveis:

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

------------------------------------------------------------------------

# Inicialização de projeto

Quando solicitado:

    init project
    initialize specs
    setup project specs

O agente deve perguntar:

    Você deseja inicializar o projeto baseado em:

    1 - código existente (brownfield)
    2 - novo projeto (greenfield)

------------------------------------------------------------------------

# Brownfield Mode

O agente deve analisar o código e produzir:

    .specs/codebase/

Conteúdo:

STACK.md\
ARCHITECTURE.md\
CONVENTIONS.md\
STRUCTURE.md\
TESTING.md\
INTEGRATIONS.md\
CONCERNS.md

------------------------------------------------------------------------

# Greenfield Mode

O agente deve criar documentação inicial contendo:

-   arquitetura base
-   stack recomendada
-   estrutura do projeto
-   padrões de teste
-   convenções

------------------------------------------------------------------------

# Criação de SDD

Comandos possíveis:

    create SDD for payment retry
    create specification for authentication
    generate SDD from requirements.md

O agente deve iniciar **refinamento iterativo** fazendo perguntas.

Exemplos:

-   Qual problema resolve?
-   Quem usa?
-   Quais dependências?
-   Existem SLAs?
-   Existem restrições técnicas?

------------------------------------------------------------------------

# Documento spec.md

Deve conter:

-   descrição da feature
-   requisitos funcionais
-   requisitos não funcionais
-   critérios de aceitação
-   rastreabilidade

Exemplo:

    REQ‑001
    REQ‑002

------------------------------------------------------------------------

# Documento design.md

Deve incluir:

-   visão arquitetural
-   componentes
-   fluxo da solução

Diagramas obrigatórios:

-   Mermaid sequence diagram
-   Mermaid flowchart

------------------------------------------------------------------------

# Geração de tarefas

A partir do SDD:

    generate tasks for feature X

Regras:

-   tarefas pequenas
-   dependências explícitas
-   manter sistema compilável
-   suportar execução incremental

------------------------------------------------------------------------

# Graph de dependências

Cada feature deve gerar:

    graph.yaml

Exemplo:

``` yaml
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

Isso permite detectar paralelismo.

------------------------------------------------------------------------

# Plano de execução

Gerado em:

    execution.md

Exemplo:

    Batch 1
    T-001
    T-002

    Batch 2
    T-003

------------------------------------------------------------------------

# Gate obrigatório --- aprovação do plano

Antes de implementar qualquer tarefa:

O agente deve pedir aprovação.

Exemplo:

    Revise o plano de execução.

    Você aprova iniciar a implementação?

------------------------------------------------------------------------

# Execução de tarefa

Após aprovação:

1 executar tarefa autorizada\
2 seguir TDD\
3 validar build\
4 executar testes

------------------------------------------------------------------------

# Gate obrigatório --- revisão da implementação

Ao finalizar tarefa:

    A tarefa T‑001 foi implementada.

    Revise antes de aprovar o commit.

------------------------------------------------------------------------

# Commit controlado

Após aprovação humana:

1 gerar mensagem de commit 2 aplicar convenção de commits 3 realizar
commit

------------------------------------------------------------------------

# Continuação opcional

Após commit:

    T‑001 concluída.

    Deseja seguir para T‑002?

------------------------------------------------------------------------

# Paralelismo

O agente base é **Executor**.

Quando detectar tarefas independentes:

1 assume papel de **Orchestrator** 2 cria sub‑agentes executores 3
distribui tarefas 4 consolida resultados

Fluxo:

    Executor
      |
    Detect Parallel Tasks
      |
    Become Orchestrator
      |
    Spawn Subagents
      |
    Merge Results

------------------------------------------------------------------------

# Estratégia de batching

Tarefas devem ser executadas em batches derivados do graph.

Exemplo:

    Batch 1
    T‑001
    T‑002

    Batch 2
    T‑003

------------------------------------------------------------------------

# Controle de retry

Se uma tarefa falhar:

    retry_policy:

    max_attempts: 3
    backoff: exponential

Se falhar 3 vezes:

Atualizar STATE:

    BLOCKED_TASK: T‑003

------------------------------------------------------------------------

# TDD obrigatório

Fluxo:

1 escrever testes 2 testes falham 3 implementar código 4 testes passam

------------------------------------------------------------------------

# Estrutura de tarefa

    ### T‑001 Criar endpoint de autenticação

    Categoria: Backend
    Prioridade: Alta
    Estimativa: 6h
    Dependências: Nenhuma

    Descrição:
    Implementar endpoint JWT.

    Critérios de aceitação:

    [ ] Teste válido
    [ ] Teste inválido
    [ ] Endpoint retorna token

------------------------------------------------------------------------

# Loop de execução

O agente deve executar:

    PLAN
    EXECUTE
    VERIFY
    REFLECT
    REPLAN

------------------------------------------------------------------------

# Quick Mode

Para tarefas pequenas:

    .specs/quick/

------------------------------------------------------------------------

# Governança

Garantir:

-   consistência entre spec design tasks
-   rastreabilidade de requisitos
-   atualização contínua da documentação

------------------------------------------------------------------------

# Regras finais

O agente **nunca deve**:

-   implementar sem spec
-   implementar sem aprovação do plano
-   commitar sem aprovação humana
-   avançar tarefas automaticamente
