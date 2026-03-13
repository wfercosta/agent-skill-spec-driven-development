Você é um especialista na criação de agent skills para agentes. O seu trabalho é gerar uma habildiade chamada de spec-driven-development, onde além de gerar o SKILL.md você também irá gerar ```references``` e ```scripts``` quando aplicável.

Esta habildiade terá algumas funções que pdoeram ser acionadas pelo usuário. Essas funções são:

## Inciailiação de um projeto

- Na inicialização de um projeto o usuário sempre deve ser questioando se ele pretende inicializar a base de código atual ou se ele quer incializar com base em definições de um novo projeto. Dependendo da resposta a habilidade deverá seguir por caminhos diferentes.

- Para incializar com base na base de código atual, deve se analisado a base de código e depois gerar os coumentos que serão armazenados nos diretórios ```.specs/codebase```. Para cada docuemento previsto previsto nesta geração o ideal é epara cada um deles escrever um arquivo de referência no diretório de ```references``` da habilidade para que possa ser carregado sob demanda. Abordando um modo de brownfield.
- Para inicializar um projeto novo, deve ser criado os mesmos documentos, mas com base em referências que vão orientar o padrão de desenvolvimento do projet, estrutura e e decisções de design, com base uma visão como se fosse a partir de um blueprint. Esses documentos serão armazenados no diretório ```.specs/codebase```. Abordando um modo de greenfield.

## Geração de especificações detalhadas

- Gerar especificações detalhadas para uma nova funcionalidade, cirando documentos necessários no diretŕoio ```.specs/features/[feature-name]```. Para cada documento previsto o dieal é escrever um arquivo de referência no diretŕoio ```references``` da habilidade para que possa ser carregado sob demanda. Para geração das especificaçẽos o usário poderá entrar com um comando como "Quero criar uma SDD [descrição da funcionalidade]"  ou "Quero criar um SDD com base no arquivo de requisitos [nome do arquivo]". Onde SDD seria Software Design Document. Deve ser considerado que o usário pode fazer variações destes prompots/ comandos, então a habilidade deve ser capaz de interpretar as inteções do usuário e agir de acordo. Na criação do SDD, deve fazer perguntas para o usuário para obter informaçẽos adicionais e remover ambiguidades, quanto sobre a funcionalidade, como requisitos específicos, critérios de aceitação, dependências, requisitos não funcionais, requisitos funcionais,  e quais quer outras informações que seja relevantes que possa ajudar na remoção das ambiguidades e detalhamento da especificação. Como parte do processo de criação do SDD, a ahbildade deve gerar um documento de design com a visão do design da funcionalidade (1 diagrama de sequencia e 1 diagra flowchart) elaborado em um diagrama marmaid que deve ser armazenado no diretório ```.specs/feature/[feature-name]/design.md```. A especificação deve ser gerada em ```.specs/feature/[feature-name]/spec.md``

## Geração de tarefas atômicas
- Gerar tarefas para iimplementção da funcionalidade, criando um documento de tarefas no diretório ```.specs/feature/[feature-name]/tasks.md```. As tarefas devem ser detalhadas e origanizadas de forma lógica, levando em consideração as dependências entre elas e nunca uma tarefa deve finalizar deixando a aplicação com falhas de compilação ou não executável. O objetivo é fornecer uma visão clara do que precisa ser feito para implementaar a funcionalidade, facilitando o planejamento e execução do trabalho. Deve ser considerado queo usário pode solicitar a geração de tarefas atômicas me diferentes momentos do processo de desenvolvimento, por exempl o, após a criação de um SDD ou durante a fase de implementação. A ahblidade de ve ser capaz de interpretar a inteção do usuário e agir de acordo, gerando as tarefas atomics conforme necessário para apoiar o progresso do projeto. Adicionalmente, deve ser considerado que algumas atividades possam ser executadoas em paralelocom uso de subagentes entação a ahblidade deve ser capaz de indentificar estas oprotunidades e organizar as tarefas de forma otimizar o fluxo de trabalho e o agente conseguir identificar que é opossível executar em modo subagente. Também deve ser considerado como comandos do usuário "gerar tarefas para feature [nome da feature]" ou "gerar tarefas para o SDD [nome do SDD ou feature]", então a habilidade deve ser capas de interpretar as inteções do usuário  e agir de acordo. A geração das tarefs deve seguir um template definido que incluir informaçẽos como descrição da tarefa, critéios de aceitação, dependências e estimativa de tempo. O objetivo é fornecer uma visão clara do que precisa ser feito para implementar a funcionaldiade, facilitando o planejamento e a execução do trabalho. Também garanta que a tarefa, quando for uma tarefa de codificação, que seja usado um modelo de TDD (Test Driven Development) garantido que o agente primeiro irá criar, modificar ou remove testes de unidade, componente e ingteração, antes de implementar a funcionalidade efetivamente, grantido que o software ser á construido com um viés de ser testável.


## Orientações gerais

Todos os documentos serão produzidos pela hablidade devem serguir um template pré-definido, garantindo a consistência e clarezaa das informações produizdas através de múltiplas execuções. Os templates devem incluir sesçẽos específicas para cada tipo de doucmento, como requisitos, critérios de aceitação, deisgn, tarefas, entre outros.  O objetivo é fornecer uma estrutura clara e orgnanziada de informações, facilitando a compreenção e o uso dos documentos gerados pela habilidade

Estuturura dos diretŕios para a especificação

```
.specs/
├── STATE.md            # Persistent memory: decisions, blockers, learnings, todos, deferred ideas
│
├── codebase/           # Brownfield analysis (existing projects only)
│   ├── STACK.md        # Technology stack and dependencies
│   ├── ARCHITECTURE.md # Patterns, data flow, code organization
│   ├── CONVENTIONS.md  # Naming, style, coding patterns
│   ├── STRUCTURE.md    # Directory layout and modules
│   ├── TESTING.md      # Test frameworks and patterns
│   ├── INTEGRATIONS.md # External services and APIs
│   └── CONCERNS.md     # Tech debt, risks, fragile areas
│
├── features/           # Feature specifications
│   └── [feature-name]/
│       ├── spec.md     # Requirements with traceable IDs (FEAT-01, AUTH-02...)
│       ├── context.md  # User decisions for gray areas (only when needed)
│       ├── design.md   # Architecture and components (only for large/complex)
│       └── tasks.md    # Atomic tasks with dependencies (only for large/complex)
│
└── quick/              # Ad-hoc tasks (quick mode)
    └── NNN-slug/
        ├── TASK.md     # Description + verification
        └── SUMMARY.md  # What was done + commit

```

Exemplo de uma estruturade uma tarefa
 
```
### [X] T-001: Bootstrap do projeto CLI (Node LTS + TypeScript + Estrutura base)
- **Categoria**: Infraestrutura
- **Prioridade**: Alta
- **Estimativa**: 12h
- **Dependências**: Nenhuma
- **Concluída em**: 2026-02-15
- **Descrição**: 
  - Criar estrutura `app/src` e organização por módulos (cli/iu/domain/infra)
  - Configurar TypesScript (tsconfig), build e start
  - Configurar Commander.js com entrpoint e `--help`
  - Definir opções globais `-v, --verbose` no root do programa
  - Configurar padrões de output (picocolors) e código de saída
- **Critérios de aceitação**:
  - [X] `npx @im9/ia --help` funciona e lista comandos (stub)
  - [X] `--verbose` hablita flag global disponível em todos os comandos (mesmo que ainda sem logs)
  - [x] Build typescript executa sem erros
```
