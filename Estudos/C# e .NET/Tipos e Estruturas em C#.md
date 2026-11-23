
Este documento resume as principais diferenças, comportamentos na memória e casos de uso das estruturas de dados e tipagem no C#.

---

## 1. Tuplas (Tuples)

**O que é:** Um agrupamento leve e temporário de múltiplos valores sem a necessidade de criar um tipo formal com nome.

*   **Comportamento:** Criadas em tempo de execução, ótimas para escopos locais.

*   **Caso de Uso Ideal:** Quando você precisa retornar mais de um valor de um método e não quer criar uma classe/record só para isso.

*   **Exemplo:**

    ```csharp
    (bool Sucesso, string Mensagem) Processar() => (true, "Feito!");
    var resultado = Processar();
    Console.WriteLine(resultado.Mensagem);
    ```

## 2. Interfaces

**O que é:** Um contrato. Ela não armazena dados nem estado, apenas define **o que** uma classe ou struct deve fazer (os métodos e propriedades que deve implementar).

*   **Comportamento:** Tipos diferentes podem assinar o mesmo contrato, permitindo que o sistema interaja com eles de forma padronizada (Polimorfismo).

*   **Caso de Uso Ideal:** Desacoplamento de sistemas, injeção de dependência e garantia de que diferentes peças do código falem a mesma língua.

*   **Exemplo:**

    ```csharp
    interface IVeiculo { void Ligar(); }
    ```

## 3. Classes (Classes)

**O que é:** O bloco de construção fundamental da Orientação a Objetos. Modela entidades complexas.

*   **Memória:** Tipo de Referência (*Reference Type*). Fica armazenada no *Heap*.

*   **Cópia:** Copiar uma classe apenas copia a "referência" (o controle remoto). Alterar a cópia altera o objeto original.

*   **Igualdade:** Igualdade de Referência. Dois objetos com os mesmos dados são considerados diferentes, a menos que apontem para o mesmo endereço de memória.

*   **Caso de Uso Ideal:** Regras de negócio complexas, serviços, entidades de banco de dados, objetos que mudam de estado com o tempo e que usam herança.

*   **Exemplo:**

    ```csharp
    class Usuario { public string Nome { get; set; } }
    ```

## 4. Records (Registros)

**O que é:** Uma estrutura (geralmente tipo de referência) otimizada para armazenar dados estáticos.

*   **Memória:** Tipo de Referência (por padrão, mas existe `record struct`).

*   **Cópia/Mutação:** Otimizado para imutabilidade. Usa-se a palavra `with` para criar uma cópia com dados alterados, sem tocar no original (Mutação não destrutiva).

*   **Igualdade:** Igualdade de Valor. Se os dados de dois records forem exatamente iguais, o C# os considera o mesmo objeto.

*   **Caso de Uso Ideal:** DTOs (Data Transfer Objects), APIs, respostas JSON, recibos, eventos. Qualquer cenário onde a informação transita e não deve sofrer mutação.

*   **Exemplo:**

    ```csharp
    public record Ticket(string Placa, decimal Valor);
    var t1 = new Ticket("ABC", 10m);
    var t2 = t1 with { Valor = 20m }; // t1 continua intacto
    ```

## 5. Structs (Estruturas)

**O que é:** Uma estrutura de dados focada em ser leve e rápida para a memória.

*   **Memória:** Tipo de Valor (*Value Type*). Fica armazenada diretamente na variável (geralmente na *Stack*).

*   **Cópia:** Cópia Física. Ao atribuir a outra variável, todos os dados são duplicados. Alterar a cópia **não** altera o original.

*   **Igualdade:** Igualdade de Valor (compara os campos internos).

*   **Caso de Uso Ideal:** Tipos muito pequenos (menos de 16 bytes) que representam um valor único composto, como Coordenadas GPS (Latitude/Longitude), Cores (RGB) ou Medidas.

*   **Exemplo:**

    ```csharp
    struct Ponto { public double X { get; set; } public double Y { get; set; } }
    ```

## 6. Lists (Listas)


---

### Resumo Rápido de Decisão:

*   Devolver 2 valores de um método? **Tupla**
*   Definir um contrato a ser seguido? **Interface**
*   Entidade com estado mutável e lógica? **Class**
*   Pacote de dados imutável da API (JSON)? **Record**
*   Coordenada matemática leve? **Struct**