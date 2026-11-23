# 💻 Comandos Navegação

## 🐚 O que é o Shell do Linux?

O **shell** é um programa que funciona como um interpretador de comandos. Ele permite ao usuário digitar instruções e as traduz para que o sistema operacional realize a tarefa desejada.

> **💡 Nota:** Aplicativos como *Terminal* ou *Console* (comuns nas interfaces gráficas) servem basicamente para abrir e exibir uma sessão do *shell*, criando a ponte visual com o usuário.

---

## 🔀 Variações de Shell

Existem vários tipos de shell com características e atalhos próprios. O **Bash** (Bourne Again Shell) é o mais consolidado e vem como padrão na imensa maioria das distribuições Linux. Outras opções bastante conhecidas incluem:
- `ksh`
- `zsh` (Muito usado em macOS e focado em produtividade)
- `tcsh`

---

## 🛠️ Comandos Básicos (Navegação)

Abaixo estão as ferramentas indispensáveis para navegar pelo terminal no dia a dia:

### `echo`
Exibe (ou "ecoa") uma string de texto de volta no terminal.
```bash
echo Hello World
```

### `pwd` *(Print Working Directory)*
Imprime na tela o caminho completo absoluto do diretório onde você está no momento.
```bash
pwd
```

### `cd` *(Change Directory)*
Navega entre os variados diretórios e pastas do sistema.

**Exemplos de uso (Absoluto e Relativo):**
```bash
# Navega para "Pictures" passando o caminho absoluto (partindo da raiz /)
cd /home/pete/Pictures

# Navega para a pasta "Pictures" que está dentro do diretório atual
cd Pictures
```

**Atalhos práticos do comando:**
| Atalho | O que faz |
| :---: | :--- |
| `cd .` | Representa o diretório atual |
| `cd ..` | Volta um nível acima (vai para a pasta "pai" da atual) |
| `cd ~` | Vai direto para a pasta *home* (diretório pessoal do seu usuário) |
| `cd -` | Alterna de volta para a última pasta em que você esteve |

---

### `ls` *(List)*
Lista o conteúdo dentro do diretório atual, ou dentro do diretório que for informado por você.

**Exemplos básicos:**
```bash
# Lista os arquivos do seu diretório atual
ls

# Lista os arquivos dentro de um caminho específico
ls /home/pete
```

**Principais Flags (Parâmetros) do `ls`:**
| Parâmetro | Descrição |
| :---: | :--- |
| `-a` *(all)* | Lista **tudo**, incluindo arquivos e pastas ocultos (quaisquer nomes que comecem com ponto `.`) |
| `-l` *(long)* | Formato de lista longa. Mostra detalhes extras como: permissões, dono, peso do arquivo e data. |
| `-r` *(reverse)* | Inverte a ordem padrão. Lista na ordem alfabética reversa. |

> **🔥 Dica Pro:** Você pode agrupar várias flags juntas para otimizar seus comandos!  
> **Exemplo:** `ls -al` *(Lista absolutamente todos os arquivos e ocultos, trazendo as informações detalhadas em formato de lista).*
