# Integrações: [NOME DO PROJETO]

> Serviços externos, APIs, message brokers e dependências de terceiros.

---

## Mapa de Integrações

| Integração | Tipo | Direção | Criticidade |
|------------|------|---------|-------------|
| [ex.: Stripe] | Gateway de pagamento | Saída | Crítica |
| [ex.: SendGrid] | Entrega de email | Saída | Alta |
| [ex.: RabbitMQ] | Message broker | Bidirecional | Crítica |
| [ex.: Redis] | Cache / sessão | Saída | Alta |
| [ex.: S3] | Armazenamento de arquivos | Saída | Média |

---

## Detalhes das Integrações

### [Nome da Integração]

- **Provedor**: [Nome do provedor e versão/API]
- **Propósito**: [O que esta integração faz para a aplicação]
- **Autenticação**: [ex.: API Key no header, OAuth2, mTLS]
- **Limites de Taxa**: [ex.: 100 req/s, 10.000 req/dia]
- **SLA / Timeout**: [ex.: timeout de 5s, SLA de 99,9% de uptime]
- **Política de Retentativa**: [ex.: 3 tentativas com backoff exponencial]
- **Circuit Breaker**: [Sim/Não — ferramenta usada se sim]
- **Docs**: [Link para documentação do provedor]
- **Variáveis de Ambiente**:
  ```
  [NOME_VAR]=...
  ```

---

## Padrão de Adapter

Todas as integrações devem ser encapsuladas em um adapter que implementa uma interface de domínio. Isso garante que a camada de domínio permaneça livre de dependências externas.

```
Interface de Domínio (Port)
         ↑
  Adapter (implementa Port)
         ↓
  SDK Externo / Cliente HTTP
```

```typescript
// Port de domínio
interface EmailSender {
  send(params: SendEmailParams): Promise<void>;
}

// Adapter de infraestrutura
class SendGridEmailSender implements EmailSender {
  async send(params: SendEmailParams): Promise<void> {
    // Chamadas ao SDK do SendGrid aqui
  }
}
```

---

## Desenvolvimento Local

| Integração | Substituto Local | Como Iniciar |
|------------|-----------------|--------------|
| [ex.: RabbitMQ] | Container Docker | `docker compose up rabbitmq` |
| [ex.: Stripe] | Mock Stripe CLI | `stripe listen --forward-to localhost:3000/webhooks` |
| [ex.: S3] | LocalStack / MinIO | `docker compose up localstack` |

---

## Tratamento de Falhas

| Integração | Impacto da Falha | Estratégia de Fallback |
|------------|-----------------|------------------------|
| [ex.: Email] | Não bloqueante | Enfileirar para retentativa |
| [ex.: Pagamento] | Bloqueante | Retornar erro ao usuário |
| [ex.: Cache] | Performance degradada | Passar direto para o banco de dados |

---

## Notas

<!-- Limitações conhecidas, comportamentos específicos de versão ou migrações pendentes -->

-
