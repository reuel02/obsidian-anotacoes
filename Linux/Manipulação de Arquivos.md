# 📂 Comandos de Manipulação de Arquivos

## 📋 Comando `cp` *(copy)*

O `cp` é o comando padrão definitivo para **copiar** arquivos e diretórios no ecossistema Linux.

### 📄 1. Cópia Básica
Para fazer uma cópia tradicional simples, basta passar primeiro o arquivo de origem (*source*) e, em seguida, o seu caminho de destino (*destination*).

```bash
cp arquivo.txt /caminho/do/destino/
```

### 🧩 2. Usando Curingas (Wildcards) para Cópias em Massa
*Curingas (wildcards)* são caracteres especiais que funcionam de fato como curingas. Eles permitem agrupar e selecionar inúmeros arquivos simultaneamente focados apenas no padrão do nome deles, em vez de exigir que você escreva o nome de cada um de forma manual.

| Símbolo Curinga | O que ele capta |
| :---: | :--- |
| `*` | Corresponde a **qualquer** sequência de caracteres, literalemtne tudo. |
| `?` | Corresponde a um **único** caractere em específico (qualquer um). |
| `[ ]` | Corresponde a **qualquer um** dos caracteres que estiverem agrupados ali dentro dos colchetes. |

**Exemplo Prático:** Copiando apenas os arquivos de imagem que terminam num formato específico (`.jpg`) para a pasta imagens:
```bash
cp *.jpg /home/reuel/imagens/
```

### 📁 3. Copiando Diretórios Inteiros (Recursivo)
Se você tentar copiar um diretório (pasta) usando o `cp` de forma limpa sem parâmetros, você receberá imediatamente um erro no terminal. Para copiar a pasta e *todo* o conteúdo interior dela (junto dos seus subdiretórios e arquivos internos), é obrigatório utilizar a flag `-r` *(recursive)*.

```bash
cp -r teste/ /home/reuel/
```

---

### ⚙️ Lidando com Sobrescrita, Força e Preservação

Por padrão, se você copiar um item para um lugar onde já exista outro arquivo com exatamente o mesmo nome, o comando `cp` vai simplesmente **sobrescrevê-lo (deletar o antigo)** sem te avisar. 

Para manusear os dados e evitar - ou abraçar - esse comportamento de gravação de forma saudável num uso diário, veja o compilado das `flags` essenciais do `cp`:

| Parâmetro (Flag) | Ação de modificador | Código de Exemplo |
| :---: | :--- | :--- |
| `-i` *(interactive)* | **Evita Perdas:** Modo interativo. Solicitará que você confirme a ação no prompt `(y/n)` antes de sobrescrever uma mídia que já existe. | `cp -i arquivo.txt /home/pete/Pictures/` |
| `-f` *(force)* | **Uso Forçado:** Força a sobrescrita imediata sem pedir qualquer confirmação na sua tela inteira. *(Bastante útil na criação de scripts automáticos)*. | `cp -f arquivo.txt /home/pete/Pictures/` |
| `-p` *(preserve)* | **O Clone Perfeito:** Copia o arquivo em si como também os seus **metadados raízes** (como modo de proteção, dono e carimbos de data/hora originais). Fica idêntico ao genuíno em todos os aspectos! | `cp -p arquivo.txt /home/pete/backups/` |
---

## 🚚 Comando `mv` *(move)*

O `mv` é o comando utilizado tanto para **mover** arquivos/diretórios de um local para outro, quanto para **renomeá-los**.

### 🏷️ 1. Renomeando Arquivos e Diretórios
Para renomear, você finge "mover" o arquivo para o mesmo diretório mas com um nome diferente.
```bash
# Renomeando um arquivo isolado:
mv nome_antigo.txt nome_novo.txt

# Renomeando um diretório:
mv pasta_antiga/ pasta_nova/
```

### 📦 2. Movendo Arquivos e Diretórios
Da mesma forma, você pode enviá-los e movê-los para caminhos diferentes do qual você está atual.
```bash
# Movendo um único arquivo:
mv arquivo.txt /home/reuel/Documentos/

# Movendo múltiplos arquivos de uma vez só:
mv arquivo1.txt arquivo2.txt /home/reuel/Documentos/
```

### ⚙️ Flags e Opções Avançadas do `mv`
| Parâmetro (Flag) | Ação da Flag | Código de Exemplo |
| :---: | :--- | :--- |
| `-t` *(target)* | **Inverte a Ordem:** Permite passar a pasta de "destino" em primeiríssimo lugar, deixando mais limpo quando se passa dezenas de arquivos depois. | `mv -t /home/reuel/Documentos/ arq1 arq2` |
| `-i` *(interactive)* | **Segurança:** Solicita obrigatóriamente a sua confirmação (`y/n`) sempre que tentar mover/renomear algo que vá sobrescrever um arquivo existente de mesmo nome. | `mv -i arq_origem.txt pasta_destino/` |
| `-b` *(backup)* | **Versão Backup:** Se ele enxergar e precisar sobrescrever um arquivo ao se mover, ele primeiro deixará um backup do existente (apenas adicionando um sufixo `~` no final dele). | `mv -b arq1.txt pasta_destino/` |
| `-v` *(verbose)* | **Modo Detalhado:** Faz o bash imprimir na tela passo-a-passo como se fosse um report mostrando o que está se movendo no background. | `mv -v arq1 arq2 /pasta_destino/` |

