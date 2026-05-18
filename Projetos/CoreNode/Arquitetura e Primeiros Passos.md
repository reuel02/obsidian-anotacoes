## Banco de dados

Tabelas

- **TENANTS:** `ID`, `NAME`, `CREATED_AT`, `UPDATED_AT`, `IS_ACTIVE` (booleano para bloquear empresas inadimplentes).
    
- **USERS:** `ID`, `TENANT_ID`, `NAME`, `EMAIL`, `PASSWORD_HASH`, `ROLE` (ex: 'OWNER', 'MEMBER' - para saber quem manda na conta), `RESET_TOKEN`, `RESET_EXPIRES`, `CREATED_AT`, `UPDATED_AT`

Arquitetura de pastas:

```
src/
├── modules/
│   ├── auth/              ✅ (Vamos focar aqui primeiro)
│   ├── tenant/            ✅ (Vamos focar aqui primeiro)
│   ├── rbac/              ⏳ (Futuro: Perfis e Permissões)
│   ├── billing/           ⏳ (Futuro: Integração Stripe)
│   └── notifications/     ⏳ (Futuro: Disparo de Emails)
├── shared/
│   ├── infra/database/    # Conexão com PostgreSQL
│   └── http/              # Servidor Node.js (Express/Fastify)
```

```
erDiagram
    TENANTS {
        uuid id PK
        string name
        boolean is_active
        datetime created_at
        datetime updated_at
    }

    USERS {
        uuid id PK
        uuid tenant_id FK
        string name
        string email
        string password_hash
        string role "Enum: OWNER, MEMBER"
        string reset_token "Nullable"
        datetime reset_expires "Nullable"
        datetime created_at
        datetime updated_at
    }

    TENANTS ||--o{ USERS : "possui"
```