# Estrutura: [NOME DO PROJETO]

> Layout de diretórios, organização de módulos e localização dos arquivos principais.

---

## Layout Raiz

```
[project-root]/
├── src/                    # Código-fonte da aplicação
├── test/                   # Arquivos de teste (se separados de src)
├── .specs/                 # Artefatos de desenvolvimento orientado a especificações
├── docs/                   # Documentação adicional (opcional)
├── scripts/                # Scripts utilitários (build, seed, etc.)
├── docker/                 # Arquivos de configuração Docker
├── [arquivos de config]    # tsconfig.json, package.json, .eslintrc, etc.
└── README.md
```

---

## Diretório Fonte (`src/`)

```
src/
├── [modulo-1]/             # [Descrever propósito, ex.: entidades de domínio]
│   ├── [entidade].ts
│   └── index.ts            # API pública do módulo
│
├── [modulo-2]/             # [Descrever propósito, ex.: casos de uso]
│   ├── [caso-de-uso].ts
│   └── index.ts
│
├── [modulo-3]/             # [Descrever propósito, ex.: adapters de infraestrutura]
│   ├── [adapter].ts
│   └── index.ts
│
├── [modulo-4]/             # [Descrever propósito, ex.: controllers HTTP]
│   ├── [controller].ts
│   └── index.ts
│
├── shared/                 # Utilitários compartilhados, tipos, erros
│   ├── errors.ts
│   ├── types.ts
│   └── utils.ts
│
└── main.ts                 # Ponto de entrada da aplicação
```

---

## Diretório de Testes (`test/`)

```
test/
├── unit/                   # Testes puramente unitários (sem I/O)
│   └── [espelha estrutura src/]
│
├── integration/            # Testes com dependências reais (DB, filas)
│   └── [feature ou módulo]
│
└── e2e/                    # Testes end-to-end completos
    └── [cenário]
```

---

## Arquivos Principais

| Arquivo | Propósito |
|---------|-----------|
| `src/main.ts` | Bootstrap da aplicação |
| `src/shared/errors.ts` | Definições de erros de domínio |
| `src/shared/types.ts` | Definições de tipos compartilhados |
| `tsconfig.json` | Configuração TypeScript |
| `package.json` | Dependências e scripts |
| `.env.example` | Documentação de variáveis de ambiente |
| `docker-compose.yml` | Serviços para desenvolvimento local |

---

## Fronteiras de Módulos

Cada módulo expõe uma API pública através de seu `index.ts`. Imports externos a um módulo devem passar por `index.ts` — nunca importar arquivos internos diretamente.

```typescript
// Correto
import { User } from '@/domain';

// Errado
import { User } from '@/domain/entities/user.entity';
```

---

## Notas

<!-- Decisões estruturais específicas do projeto ou desvios do template -->

-