---

## 📁 Comando `mkdir` *(make directory)*

Como o próprio formato indica, atua puramente para criar diretórios (pastas exclusivas).

**Criando uma ou várias pastas de uma vez:**
```bash
# Única pasta formatada:
mkdir documentos

# Várias pastas na mesma linha sem dor de cabeça:
mkdir documentos fotos livros
```

### 🌳 Criando Estruturas Aninhadas (Flag `-p`)
Caso você precise criar uma árvore super complexa de subpastas (exemplo: `A/B/C`), você sofria um erro no linux se a pasta "pai" ainda não existisse. A flag `-p` *(parent)* resolve isso de primeira, criando todo o caminho pai que faz falta de forma silenciosa e efetiva!
```bash
mkdir -p documentos/pessoais/antigos
```

---

## 🗑️ Comando `rm` *(remove)*

Este é o comando para **excluir e deletar definitivamente** os maravilhosos arquivos soltos e seus diretórios.

**Exclusão Básica de Arquivo:**
```bash
rm arquivo1.txt
```
> **⚠️ CUIDADO REDOBRADO:** Diferente do Windows, o Linux pelo terminal CLI **NÃO TEM UMA LIXEIRA MÁGICA**. Apagou algum recurso com o `rm`, o arquivo não tem segunda chance e evaporeceu do mapa! 

### ⚙️ Flags Cruciais do `rm`
Como é um comando destrutivo, os manipuladores são seu maior amigo:

| Flag | Ação e Significado |
| :---: | :--- |
| `-f` *(force)* | Exclui arquivos e diretórios *à força bruta*, ignorando as proteções de leitura/escrita silenciosamente e sem fazer uma única pergunta na sua tela. Cuidado extremo! |
| `-i` *(interactive)* | É a pura essência de uso seguro da máquina. Imita um processo pedindo permissão de usuário `(y/n)` antes de varrer cada documentário selecionado. |
| `-r` *(recursive)* | A forma perfeita para **apagar diretórios lotados e não brancos**. Ele destrói a raíz da pasta e limpa sem dó todos os inúmeros subdiretórios alojados lá dentro dela. |

### 🧹 Limpeza Segura usando apenas `rmdir` *(remove directory)*
Para as vezes evitar de passar a fatídica e cega flag `rm -r` que tem chance catastrófica de deletar a pasta incorreta por acidente e ferrar o sistema, introduzimos o comando secundário de purificação `rmdir`.
```bash
rmdir pasta1
```
> **💡 Detalhe Importante:** Diferente da versão recursiva assassina do anterior, o `rmdir` é um comando 100% de uso pacífico e seguro! O motivo? É claro, pois o comando dele **só se tornará de fato ativado se e exclusivamente se o diretório estiver TOTALMENTE vazio sem sequer um byte sobrando ali dentro**. Para limpezas organizacionais essa é a arma adequada.

## 🔍 Comando `find`

Esse comando é utilizado para pesquisar arquivos e diretórios dentro de um determinado diretório.

**Exemplo Básico:**
```bash
find /home -name puppies.jpg
```

### 🗂️ Pesquisando por Nome e Tipo
```bash
find /home -type d -name MyFolder
```

---

## ❓ Comando `help` e flag `--help`

Para comandos *built-ins* do bash podemos usar o comando `help` para fornecer informações de uso do comando.

```bash
help echo
```

Para comandos que não são *built-ins* usamos a flag `--help` que fornece informações de uso de qualquer comando.

**Exemplo:**
```bash
ls --help
```

---

## 📖 Comando `man` *(manual)*

Esse comando exibe o manual de cada comando Linux, podemos usar para obter todas as informações disponíveis de qualquer comando.

**Exemplo:**
```bash
man ls
```

---

## ℹ️ Comando `whatis`

Esse comando exibe uma descrição curta e objetiva de um comando Linux, podemos usar para saber o que um comando específico faz de forma objetiva.

**Exemplo:**
```bash
whatis cat
```
> **Output:** `cat (1)              - concatenate files and print on the standard output`

---

## 🔗 Comando `alias`

Esse comando permite criar atalhos para comandos do Linux, podendo ser usado de forma temporária ou permanente. Um alias temporário só pode ser usado durante a sessão atual do shell, assim que você fechar o terminal o alias é excluído.

### ⏱️ Criando um Alias Temporário
```bash
alias ll='ls -la'
```

### ♾️ Criando um Alias Permanente

1. Abra o arquivo em um editor de texto:
```bash
nano ~/.bashrc
```

2. Adicione sua definição de alias ao arquivo, exatamente como você a digitou na linha de comando:
```bash
alias ll='ls -la'
alias update='sudo apt update && sudo apt upgrade'
```

3. Salve o arquivo e saia do editor.

4. Para que as alterações entrem em vigor, você deve fechar e reabrir seu terminal ou instruir o shell a recarregar o arquivo de configuração usando o comando `source`:
```bash
source ~/.bashrc
```

### ❌ Removendo um Alias
```bash
unalias ll
```

---

## 🚪 Comando `exit`

Esse comando é usado para terminar uma sessão do shell.

---

## 🛑 Comando `logout`

Parecido com o `exit` na maioria dos sistemas modernos, ele encerra uma sessão em um shell de login.