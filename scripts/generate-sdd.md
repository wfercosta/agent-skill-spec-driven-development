# Script: generate-sdd

> Gerar um Documento de Design de Software (SDD) por meio de refinamento iterativo com o usuário.

---

## Objetivo

Produzir uma especificação completa e sem ambiguidades para uma feature fazendo perguntas direcionadas para eliminar ambiguidades, e então gerar `spec.md`, `context.md` (se necessário) e `design.md` para a feature. Atualizar STATE.md para refletir `DESIGN_DEFINED`.

---

## Entradas

- Descrição da feature a partir do comando do usuário (ex.: `"create SDD for payment retry"`) OU
- Um arquivo de requisitos fornecido pelo usuário (ex.: `"generate SDD from requirements.md"`).
- Documentos existentes em `.specs/codebase/` (ARCHITECTURE.md, STACK.md, CONVENTIONS.md).

---

## Pré-condições

1. `.specs/codebase/` deve existir. Se não, instruir o usuário a executar a inicialização do projeto primeiro:
   ```
   O projeto ainda não foi inicializado.
   Execute: "init project" para começar.
   ```
2. Ler `.specs/STATE.md` para entender o estado atual.
3. Derivar o slug `feature-name` a partir da descrição do usuário (kebab-case, minúsculo, máx. 4 palavras).
   - Exemplo: `"payment retry logic"` → `payment-retry`
4. Verificar se `.specs/features/[feature-name]/` já existe.
   - Se sim, perguntar:
     ```
     Já existe uma SDD para "[feature-name]".
     Deseja atualizá-la ou criar uma nova versão?
     ```

---

## Passos

### Passo 1 — Extrair Descrição Inicial

Se um arquivo de requisitos foi referenciado, lê-lo. Caso contrário, extrair a descrição da feature do comando do usuário.

Resumir o entendimento de volta ao usuário:
```
Entendi que você deseja especificar: [descrição da feature].

Vou fazer algumas perguntas para detalhar a especificação e remover ambiguidades.
```

### Passo 2 — Perguntas de Esclarecimento

Fazer as seguintes perguntas. Adaptar ou pular perguntas que já estejam claramente respondidas pelo input do usuário ou pela documentação existente. Agrupar perguntas logicamente.

**Problema e Contexto:**
```
1. Qual problema específico essa funcionalidade resolve?
2. Quem são os usuários ou sistemas que utilizarão essa funcionalidade?
3. Existe alguma funcionalidade similar já implementada que devemos considerar ou reutilizar?
```

**Requisitos Funcionais:**
```
4. Quais são os comportamentos esperados do sistema (o que ele deve fazer)?
5. Quais são os casos de uso principais? Descreva o fluxo do ponto de vista do usuário.
6. Existem casos de uso alternativos ou fluxos de exceção importantes?
```

**Requisitos Não Funcionais:**
```
7. Existem requisitos de performance? (ex.: tempo de resposta, throughput)
8. Existem requisitos de segurança? (ex.: autenticação, autorização, criptografia)
9. Existe algum SLA ou requisito de disponibilidade?
10. A funcionalidade precisa ser escalável horizontalmente?
```

**Critérios de Aceitação:**
```
11. Como saberemos que a funcionalidade está correta e completa?
    Liste os critérios de aceitação mais importantes.
```

**Dependências e Restrições:**
```
12. Quais partes do sistema existente essa funcionalidade depende?
13. Existem integrações externas envolvidas?
14. Existem restrições técnicas (ex.: não pode usar X, deve manter compatibilidade com Y)?
15. Existem restrições de prazo ou de escopo?
```

**Riscos e Questões em Aberto:**
```
16. Quais são os principais riscos ou incertezas que você identifica nessa funcionalidade?
17. Existe alguma decisão de design que está em aberto e precisa ser tomada?
```

### Passo 3 — Refinamento Iterativo

Após receber as respostas:
1. Identificar quaisquer ambiguidades restantes ou informações conflitantes.
2. Fazer perguntas de acompanhamento apenas para itens não resolvidos — não reperguntar o que já foi respondido.
3. Repetir até que o agente tenha informação suficiente para escrever uma spec completa e sem ambiguidades.

Antes de escrever os documentos, confirmar:
```
Tenho as informações necessárias para gerar a SDD.

Resumo do que entendi:
- Funcionalidade: [resumo]
- Requisitos principais: [lista]
- Critérios de aceitação: [lista]
- Dependências: [lista]

Posso prosseguir com a geração dos documentos?
```

### Passo 4 — Criar Diretório da Feature

Criar o diretório: `.specs/features/[feature-name]/`

### Passo 5 — Gerar `spec.md`

Usando `references/sdd-template.md` como base, preencher todas as seções:
- Atribuir um ID de feature: `FEAT-[NNN]` (incrementar das features existentes).
- Atribuir IDs de requisito rastreáveis: `FEAT-[NNN]-REQ-001`, `FEAT-[NNN]-REQ-002`, etc.
- Preencher Requisitos Funcionais das respostas do Passo 2.
- Preencher Requisitos Não Funcionais das respostas do Passo 2.
- Preencher Critérios de Aceitação das respostas do Passo 2.
- Preencher Dependências e Restrições.
- Preencher Matriz de Rastreabilidade.
- Documentar quaisquer questões em aberto que permaneçam.

Escrever em: `.specs/features/[feature-name]/spec.md`

### Passo 6 — Gerar `context.md` (se necessário)

Criar este arquivo APENAS se houver decisões em área cinza, trade-offs ou suposições feitas durante o processo de especificação que não sejam óbvias a partir da spec em si.

O conteúdo deve incluir:
- Decisões principais tomadas e sua justificativa.
- Alternativas consideradas e por que foram rejeitadas.
- Suposições feitas por falta de informação.

Escrever em: `.specs/features/[feature-name]/context.md`

### Passo 7 — Gerar `design.md`

Executar `scripts/generate-design.md` com o spec.md gerado como entrada.

### Passo 8 — Atualizar `STATE.md`

Chamar `scripts/update-state.md` com:
```
Status: DESIGN_DEFINED
Feature: [feature-name]
Updated At: [timestamp atual]
```

---

## Saídas

```
.specs/features/[feature-name]/
├── spec.md
├── context.md    (somente se necessário)
└── design.md
```

`.specs/STATE.md` atualizado para `DESIGN_DEFINED`.

---

## Mensagem de Conclusão

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

## Tratamento de Erros

- Se o usuário não conseguir responder a perguntas críticas, documentar a lacuna como questão em aberto em spec.md e sinalizá-la na mensagem de conclusão.
- Nunca prosseguir para escrever spec.md com ambiguidades críticas não resolvidas — sempre perguntar.
- Se um arquivo de requisitos for referenciado mas não puder ser lido, informar o usuário e pedir que forneça os requisitos diretamente.
