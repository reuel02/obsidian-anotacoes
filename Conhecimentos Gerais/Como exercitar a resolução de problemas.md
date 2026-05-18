O bloqueio lógico raramente é um problema de não saber a sintaxe do TypeScript ou do Node.js.

O bloqueio acontece porque o cérebro está tentando resolver a funcionalidade inteira (ex: "criar um sistema de login multi-tenant") de uma só vez. Isso sobrecarrega sua memória de trabalho.

Um estudante de Análise e Desenvolvimento de Sistemas precisa focar nos fundamentos dos algoritmos antes da linguagem. O processo mental de um sênior não é "qual código eu escrevo?", mas sim "quais são as regras e as menores etapas para que isso funcione?".

A regra de ouro a partir de hoje é: **Proibido tocar no teclado (para programar) antes de ter o fluxo escrito em português simples.**

## Prática

Para voltar a treinar o seu cérebro, vamos usar a técnica da **Decomposição Extrema**. Sempre que você travar, feche o VS Code, pegue um bloco de notas (ou papel) e faça o seguinte:

![[Pasted image 20260517170241.png|350]]

**1. Escreva o objetivo final:** "Preciso verificar se uma senha é forte o suficiente no cadastro." **2. Escreva os passos em Português (Pseudocódigo):**

- Receber a senha do usuário.
    
- Verificar se tem pelo menos 8 caracteres. (Se não, retornar erro).
    
- Verificar se tem pelo menos uma letra maiúscula. (Se não, retornar erro).
    
- Verificar se tem pelo menos um número. (Se não, retornar erro).
    
- Se passar por tudo, criptografar a senha e salvar.
    

**3. Traduza linha por linha para código:**
```  
// 1. Receber a senha do usuário
function validatePassword(password: string): boolean {
    
    // 2. Verificar se tem pelo menos 8 caracteres
    if (password.length < 8) {
        throw new Error("A senha deve ter no mínimo 8 caracteres.");
    }

    // 3. Verificar se tem pelo menos uma letra maiúscula
    const hasUpperCase = /[A-Z]/.test(password);
    if (!hasUpperCase) {
        throw new Error("A senha precisa de uma letra maiúscula.");
    }

    // 4. Verificar se tem pelo menos um número
    const hasNumber = /[0-9]/.test(password);
    if (!hasNumber) {
        throw new Error("A senha precisa conter um número.");
    }

    // 5. Se passar por tudo
    return true;
}

```

## Como usar a IA do jeito certo

Você não vai mais pedir "Crie uma função para validar senha forte". Você vai escrever a lógica estruturada por conta própria e, se esquecer como checa uma letra maiúscula no TypeScript, você pergunta à IA **apenas a sintaxe**: _"Como verifico se uma string tem letra maiúscula usando Regex no TypeScript?"_. Isso te mantém no controle da arquitetura.