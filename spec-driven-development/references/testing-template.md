# Testes: [NOME DO PROJETO]

> Estratégia de testes, frameworks, padrões e expectativas de cobertura.

---

## Visão Geral da Estratégia

| Nível | Escopo | Ferramentas | Objetivo |
|-------|--------|-------------|----------|
| Unitário | Função/classe única, sem I/O | [ex.: Vitest] | Correção da lógica de negócio |
| Integração | Módulo + dependências reais | [ex.: Vitest + Testcontainers] | Correção de adapters e DB |
| E2E | Sistema completo via interface pública | [ex.: Supertest / Playwright] | Comportamento do ponto de vista do usuário |

---

## Frameworks & Bibliotecas de Teste

| Biblioteca | Versão | Propósito |
|------------|--------|-----------|
| [ex.: Vitest] | [v] | Runner de testes e asserções |
| [ex.: @vitest/coverage-v8] | [v] | Relatório de cobertura |
| [ex.: Supertest] | [v] | Testes de integração HTTP |
| [ex.: testcontainers] | [v] | DB/serviços reais nos testes |
| [ex.: @faker-js/faker] | [v] | Geração de dados de teste |

---

## Executando Testes

```bash
# Todos os testes
[comando de teste]

# Modo watch
[comando de watch]

# Cobertura
[comando de cobertura]

# Arquivo específico
[runner] [caminho/para/arquivo.test.ts]

# Padrão específico
[runner] --grep "UserService"
```

---

## Organização de Arquivos

```
# Opção A: Co-located (ao lado do fonte)
src/
└── users/
    ├── user.service.ts
    └── user.service.test.ts

# Opção B: Diretório paralelo
test/
└── unit/
    └── users/
        └── user.service.test.ts
```

> Este projeto usa: **[Opção A / Opção B]**

---

## Estrutura dos Testes

```typescript
describe('[NomeClasse ou nome da função]', () => {
  // Setup compartilhado
  let sut: UserService;
  let mockRepository: MockUserRepository;

  beforeEach(() => {
    mockRepository = new MockUserRepository();
    sut = new UserService(mockRepository);
  });

  describe('[nome do método]', () => {
    it('should [comportamento esperado] when [condição]', async () => {
      // Arrange
      const input = { ... };

      // Act
      const result = await sut.method(input);

      // Assert
      expect(result).toEqual({ ... });
    });

    it('should throw [TipoDeErro] when [condição inválida]', async () => {
      // Arrange
      const invalid = { ... };

      // Act & Assert
      await expect(sut.method(invalid)).rejects.toThrow(SomeError);
    });
  });
});
```

---

## Mocking

- Preferir mocks manuais ao invés de auto-mocking pela clareza.
- Mockar apenas nas fronteiras do sistema (repositórios, APIs externas, filas).
- Não mockar lógica de domínio — testá-la diretamente.
- Resetar todos os mocks em `beforeEach` ou `afterEach`.

```typescript
// Exemplo de mock manual
class MockUserRepository implements UserRepository {
  users: User[] = [];

  async findById(id: string): Promise<User | null> {
    return this.users.find(u => u.id === id) ?? null;
  }
}
```

---

## Requisitos de Cobertura

| Métrica | Mínimo |
|---------|--------|
| Statements | [ex.: 80%] |
| Branches | [ex.: 75%] |
| Functions | [ex.: 80%] |
| Lines | [ex.: 80%] |

---

## Fluxo de TDD (Obrigatório para tarefas de codificação)

1. Escrever teste(s) falhando que descrevam o comportamento esperado.
2. Executar testes — confirmar que falham pelo motivo correto.
3. Escrever o código mínimo para fazer os testes passarem.
4. Executar testes — confirmar que passam.
5. Refatorar se necessário — os testes devem continuar passando.
6. Executar a suite completa de testes antes de fazer commit.

---

## Dados de Teste

- Usar funções factory ou builders para criar dados de teste.
- Nunca usar IDs hardcoded que possam conflitar entre testes.
- Para testes de integração, usar schemas de banco de dados isolados ou transações que fazem rollback.

---

## Notas

<!-- Decisões de teste específicas do projeto, testes instáveis conhecidos ou áreas com baixa cobertura -->

-
