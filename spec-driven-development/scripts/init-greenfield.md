# Script: init-greenfield

> Inicializar um novo projeto gerando a documentação base de `.specs/codebase/` como blueprint para o desenvolvimento.

---

## Objetivo

Guiar o usuário na definição do stack, arquitetura, convenções e estrutura do projeto antes de qualquer código ser escrito. Produzir todos os documentos em `.specs/codebase/` com base nas respostas do usuário e boas práticas de engenharia. Ao final, STATE.md deve refletir `CODEBASE_ANALYZED`.

---

## Entradas

- Descrição e objetivos do projeto fornecidos pelo usuário.
- Respostas às perguntas de esclarecimento (coletadas durante este script).

---

## Pré-condições

1. Verificar se `.specs/` já existe.
   - Se existir, avisar o usuário:
     ```
     Um diretório .specs/ já existe.
     Deseja recriar os documentos de codebase para um projeto greenfield?
     Isso substituirá o conteúdo atual de .specs/codebase/.
     ```
   - Aguardar confirmação antes de prosseguir.
2. Se `.specs/` não existir, criar a estrutura completa de diretórios silenciosamente.

---

## Passos

### Passo 1 — Configuração de Diretórios

Criar os seguintes diretórios se não existirem:

```
.specs/
.specs/codebase/
.specs/features/
.specs/quick/
```

### Passo 2 — Coletar Informações do Projeto

Fazer as seguintes perguntas ao usuário. Aguardar respostas completas antes de prosseguir. Pode fazer todas de uma vez ou em fluxo conversacional.

```
Vou fazer algumas perguntas para criar o blueprint do seu projeto.

1. Qual é o objetivo principal do projeto?
   (Ex.: API REST para e-commerce, CLI para automação de infraestrutura, SaaS de gestão de tarefas)

2. Qual linguagem e runtime você deseja usar?
   (Ex.: TypeScript + Node.js, Go, Python, Java + Spring Boot)

3. Qual é o tipo de aplicação?
   (Ex.: API REST, GraphQL API, CLI, worker/job processor, fullstack web, biblioteca)

4. Você tem preferências de framework?
   (Ex.: Fastify, NestJS, Express, Hono — ou sem preferência)

5. Qual banco de dados será utilizado?
   (Ex.: PostgreSQL, MongoDB, SQLite — ou ainda não definido)

6. Existem integrações externas já previstas?
   (Ex.: serviço de email, pagamentos, armazenamento de arquivos, autenticação terceirizada)

7. Quais são as principais preocupações técnicas?
   (Ex.: alta disponibilidade, baixa latência, segurança de dados, escalabilidade horizontal)

8. Você tem alguma convenção de código ou padrão de equipe já definido?
   (Ex.: Conventional Commits, ESLint Airbnb, testes obrigatórios)
```

### Passo 3 — Gerar `STACK.md`

Com base nas respostas do usuário, produzir um STACK.md usando `references/stack-template.md` com:
- Linguagem, runtime e gerenciador de pacotes recomendados.
- Frameworks sugeridos com justificativa.
- Bibliotecas de teste adequadas ao stack escolhido.
- Scripts de build adequados ao tipo de projeto.
- Componentes de infraestrutura com base nas necessidades declaradas.
- Variáveis de ambiente necessárias (conjunto inicial).

Escrever em: `.specs/codebase/STACK.md`

### Passo 4 — Gerar `ARCHITECTURE.md`

Usando `references/architecture-template.md`, definir:
- Estilo arquitetural recomendado com justificativa (ex.: Hexagonal para API com múltiplas integrações, Layered simples para um serviço CRUD básico).
- Organização inicial de camadas/módulos.
- Fluxo de dados esperado pelo sistema.
- Componentes principais e suas responsabilidades (conjunto inicial, a ser expandido conforme as features são definidas).
- Estratégia de preocupações transversais.

Escrever em: `.specs/codebase/ARCHITECTURE.md`

### Passo 5 — Gerar `CONVENTIONS.md`

