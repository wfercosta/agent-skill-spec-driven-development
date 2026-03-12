# Convenções: [NOME DO PROJETO]

> Nomenclatura, estilo de código, padrões e acordos de equipe que devem ser aplicados consistentemente.

---

## Convenções de Nomenclatura

### Arquivos & Diretórios

| Artefato | Convenção | Exemplo |
|----------|-----------|---------|
| Arquivos fonte | kebab-case | `user-repository.ts` |
| Arquivos de teste | `[nome].test.ts` | `user-repository.test.ts` |
| Diretórios | kebab-case | `use-cases/` |
| Classes | PascalCase | `UserRepository` |
| Interfaces | PascalCase (sem prefixo `I`) | `UserRepository` |
| Types | PascalCase | `CreateUserInput` |

### Código

| Artefato | Convenção | Exemplo |
|----------|-----------|---------|
| Variáveis & funções | camelCase | `getUserById` |
| Constantes | UPPER_SNAKE_CASE | `MAX_RETRY_ATTEMPTS` |
| Enums | membros PascalCase | `Status.ACTIVE` |
| Membros privados | camelCase (sem underscore) | `this.repository` |
| Variáveis booleanas | prefixo `is/has/can` | `isActive`, `hasPermission` |

---

## Ordem de Imports

```typescript
// 1. Built-ins do Node
import { readFile } from 'node:fs/promises';

// 2. Pacotes externos
import { z } from 'zod';

// 3. Internos — caminhos absolutos (se configurado)
import { UserRepository } from '@/domain/user';

// 4. Internos — caminhos relativos
import { CreateUserInput } from './types';
```

---

## Tratamento de Erros

<!-- Descrever a estratégia: exceções, tipo Result, Either, etc. -->

```typescript
// Exemplo com padrão Result:
type Result<T> = { ok: true; value: T } | { ok: false; error: AppError };
```

---

## Convenção de Commits

Seguindo [Conventional Commits](https://www.conventionalcommits.org/):

```
<tipo>(<escopo>): <resumo curto>

Tipos: feat, fix, docs, style, refactor, test, chore, perf, ci
```

Exemplos:
```
feat(auth): add JWT refresh token support
fix(users): handle duplicate email on registration
test(payments): add integration tests for retry logic
```

---

## Estilo de Código

| Regra | Valor |
|-------|-------|
| Indentação | 2 espaços |
| Aspas | Simples (`'`) |
| Ponto e vírgula | Sim |
| Comprimento máximo de linha | 100 caracteres |
| Vírgulas finais | Todas (ES5+) |

---

## Convenções de Teste

- Arquivos de teste ficam ao lado dos arquivos fonte OU em um diretório paralelo `test/` (escolher um, ser consistente).
- Cada arquivo de teste cobre exatamente um módulo.
- Blocos describe: `describe('[NomeClasse/NomeFunção]', () => {})`.
- Nomes de teste: `it('should [comportamento esperado] when [condição]', ...)`.
- Evitar `test()` — preferir `it()` pela legibilidade.
- Usar `beforeEach` para resetar estado, nunca compartilhar estado mutável entre testes.

---

## Documentação

- Funções e classes públicas devem ter JSDoc.
- Algoritmos complexos devem ter comentários inline explicando o *porquê*, não o *o quê*.
- TODOs devem referenciar um ID de tarefa: `// TODO(T-XXX): ...`.

---

## Padrões a Evitar

<!-- Anti-padrões específicos a este projeto -->

- Não usar `any` em TypeScript — usar `unknown` e narrowing explícito.
- Não engolir erros silenciosamente — sempre registrar ou propagar.
- Não acessar variáveis de ambiente fora do módulo de configuração.
