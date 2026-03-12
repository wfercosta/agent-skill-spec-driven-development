# Script: execute-task

> Executar uma única tarefa seguindo TDD, apresentar a implementação para revisão humana e realizar um commit controlado após aprovação.

---

## Objetivo

Implementar uma tarefa do plano de execução aprovado. Seguir TDD estritamente. Após a implementação, apresentar o resultado ao humano para revisão. Mediante aprovação, fazer commit das mudanças com uma mensagem de conventional commit. Perguntar se deve continuar para a próxima tarefa.

---

## Entradas

- ID da tarefa (ex.: `T-001`).
- `.specs/features/[feature-name]/tasks.md` — definição da tarefa.
- `.specs/features/[feature-name]/execution.md` — plano de execução e rastreador de progresso.
- `.specs/features/[feature-name]/spec.md` — requisitos e critérios de aceitação.
- `.specs/features/[feature-name]/design.md` — decisões de design.
- `.specs/codebase/CONVENTIONS.md` — convenções de nomenclatura e código.
- `.specs/codebase/TESTING.md` — padrões de teste.

---

## Pré-condições

1. O plano de execução deve ter sido aprovado (STATE está em `TASK_IN_PROGRESS` ou aprovado pelo usuário).
2. Todas as tarefas listadas como dependências da tarefa alvo devem estar `Concluídas` em `execution.md`.
3. O build deve estar limpo antes de iniciar (executar build e testes para confirmar a baseline).

---

## Passos

### Passo 1 — Ler Definição da Tarefa

Ler a tarefa alvo de `tasks.md`. Extrair:
- Descrição.
- Passos de TDD e cenários de teste.
- Critérios de aceitação.
- Dependências (verificar se todas estão concluídas).

### Passo 2 — Atualizar STATE para `TASK_IN_PROGRESS`

Chamar `scripts/update-state.md`:
```
Status: TASK_IN_PROGRESS
Feature: [feature-name]
Current Task: [T-XXX]
Updated At: [timestamp atual]
```

Atualizar `execution.md` — marcar a tarefa como `Em Andamento` com timestamp de início.

### Passo 3 — TDD: Escrever Testes Primeiro (se tarefa de codificação)

Antes de escrever qualquer código de implementação:

1. Identificar ou criar o arquivo de teste no caminho especificado na definição da tarefa.
2. Escrever todos os casos de teste especificados nos passos de TDD da tarefa:
   - Teste(s) do happy path.
   - Teste(s) de erro/falha.
   - Teste(s) de caso extremo.
3. Executar os testes.
4. **Confirmar que FALHAM** — se algum teste passar antes da implementação, o teste está errado. Corrigir o teste antes de prosseguir.

Se NÃO for uma tarefa de codificação (ex.: documentação, apenas script de migração, config), pular para o Passo 5.

### Passo 4 — Implementar o Código

Com os testes falhando em posição, escrever o código de produção mínimo necessário para fazer os testes passarem:

- Seguir o design de `design.md`.
- Seguir as convenções de nomenclatura de `CONVENTIONS.md`.
- Não escrever mais código do que o necessário para satisfazer os testes.
- Não implementar comportamento de outras tarefas.

Após a implementação:
1. Executar os testes desta tarefa — **todos devem passar**.
2. Executar a suite completa de testes — **nenhuma regressão permitida**.
3. Executar o build — **deve compilar sem erros**.

Se algum teste falhar ou o build quebrar, corrigir o problema antes de prosseguir. NÃO pular ou comentar testes falhando.

### Passo 5 — Verificar Critérios de Aceitação

Ler os critérios de aceitação da definição da tarefa. Para cada critério:
- Verificar se está satisfeito pela implementação.
- Se um critério não for atendido, continuar a implementação até que seja.
- Verificar se algum requisito de `spec.md` que esta tarefa rastreia agora está satisfeito.

### Passo 6 — Atualizar `execution.md`

Marcar a tarefa como `Revisão Pendente` no rastreador de progresso.

### Passo 7 — Atualizar `STATE.md` para `IMPLEMENTATION_REVIEW_PENDING`

Chamar `scripts/update-state.md`:
```
Status: IMPLEMENTATION_REVIEW_PENDING
Feature: [feature-name]
Current Task: [T-XXX]
Updated At: [timestamp atual]
```

### Passo 8 — Apresentar Implementação para Revisão (GATE OBRIGATÓRIO)

Apresentar um resumo de todas as mudanças feitas e solicitar revisão humana:

