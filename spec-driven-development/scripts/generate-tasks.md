# Script: generate-tasks

> Decompor um SDD de feature em tarefas atômicas e implementáveis com dependências explícitas e oportunidades de paralelismo.

---

## Objetivo

Produzir um `tasks.md` e `graph.yaml` completos para uma feature quebrando o SDD nas menores tarefas possíveis que deixem a aplicação em estado compilável. Cada tarefa de codificação deve seguir o padrão TDD. Atualizar STATE.md para `TASKS_GENERATED`.

---

## Entradas

- `.specs/features/[feature-name]/spec.md`
- `.specs/features/[feature-name]/design.md`
- `.specs/features/[feature-name]/context.md` (se existir)
- `.specs/codebase/ARCHITECTURE.md`
- `.specs/codebase/TESTING.md`
- `.specs/codebase/CONVENTIONS.md`

---

## Pré-condições

1. `spec.md` e `design.md` devem existir para a feature alvo.
2. Ler todos os documentos de entrada antes de começar.
3. `STATE.md` deve estar em `DESIGN_DEFINED` ou posterior.

---

## Passos

### Passo 1 — Ler e Internalizar Todas as Entradas

Ler `spec.md`, `design.md`, `context.md` e os documentos da base de código.

Extrair:
- Todos os critérios de aceitação de `spec.md`.
- Todos os componentes de `design.md`.
- Mudanças no modelo de dados.
- Contratos de API.
- Requisitos de tratamento de erros.

### Passo 2 — Identificar Áreas de Trabalho

Agrupar o trabalho em áreas lógicas com base no design:

1. **Infraestrutura / Setup** — scaffolding, config, migrações, novos módulos.
2. **Domínio** — entidades, value objects, regras de negócio.
3. **Aplicação** — casos de uso, orquestração.
4. **Adapters de Infraestrutura** — repositórios, clientes de serviços externos, publicadores de eventos.
5. **Interface** — controllers HTTP, comandos CLI, consumidores de eventos.
6. **Testes** — testes de integração, testes e2e (testes unitários fazem parte de cada tarefa de codificação).
7. **Documentação** — atualizações dos docs da base de código se a arquitetura mudar.

### Passo 3 — Decompor em Tarefas Atômicas

Para cada área de trabalho, criar a menor tarefa possível que:
- Tenha uma única responsabilidade clara.
- Deixe a aplicação em um **estado compilável e executável** quando concluída.
- Tenha critérios de aceitação mensuráveis.
- Possa ser revisada de forma isolada.

**Regras de decomposição:**
- Uma tarefa por entidade ou value object.
- Uma tarefa por caso de uso.
- Uma tarefa por interface de repositório + implementação.
- Uma tarefa por endpoint HTTP ou comando CLI.
- Uma tarefa por adapter de integração externa.
- Uma tarefa para migrações de banco de dados (separada da criação de entidades).
- Uma tarefa para testes de integração/e2e se abrangerem múltiplos componentes.

**Numeração das tarefas:** `T-001`, `T-002`, ..., `T-NNN` (sequencial dentro da feature).

### Passo 4 — Aplicar Padrão TDD às Tarefas de Codificação

Para cada tarefa que envolva escrever, modificar ou deletar código:

Marcá-la como exigindo TDD e estruturar os passos de TDD:

```
Passos TDD:
1. Escrever testes em [caminho do arquivo de teste] cobrindo:
   - [Cenário de teste 1: happy path]
   - [Cenário de teste 2: caso de erro]
   - [Cenário de teste N: caso extremo]
2. Confirmar que os testes falham.
3. Implementar [código específico].
4. Confirmar que os testes passam.
5. Executar a suite completa de testes e o build.
```

Os caminhos dos arquivos de teste devem seguir as convenções de teste do projeto em `TESTING.md`.

### Passo 5 — Identificar Dependências

Para cada tarefa, determinar quais outras tarefas devem estar completas antes que ela possa iniciar:

