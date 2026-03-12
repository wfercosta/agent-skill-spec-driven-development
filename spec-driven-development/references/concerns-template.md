# Preocupações: [NOME DO PROJETO]

> Débito técnico, áreas frágeis, riscos conhecidos e itens que requerem atenção.

---

## Níveis de Severidade

| Nível | Significado |
|-------|-------------|
| CRÍTICO | Problema bloqueante — deve ser resolvido antes de novas features |
| ALTO | Risco significativo — agendar para resolução próxima |
| MÉDIO | Débito gerenciável — resolver durante próxima refatoração |
| BAIXO | Problema menor — resolver oportunisticamente |

---

## Preocupações Ativas

### [CONCERN-001] [Título curto]

- **Severidade**: [CRÍTICO / ALTO / MÉDIO / BAIXO]
- **Área**: [ex.: Autenticação, Camada de banco de dados, Pipeline CI]
- **Descrição**: [Qual é o problema e onde ele está]
- **Impacto**: [O que quebra ou degrada se isso for ignorado]
- **Causa Raiz**: [Por que isso existe — pressão de tempo, decisão legada, conhecimento ausente]
- **Remediação**: [Solução proposta ou contorno]
- **Esforço**: [ex.: Pequeno / Médio / Grande]
- **Identificado Em**: [YYYY-MM-DD]

---

## Log de Débito Técnico

| ID | Área | Descrição | Severidade | Identificado | Status |
|----|------|-----------|------------|-------------|--------|
| CONCERN-001 | [área] | [descrição breve] | ALTO | [data] | Aberto |

---

## Áreas Frágeis

<!-- Áreas de código conhecidas por quebrar facilmente ou que requerem cuidado especial ao modificar -->

| Área / Arquivo | Por que É Frágil | Precauções |
|----------------|-----------------|------------|
| [ex.: `src/payments/retry.ts`] | Máquina de estados complexa, sem testes | Adicionar testes antes de modificar |
| [ex.: Migrações de banco de dados] | Passos manuais, sem rollback | Testar em staging primeiro |

---

## Preocupações de Segurança

<!-- Problemas de segurança conhecidos ou áreas que precisam de revisão de segurança -->

| Preocupação | Status | Notas |
|-------------|--------|-------|
| [ex.: Segredos em logs] | Aberto | Verificar todas as chamadas log.info |
| [ex.: SQL injection na busca] | Resolvido | Migrado para Prisma ORM |

---

## Preocupações de Performance

<!-- Gargalos conhecidos ou riscos de escalabilidade -->

| Área | Preocupação | Medido? | Notas |
|------|------------|---------|-------|
| [ex.: Endpoint de listagem de usuários] | Query N+1 | Não | Precisa de eager loading |

---

## Preocupações Resolvidas

<!-- Manter um registro das preocupações endereçadas para referência histórica -->

| ID | Descrição | Resolvido Em | Como |
|----|-----------|-------------|------|
| | | | |

---

## Notas

<!-- Qualquer coisa que não se encaixe nas categorias acima mas mereça atenção -->

-
