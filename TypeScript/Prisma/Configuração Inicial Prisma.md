Este guia descreve os passos para configurar o Prisma ORM em um projeto TypeScript, permitindo a conexão com qualquer banco de dados suportado.

## 1. Criação do Projeto

Crie o diretório do projeto e inicialize o TypeScript:

```bash
mkdir meu-projeto && cd meu-projeto
npm init -y
npm install typescript tsx @types/node --save-dev
npx tsc --init
```

## 2. Instalação de Dependências
Instale os pacotes necessários:

```bash
# Dependências de desenvolvimento
npm install prisma @types/node -D

# Dependências de produção
npm install @prisma/client dotenv
```

**O que cada pacote faz:**
- **`prisma`**: CLI para comandos como `init`, `migrate` e `generate`.
- **`@prisma/client`**: Biblioteca para realizar consultas ao banco.
- **`dotenv`**: Carrega variáveis de ambiente do arquivo `.env`.

## 3. Configuração de ESM
Para garantir compatibilidade com módulos ES (import/export):

**`tsconfig.json`**:
```json
{
  "compilerOptions": {
    "module": "ESNext",
    "moduleResolution": "bundler",
    "target": "ES2023",
    "strict": true,
    "esModuleInterop": true
  }
}
```

**`package.json`**:
Adicione `"type": "module"` ao seu arquivo `package.json`.

## 4. Inicialização do Prisma
Inicialize o Prisma no seu projeto:

```bash
npx prisma init
```

Isso criará a pasta `prisma/` com o arquivo `schema.prisma` e um arquivo `.env` na raiz. No `.env`, defina a URL de conexão do seu banco:

```env
DATABASE_URL="postgresql://usuario:senha@localhost:5432/nome_do_banco"
```

## 5. Definição do Modelo de Dados
No arquivo `prisma/schema.prisma`, configure o provedor e seus modelos:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql" // Altere para mysql, sqlite, sqlserver, etc.
  url      = env("DATABASE_URL")
}

model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  author    User    @relation(fields: [authorId], references: [id])
  authorId  Int
}
```

## 6. Migração e Geração do Cliente
Aplique as alterações ao banco de dados e gere o cliente Prisma:

1. **Criar e aplicar migração:**
   ```bash
   npx prisma migrate dev --name init
   ```

2. **Gerar o Prisma Client:**
   ```bash
   npx prisma generate
   ```

---

### Dicas Adicionais:
- Sempre que alterar o `schema.prisma`, execute `npx prisma migrate dev` para sincronizar o banco e `npx prisma generate` para atualizar os tipos do TypeScript.
- Para visualizar seus dados graficamente, utilize o comando `npx prisma studio`.