```
A tarefa [T-XXX]: "[título da tarefa]" foi implementada.

Arquivos criados/modificados:
  + [caminho do novo arquivo]
  ~ [caminho do arquivo modificado]
  - [caminho do arquivo deletado]

Resultados dos testes:
  ✓ [N] testes passando
  ✗ 0 falhas

Build: OK

Critérios de aceitação:
  [X] [Critério 1]
  [X] [Critério 2]

Revise as mudanças e me informe se aprova o commit.
```

Aguardar aprovação explícita antes de fazer commit.

### Passo 9 — Lidar com a Resposta de Revisão

**Se aprovado:** prosseguir para o Passo 10.

**Se mudanças forem solicitadas:**
- Aplicar as mudanças solicitadas.
- Re-executar testes e build.
- Retornar ao Passo 8 com o resumo atualizado.

**Se rejeitado inteiramente:**
- Perguntar ao usuário o que deve ser feito de forma diferente.
- Reverter ou retrabalhar conforme instruído.
- Retornar ao Passo 3 ou Passo 4 conforme apropriado.

### Passo 10 — Atualizar STATE para `COMMIT_APPROVAL_PENDING`

Chamar `scripts/update-state.md`:
```
Status: COMMIT_APPROVAL_PENDING
Feature: [feature-name]
Current Task: [T-XXX]
Updated At: [timestamp atual]
```

### Passo 11 — Gerar Mensagem de Commit

Gerar uma mensagem de commit seguindo a especificação Conventional Commits:

```
<tipo>(<escopo>): <resumo curto>

<corpo opcional: o que foi feito e por quê>

Refs: [FEAT-XXX-REQ-NNN], T-XXX
```

Tipos: `feat`, `fix`, `test`, `refactor`, `docs`, `chore`, `perf`

Apresentar ao usuário:
```
Mensagem de commit proposta:

[mensagem de commit]

Confirma o commit?
```

### Passo 12 — Realizar Commit

Após confirmação:
1. Fazer stage de todos os arquivos relevantes (`git add`).
2. Fazer commit com a mensagem aprovada.
3. Confirmar o hash do commit ao usuário.

### Passo 13 — Atualizar `execution.md` e `tasks.md`

- Marcar a tarefa como `Concluída` com timestamp e hash do commit em `execution.md`.
- Marcar o checkbox da tarefa como completo em `tasks.md`.

### Passo 14 — Atualizar `STATE.md`

Verificar se todas as tarefas da feature estão concluídas:
- **Se houver mais tarefas**: definir o estado de volta para `TASK_IN_PROGRESS`.
- **Se todas as tarefas estiverem concluídas**: definir o estado para `FEATURE_COMPLETED`.

Chamar `scripts/update-state.md` conforme apropriado.

### Passo 15 — Perguntar para Continuar

```
[T-XXX] concluída e commitada.

[Se houver próxima tarefa:]
Próxima tarefa: [T-YYY] — "[título da próxima tarefa]"
Dependências satisfeitas: Sim

Deseja continuar com [T-YYY]?

[Se todas as tarefas estiverem concluídas:]
Todas as tarefas da feature "[feature-name]" foram concluídas.
Status: FEATURE_COMPLETED
```

---

## Saídas

- Código implementado no projeto.
- `execution.md` atualizado (tarefa marcada como Concluída).
- `tasks.md` atualizado (checkbox da tarefa marcado).
- `STATE.md` atualizado.
- Um git commit com mensagem de conventional commit.

---

## Invariantes de TDD

As seguintes regras NUNCA devem ser violadas:

1. Os testes devem ser escritos ANTES do código de implementação.
2. Os testes devem FALHAR antes da implementação (fase vermelha).
3. Os testes devem PASSAR após a implementação (fase verde).
4. O build deve estar limpo após cada tarefa.
5. Nenhum teste pode ser pulado, comentado ou marcado como todo para fazer a suite passar.

---

## Tratamento de Erros

- Build falha após implementação: corrigir o erro de compilação antes de apresentar para revisão. Nunca apresentar código quebrado.
- Testes falham após implementação: depurar e corrigir. Nunca deixar testes falhando.
- A implementação quebra testes não relacionados (regressão): corrigir a regressão antes de prosseguir, mesmo que isso requeira retrabalhar a abordagem da tarefa.
- Aprovação do usuário não é dada após 3 ciclos de revisão: escalar para o usuário com um resumo dos pontos de atrito e perguntar como prosseguir.
