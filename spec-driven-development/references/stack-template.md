# Stack: [NOME DO PROJETO]

> Stack tecnológico, dependências principais e decisões de ferramental.

---

## Linguagem & Runtime

| Item | Versão | Notas |
|------|--------|-------|
| Linguagem | [ex.: TypeScript 5.x] | |
| Runtime | [ex.: Node.js 22 LTS] | |
| Gerenciador de Pacotes | [ex.: pnpm 9.x] | |

---

## Frameworks & Bibliotecas

### Core

| Biblioteca | Versão | Propósito |
|------------|--------|-----------|
| [ex.: Express] | [v] | Servidor HTTP |
| [ex.: Prisma] | [v] | ORM |

### Testes

| Biblioteca | Versão | Propósito |
|------------|--------|-----------|
| [ex.: Vitest] | [v] | Testes unitários e de integração |
| [ex.: Supertest] | [v] | Testes de integração HTTP |

### Ferramental

| Ferramenta | Versão | Propósito |
|------------|--------|-----------|
| [ex.: ESLint] | [v] | Linting |
| [ex.: Prettier] | [v] | Formatação |
| [ex.: tsx / ts-node] | [v] | Runner de desenvolvimento |

---

## Build & Scripts

```json
{
  "scripts": {
    "build": "[comando de build]",
    "start": "[comando de start]",
    "dev": "[comando de dev]",
    "test": "[comando de teste]",
    "test:watch": "[comando de watch]",
    "lint": "[comando de lint]"
  }
}
```

---

## Infraestrutura

| Componente | Tecnologia | Notas |
|------------|------------|-------|
| Banco de Dados | [ex.: PostgreSQL 16] | |
| Cache | [ex.: Redis 7] | |
| Message Broker | [ex.: RabbitMQ] | |
| Container | [ex.: Docker + Compose] | |

---

## Variáveis de Ambiente

| Variável | Obrigatória | Descrição |
|----------|-------------|-----------|
| `DATABASE_URL` | Sim | String de conexão com o banco de dados |
| `PORT` | Não | Porta HTTP (padrão: 3000) |

---

## Restrições & Notas

<!-- Restrições de versão conhecidas, avisos de EOL ou planos de upgrade -->

-