- Uma tarefa de caso de uso depende da tarefa de entidade de domínio.
- Uma tarefa de controller HTTP depende da tarefa de caso de uso.
- Uma tarefa de implementação de repositório depende da tarefa de interface de domínio.
- Uma tarefa de migração que é necessária para um caso de uso funcionar cria uma dependência.
- Tarefas que são independentes têm `depends_on: []`.

### Passo 6 — Identificar Oportunidades de Paralelismo

Marcar grupos de tarefas que podem ser executados em paralelo (tarefas sem dependência entre si):

```
# Exemplo de grupos paralelos:
Grupo Paralelo A: T-001 (entidade de domínio), T-002 (migração)
Grupo Paralelo B: T-003 (caso de uso), T-004 (impl. repositório) — após T-001 e T-002
```

Adicionar uma nota a cada tarefa independente: `Pode paralelizar: Sim`.

### Passo 7 — Escrever `tasks.md`

Usando `references/task-template.md` como base, escrever cada tarefa com:
- Descrição completa do que deve ser feito.
- Passos de TDD (para tarefas de codificação).
- Critérios de aceitação rastreáveis aos requisitos de `spec.md`.
- Dependências listadas explicitamente.

Escrever em: `.specs/features/[feature-name]/tasks.md`

### Passo 8 — Escrever `graph.yaml`

Gerar um grafo de dependências YAML:

```yaml
tasks:
  T-001:
    title: "[título]"
    depends_on: []
    can_parallelize: true

  T-002:
    title: "[título]"
    depends_on: []
    can_parallelize: true

  T-003:
    title: "[título]"
    depends_on:
      - T-001
      - T-002
    can_parallelize: false
```

Escrever em: `.specs/features/[feature-name]/graph.yaml`

### Passo 9 — Validar Conjunto de Tarefas

Antes de escrever os arquivos, verificar:

- [ ] Cada critério de aceitação de `spec.md` é coberto por ao menos uma tarefa.
- [ ] Nenhuma tarefa deixa a aplicação em estado quebrado/não compilável.
- [ ] Todas as tarefas TDD têm caminhos de arquivos de teste concretos e cenários de teste.
- [ ] As dependências formam um DAG válido (sem dependências circulares).
- [ ] As tarefas são pequenas o suficiente para serem revisadas em uma única sessão (máx. ~8h de estimativa por tarefa).
- [ ] Cada tarefa é rastreável a ao menos um ID de requisito de `spec.md`.

### Passo 10 — Atualizar `STATE.md`

Chamar `scripts/update-state.md` com:
```
Status: TASKS_GENERATED
Feature: [feature-name]
Updated At: [timestamp atual]
```

---

## Saídas

- `.specs/features/[feature-name]/tasks.md`
- `.specs/features/[feature-name]/graph.yaml`
- `.specs/STATE.md` atualizado para `TASKS_GENERATED`

---

## Mensagem de Conclusão

```
Tarefas geradas para "[feature-name]".

Total de tarefas: [N]
Tarefas paralelas identificadas: [N] (em [N] grupos)
Estimativa total: ~[Xh]

Documentos criados:
- .specs/features/[feature-name]/tasks.md
- .specs/features/[feature-name]/graph.yaml

Status atual: TASKS_GENERATED

Próximos passos:
- Revise as tarefas e ajuste estimativas ou dependências.
- Para gerar o plano de execução, execute: "generate execution plan for [feature-name]"
```

---

## Tratamento de Erros

- Se `spec.md` ou `design.md` estiverem incompletos, não prosseguir — informar o usuário das informações ausentes e pedir que complete os documentos primeiro.
- Se uma tarefa deixar a aplicação em estado quebrado, dividi-la em uma tarefa menor que mantenha a compilabilidade.
- Se dependências circulares forem detectadas no grafo, resolvê-las dividindo ou reordenando as tarefas e sinalizar o problema ao usuário.
