## 1. MVC (Model-View-Controller)

O MVC é um padrão clássico que separa a aplicação em três camadas principais:

- **Model:** Gerencia os dados e a lógica de negócio (ex: entidades Prisma).
- **View:** A interface do usuário (em APIs, geralmente é o JSON retornado).
- **Controller:** Recebe a requisição, chama o Model e decide o que responder.

### Quando usar:
- Projetos pequenos a médios.
- Aplicações CRUD simples.
- Quando a velocidade de entrega é a prioridade.

### Por que usar:
- **Simplicidade:** Baixa curva de aprendizado.
- **Padronização:** A maioria dos frameworks (como NestJS ou Express) facilita essa estrutura.
- **Agilidade:** Menos código "boilerplate" (código repetitivo).

---

## 2. DDD (Domain-Driven Design)

O DDD foca na complexidade do negócio, isolando a lógica central (Domínio) de detalhes técnicos (banco de dados, frameworks, APIs).

- **Domain:** O coração da aplicação (Entidades, Value Objects, Regras de Negócio).
- **Application:** Casos de uso que orquestram o domínio.
- **Infrastructure:** Implementações técnicas (repositórios Prisma, serviços de e-mail, etc).

### Quando usar:
- Projetos de alta complexidade.
- Sistemas onde as regras de negócio mudam frequentemente.
- Equipes grandes onde a separação de responsabilidades é crucial.

### Por que usar:
- **Desacoplamento:** Você pode trocar o banco de dados ou o framework sem tocar na lógica de negócio.
- **Testabilidade:** É muito mais fácil testar o domínio isoladamente.
- **Manutenibilidade:** O código reflete a linguagem e as regras do negócio, não a estrutura do banco de dados.

---

## Comparativo: Qual escolher?

| Característica | MVC | DDD |
| :--- | :--- | :--- |
| **Complexidade** | Baixa | Alta |
| **Foco** | Estrutura de dados | Regras de negócio |
| **Escalabilidade** | Limitada | Alta |
| **Velocidade Inicial** | Rápida | Lenta |

### Resumo para decisão:
- Se você está criando um **MVP ou um sistema simples**, o **MVC** é o caminho mais eficiente.
- Se você está construindo um **sistema robusto, com regras de negócio complexas** que precisam sobreviver a mudanças tecnológicas por anos, o **DDD** é o investimento correto.

---

*Dica: No TypeScript, o uso de **Interfaces** e **Injeção de Dependência** (com bibliotecas como `tsyringe` ou o próprio módulo de DI do NestJS) é o que torna o DDD viável e limpo.*