Usando `references/conventions-template.md`, definir:
- Convenções de nomenclatura de arquivos e diretórios.
- Convenções de nomenclatura de código.
- Ordem de imports.
- Estratégia de tratamento de erros.
- Convenção de mensagens de commit.
- Configuração de estilo de código.
- Padrões explicitamente proibidos neste projeto.

Escrever em: `.specs/codebase/CONVENTIONS.md`

### Passo 6 — Gerar `STRUCTURE.md`

Usando `references/structure-template.md`, definir:
- Layout de diretório raiz proposto.
- Detalhamento de módulos/pacotes com descrições.
- Organização do diretório de testes.
- Arquivos principais a serem criados (pontos de entrada, config, etc.).
- Regras de fronteira entre módulos.

Escrever em: `.specs/codebase/STRUCTURE.md`

### Passo 7 — Gerar `TESTING.md`

Usando `references/testing-template.md`, definir:
- Estratégia de testes (unitário, integração, e2e — o que está no escopo).
- Frameworks e bibliotecas de teste selecionados.
- Comandos de teste.
- Organização dos arquivos de teste.
- Fluxo de TDD a ser seguido (obrigatório para todas as tarefas de codificação).
- Metas de cobertura.

Escrever em: `.specs/codebase/TESTING.md`

### Passo 8 — Gerar `INTEGRATIONS.md`

Usando `references/integrations-template.md`, documentar:
- Todas as integrações externas identificadas no Passo 2.
- Padrão de adapter a ser aplicado para cada uma.
- Substitutos para desenvolvimento local (serviços Docker, mocks, contas sandbox).

Se nenhuma integração foi mencionada, criar um documento mínimo indicando que todas as integrações futuras devem seguir o padrão de adapter.

Escrever em: `.specs/codebase/INTEGRATIONS.md`

### Passo 9 — Gerar `CONCERNS.md`

Usando `references/concerns-template.md`, documentar:
- Riscos conhecidos para este tipo de projeto (ex.: preocupações de segurança para sistemas de auth, desafios de consistência para sistemas distribuídos).
- Áreas a observar durante a implementação.
- Quaisquer restrições mencionadas pelo usuário.

Escrever em: `.specs/codebase/CONCERNS.md`

### Passo 10 — Inicializar `STATE.md`

Usando `references/state-template.md`, criar:

```
Status: CODEBASE_ANALYZED
Feature: -
Current Task: -
Updated At: [timestamp atual]

Decisions:
- [YYYY-MM-DD] Stack selecionado: [linguagem + framework] — [justificativa breve]
- [YYYY-MM-DD] Arquitetura: [estilo] — [justificativa breve]
```

Escrever em: `.specs/STATE.md`

---

## Saídas

```
.specs/
├── STATE.md                    (Status: CODEBASE_ANALYZED)
└── codebase/
    ├── STACK.md
    ├── ARCHITECTURE.md
    ├── CONVENTIONS.md
    ├── STRUCTURE.md
    ├── TESTING.md
    ├── INTEGRATIONS.md
    └── CONCERNS.md
```

---

## Mensagem de Conclusão

Após todos os arquivos serem escritos, informar ao usuário:

```
Blueprint do projeto gerado com sucesso.

Os seguintes documentos foram criados em .specs/codebase/:
- STACK.md
- ARCHITECTURE.md
- CONVENTIONS.md
- STRUCTURE.md
- TESTING.md
- INTEGRATIONS.md
- CONCERNS.md

Status atual: CODEBASE_ANALYZED

Próximos passos:
- Revise os documentos e ajuste conforme necessário.
- Quando estiver pronto, crie a primeira SDD com: "create SDD for [nome da feature]"
```

---

## Tratamento de Erros

- Se o usuário não conseguir responder a uma pergunta, prosseguir com um padrão razoável e documentar a suposição no arquivo relevante.
- Se escolhas conflitantes forem feitas (ex.: um framework incompatível com a linguagem escolhida), sinalizar o conflito e pedir esclarecimento antes de gerar os documentos.
- Sempre documentar suposições e decisões em aberto explicitamente nos arquivos gerados.
