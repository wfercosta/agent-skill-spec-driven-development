# Script: init-brownfield

> Analisar uma base de código existente e gerar o conjunto de documentação `.specs/codebase/`.

---

## Objetivo

Produzir um snapshot completo e preciso do sistema existente lendo a base de código, e então preencher todos os documentos em `.specs/codebase/` usando os templates de referência correspondentes. Ao final, o STATE.md deve refletir `CODEBASE_ANALYZED`.

---

## Entradas

- Código-fonte existente no diretório de trabalho atual.
- Opcionalmente: um `README.md`, `package.json`, `docker-compose.yml`, ou quaisquer arquivos de configuração na raiz.

---

## Pré-condições

1. Verificar se `.specs/` já existe.
   - Se existir, avisar o usuário:
     ```
     Um diretório .specs/ já existe neste projeto.
     Deseja sobrescrever os documentos existentes em .specs/codebase/?
     (Isso não afetará .specs/features/ ou .specs/quick/)
     ```
   - Aguardar confirmação explícita antes de prosseguir.
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

### Passo 2 — Exploração da Base de Código

Ler a base de código sistematicamente para coletar informações para cada documento. Ordem de leitura recomendada:

1. Arquivos de config na raiz: `package.json`, `tsconfig.json`, `pom.xml`, `go.mod`, `Cargo.toml`, `Gemfile`, etc.
2. `docker-compose.yml`, `Dockerfile`, `.env.example`, `Makefile`.
3. `README.md` ou qualquer documentação de nível superior.
4. Estrutura de diretórios (primeiros 3 níveis).
5. Arquivos-fonte de amostra de cada módulo/pacote principal (2–3 arquivos por módulo).
6. Arquivos de teste para entender os padrões de teste.
7. Configuração de CI/CD: `.github/workflows/`, `Jenkinsfile`, `.gitlab-ci.yml`.

### Passo 3 — Gerar `STACK.md`

Usando `references/stack-template.md` como base, preencher:
- Linguagem, runtime e gerenciador de pacotes com versões detectadas.
- Frameworks e bibliotecas principais dos arquivos de dependência.
- Bibliotecas de teste.
- Scripts de build extraídos da configuração do projeto.
- Componentes de infraestrutura identificados nos arquivos Docker/compose.
- Variáveis de ambiente de `.env.example` ou documentação.

Escrever em: `.specs/codebase/STACK.md`

### Passo 4 — Gerar `ARCHITECTURE.md`

Usando `references/architecture-template.md` como base, documentar:
- O estilo arquitetural (ex.: layered, hexagonal, MVC, microsserviços).
- Como o código-fonte é organizado em camadas ou módulos.
- O fluxo principal de dados pela aplicação.
- Componentes principais e suas responsabilidades.
- Regras de direção de dependência observadas.
- Preocupações transversais (logging, tratamento de erros, auth).

Escrever em: `.specs/codebase/ARCHITECTURE.md`

### Passo 5 — Gerar `CONVENTIONS.md`

Usando `references/conventions-template.md` como base, documentar:
- Padrões de nomenclatura de arquivos e diretórios observados.
- Convenções de nomenclatura de classes, funções, variáveis e constantes.
- Padrões de organização de imports.
- Abordagem de tratamento de erros.
- Convenção de mensagens de commit (verificar git log se acessível).
- Configurações de estilo de código dos arquivos de linter/formatter.
- Padrões a evitar com base em regras de linting existentes.

Escrever em: `.specs/codebase/CONVENTIONS.md`

### Passo 6 — Gerar `STRUCTURE.md`

Usando `references/structure-template.md` como base, documentar:
- Layout do diretório raiz.
- Detalhamento de módulos de `src/` (ou equivalente) com descrições.
- Organização do diretório `test/` ou de testes.
- Arquivos principais e seus propósitos.
- Regras de fronteira entre módulos observadas.

Escrever em: `.specs/codebase/STRUCTURE.md`

### Passo 7 — Gerar `TESTING.md`

Usando `references/testing-template.md` como base, documentar:
- Frameworks e bibliotecas de teste em uso.
- Como executar testes (comandos dos scripts do package ou Makefile).
- Organização dos arquivos de teste (co-located vs. diretório paralelo).
- Padrões de mock e stub observados.
- Configuração de cobertura se encontrada.
- Quaisquer convenções de TDD ou BDD existentes.

Escrever em: `.specs/codebase/TESTING.md`

### Passo 8 — Gerar `INTEGRATIONS.md`

Usando `references/integrations-template.md` como base, documentar:
- Serviços externos identificados (bancos de dados, message brokers, gateways de pagamento, provedores de email, armazenamento em nuvem, etc.).
- Métodos de autenticação usados para cada integração.
- Padrões de adapter ou wrapper observados.
- Substitutos para desenvolvimento local (serviços Docker, mocks).

Escrever em: `.specs/codebase/INTEGRATIONS.md`

### Passo 9 — Gerar `CONCERNS.md`

Usando `references/concerns-template.md` como base, documentar problemas observados:
- Áreas com cobertura de teste ausente ou baixa.
- Código complexo sem documentação.
- Dependências depreciadas.
- Comentários TODO/FIXME conhecidos no código.
- Potenciais vulnerabilidades de segurança (credenciais hardcoded, validação ausente).
- Gargalos de performance (padrões N+1, índices ausentes, payloads grandes).

Escrever em: `.specs/codebase/CONCERNS.md`

### Passo 10 — Inicializar `STATE.md`

Usando `references/state-template.md` como base, criar:

```
Status: CODEBASE_ANALYZED
Feature: -
Current Task: -
Updated At: [timestamp atual]
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
Inicialização brownfield concluída.

Os seguintes documentos foram gerados em .specs/codebase/:
- STACK.md
- ARCHITECTURE.md
- CONVENTIONS.md
- STRUCTURE.md
- TESTING.md
- INTEGRATIONS.md
- CONCERNS.md

Status atual: CODEBASE_ANALYZED

Próximos passos:
- Revise os documentos gerados e corrija qualquer imprecisão.
- Quando estiver pronto, crie uma SDD com: "create SDD for [nome da feature]"
```

---

## Tratamento de Erros

- Se a base de código for muito grande para ler completamente, priorizar pontos de entrada, arquivos de configuração e um arquivo representativo por módulo.
- Se um documento não puder ser totalmente preenchido por falta de informação, preencher o que for possível e adicionar um comentário `<!-- TODO: verificar -->` nas seções incertas.
- Nunca fabricar informações — se houver dúvida, documentar a incerteza explicitamente.
