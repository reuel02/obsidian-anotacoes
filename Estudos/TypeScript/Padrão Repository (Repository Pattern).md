O **Repository Pattern** atua como uma camada de abstração entre a lógica de negócio da sua aplicação e a camada de acesso a dados (banco de dados). Ele cria uma interface que simula uma coleção de objetos em memória, escondendo a complexidade de consultas SQL ou chamadas de ORM (como o Prisma).

## Por que usar?

1. **Desacoplamento:** Sua lógica de negócio não sabe se você está usando Prisma, TypeORM, MongoDB ou um arquivo JSON.
2. **Testabilidade:** Facilita a criação de *Mocks* para testes unitários. Você pode criar um repositório "em memória" para testar sua lógica sem precisar de um banco de dados real.
3. **Centralização:** Toda a lógica de consulta complexa fica em um único lugar, evitando repetição de código em vários *Controllers* ou *Services*.

## Estrutura Típica em TypeScript

O padrão geralmente consiste em duas partes: uma **Interface** (o contrato) e uma **Implementação** (o código que fala com o banco).

### 1. Definindo o Contrato (Interface)
```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>;
  create(data: UserCreateInput): Promise<User>;
}
```

### 2. Implementando com Prisma
```typescript
class PrismaUserRepository implements UserRepository {
  async findById(id: string) {
    return await prisma.user.findUnique({ where: { id } });
  }

  async create(data: UserCreateInput) {
    return await prisma.user.create({ data });
  }
}
```

## Quando utilizar?

- **Em arquiteturas DDD:** É essencial para separar o Domínio da Infraestrutura.
- **Projetos de médio/grande porte:** Onde a manutenção e a testabilidade são prioridades.
- **Quando há necessidade de trocar de banco:** Se você precisar migrar de um banco SQL para um NoSQL, você só precisará criar uma nova implementação da interface, sem alterar a lógica de negócio.

## Diferença entre MVC e DDD com Repository

- **No MVC:** Muitas vezes o *Controller* chama o ORM diretamente. O Repository entra aqui para limpar o *Controller*.
- **No DDD:** O *Service* (ou Caso de Uso) chama o Repository através de uma interface. O *Service* nunca conhece o ORM diretamente.

---

### Exemplo de uso no Service:
```typescript
class CreateUserUseCase {
  constructor(private userRepository: UserRepository) {}

  async execute(data: UserCreateInput) {
    // O caso de uso não sabe que o Prisma existe!
    return await this.userRepository.create(data);
  }
}
```

