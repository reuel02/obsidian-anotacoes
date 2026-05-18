
Este arquivo define a configuração para o serviço de banco de dados PostgreSQL.

## Configuração do Serviço

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    container_name: corenode_db
    restart: always
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: adminpassword
      POSTGRES_DB: corenode
    ports:
      - "5432:5432"
    volumes:
      - corenode_pgdata:/var/lib/postgresql/data

volumes:
  corenode_pgdata:
```

## Explicação dos Parâmetros

| Parâmetro | Descrição |
| :--- | :--- |
| `version` | Define a versão da linguagem do Docker Compose (3.8 é o padrão de estabilidade). |
| `services` | Lista os contêineres que serão executados. |
| `image` | Define a imagem oficial a ser baixada (ex: `postgres:15`). |
| `container_name` | Define o nome amigável para exibição no Docker Desktop. |
| `restart` | Define a política de reinicialização (ex: `always` para resiliência). |
| `environment` | Variáveis de ambiente injetadas no contêiner (credenciais). |
| `ports` | Mapeamento de portas entre o host e o contêiner (`host:container`). |
| `volumes` | Persistência de dados, mapeando pastas do contêiner para o disco rígido. |
