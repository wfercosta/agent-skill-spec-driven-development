# Spec: [NOME DA FEATURE]

> Documento de Design de Software — fonte única de verdade para esta feature.

**Feature ID**: [FEAT-XXX]
**Status**: Rascunho | Em Revisão | Aprovado
**Autor**: [nome ou agente]
**Criado Em**: [YYYY-MM-DD]
**Última Atualização**: [YYYY-MM-DD]

---

## 1. Visão Geral

### Declaração do Problema

[Qual problema esta feature resolve? Por que ela precisa existir?]

### Resumo da Solução

[Descrição de alto nível do que será construído. Um parágrafo.]

### Escopo

**No escopo:**
- [Item 1]
- [Item 2]

**Fora do escopo:**
- [Item 1]
- [Item 2]

---

## 2. Partes Interessadas

| Papel | Nome / Time | Interesse |
|-------|-------------|-----------|
| [ex.: Product Owner] | [nome] | Aprovação da feature |
| [ex.: Time de Backend] | [nome] | Implementação |
| [ex.: QA] | [nome] | Cobertura de testes |

---

## 3. Requisitos Funcionais

### [FEAT-XXX-REQ-001] [Título do requisito]

**Descrição**: [O que o sistema deve fazer]
**Prioridade**: Must Have | Should Have | Nice to Have
**Justificativa**: [Por que este requisito existe]

---

### [FEAT-XXX-REQ-002] [Título do requisito]

**Descrição**: [O que o sistema deve fazer]
**Prioridade**: Must Have | Should Have | Nice to Have
**Justificativa**: [Por que este requisito existe]

---

## 4. Requisitos Não Funcionais

### Performance

- [ex.: Tempo de resposta P95 deve ser menor que 200ms]
- [ex.: Deve suportar 500 requisições simultâneas]

### Segurança

- [ex.: Todos os endpoints devem exigir autenticação]
- [ex.: PII deve ser criptografado em repouso]

### Disponibilidade & Confiabilidade

- [ex.: SLA: 99,9% de uptime]
- [ex.: Deve se recuperar de falhas transientes em 30 segundos]

### Escalabilidade

- [ex.: Deve escalar horizontalmente sem estado compartilhado]

### Observabilidade

- [ex.: Todas as operações devem emitir logs estruturados]
- [ex.: Métricas principais devem ser exportadas para o Prometheus]

---

## 5. Critérios de Aceitação

| ID | Critério | Método de Verificação |
|----|----------|----------------------|
| AC-001 | [Quando X ocorre, o sistema deve fazer Y] | [ex.: Teste automatizado / QA manual] |
| AC-002 | [Quando A falha, o sistema deve responder com B] | [ex.: Teste de integração] |

---

## 6. Dependências

### Internas

| Dependência | Tipo | Notas |
|-------------|------|-------|
| [ex.: Módulo de usuários] | Dependência forte | Deve estar completo antes da implementação |
| [ex.: Middleware de auth] | Dependência forte | Validação JWT necessária |

### Externas

| Dependência | Tipo | Notas |
|-------------|------|-------|
| [ex.: Stripe API v3] | Serviço externo | Limite de taxa: 100 req/s |

---

## 7. Restrições & Suposições

### Restrições

- [ex.: Deve usar o banco de dados PostgreSQL existente — nenhum BD novo permitido]
- [ex.: Não pode quebrar o contrato de API pública existente]

### Suposições

- [ex.: Os usuários já estão autenticados ao chegar nesta feature]
- [ex.: O catálogo de produtos é somente leitura durante esta operação]

---

## 8. Contexto & Decisões

[Referência a `.specs/features/[feature-name]/context.md` para decisões em área cinza]

---

## 9. Matriz de Rastreabilidade

| Requisito | Critérios de Aceitação | Seção do Design | Tarefa |
|-----------|----------------------|-----------------|--------|
| FEAT-XXX-REQ-001 | AC-001, AC-002 | design.md §3 | T-001 |

---

## 10. Questões em Aberto

| # | Questão | Responsável | Status |
|---|---------|-------------|--------|
| 1 | [Questão] | [nome] | Aberta |

---

## Log de Mudanças

| Data | Autor | Mudança |
|------|-------|---------|
| [YYYY-MM-DD] | [nome/agente] | Rascunho inicial |
