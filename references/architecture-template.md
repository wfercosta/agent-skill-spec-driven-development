# Arquitetura: [NOME DO PROJETO]

> Estilo arquitetural, organização de camadas, fluxo de dados e decisões de design principais.

---

## Estilo Arquitetural

<!-- ex.: Layered (N-tier), Hexagonal, Event-Driven, Microsserviços, Monolito -->

[Descrever o estilo arquitetural e a justificativa por trás da escolha.]

---

## Organização de Camadas

```
[project-root]/
├── src/
│   ├── [camada-1]/       # ex.: domain — regras de negócio e entidades
│   ├── [camada-2]/       # ex.: application — casos de uso e orquestração
│   ├── [camada-3]/       # ex.: infrastructure — DB, filas, APIs externas
│   └── [camada-4]/       # ex.: interface — controllers HTTP, CLI, consumers
└── test/
    ├── unit/
    ├── integration/
    └── e2e/
```

---

## Fluxo de Dados

```
[Descrever o fluxo de requisição/evento do ponto de entrada à persistência e de volta]

Exemplo:
Requisição HTTP → Controller → Caso de Uso → Domínio → Repositório → Banco de Dados
                                             ↑
                                    Eventos de Domínio → Event Bus → Consumers
```

---

## Componentes Principais

| Componente | Camada | Responsabilidade |
|------------|--------|-----------------|
| [ex.: UserController] | Interface | Recebe HTTP, delega para caso de uso |
| [ex.: CreateUserUseCase] | Application | Orquestra domínio + persistência |
| [ex.: User] | Domain | Entidade com regras de negócio |
| [ex.: UserRepository] | Infrastructure | Adapter de persistência Prisma |

---

## Direção de Dependências

<!-- As dependências devem apontar apenas para dentro (em direção ao domínio). -->

```
Interface → Application → Domain ← Infrastructure
```

---

## Preocupações Transversais

| Preocupação | Abordagem |
|-------------|-----------|
| Tratamento de Erros | [ex.: Padrão Result / exceções] |
| Logging | [ex.: Pino com JSON estruturado] |
| Validação | [ex.: Zod na camada de interface] |
| Autenticação | [ex.: Middleware JWT] |
| Autorização | [ex.: RBAC na camada de aplicação] |

---

## Decisões Arquiteturais (Resumo ADR)

| ID | Decisão | Justificativa |
|----|---------|---------------|
| ADR-001 | [ex.: Hexagonal ao invés de MVC] | [motivo] |

---

## Restrições & Riscos

<!-- Limitações arquiteturais conhecidas ou débito técnico relacionado à arquitetura -->

-
