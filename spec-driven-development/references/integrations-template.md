# Integrations: [PROJECT NAME]

> External services, APIs, message brokers, and third-party dependencies.

---

## Integration Map

| Integration | Type | Direction | Criticality |
|-------------|------|-----------|-------------|
| [e.g., Stripe] | Payment gateway | Outbound | Critical |
| [e.g., SendGrid] | Email delivery | Outbound | High |
| [e.g., RabbitMQ] | Message broker | Bidirectional | Critical |
| [e.g., Redis] | Cache / session | Outbound | High |
| [e.g., S3] | File storage | Outbound | Medium |

---

## Integration Details

### [Integration Name]

- **Provider**: [Provider name and version/API]
- **Purpose**: [What this integration does for the application]
- **Authentication**: [e.g., API Key in header, OAuth2, mTLS]
- **Rate Limits**: [e.g., 100 req/s, 10,000 req/day]
- **SLA / Timeout**: [e.g., 5s timeout, 99.9% uptime SLA]
- **Retry Policy**: [e.g., 3 retries with exponential backoff]
- **Circuit Breaker**: [Yes/No — tool used if yes]
- **Docs**: [Link to provider documentation]
- **Environment Variables**:
  ```
  [VAR_NAME]=...
  ```

---

## Adapter Pattern

All integrations must be wrapped in an adapter that implements a domain interface. This ensures the domain layer remains free of external dependencies.

```
Domain Interface (Port)
       ↑
  Adapter (implements Port)
       ↓
  External SDK / HTTP Client
```

```typescript
// Domain port
interface EmailSender {
  send(params: SendEmailParams): Promise<void>;
}

// Infrastructure adapter
class SendGridEmailSender implements EmailSender {
  async send(params: SendEmailParams): Promise<void> {
    // SendGrid SDK calls here
  }
}
```

---

## Local Development

| Integration | Local Substitute | How to Start |
|-------------|-----------------|--------------|
| [e.g., RabbitMQ] | Docker container | `docker compose up rabbitmq` |
| [e.g., Stripe] | Stripe CLI mock | `stripe listen --forward-to localhost:3000/webhooks` |
| [e.g., S3] | LocalStack / MinIO | `docker compose up localstack` |

---

## Failure Handling

| Integration | Failure Impact | Fallback Strategy |
|-------------|---------------|-------------------|
| [e.g., Email] | Non-blocking | Queue for retry |
| [e.g., Payment] | Blocking | Return error to user |
| [e.g., Cache] | Degraded performance | Fall through to database |

---

## Notes

<!-- Known limitations, version-specific behaviors, or pending migrations -->

-
