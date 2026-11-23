# 📂 Comandos de Arquivos

## 🛠️ Manipulação com `touch`

O comando `touch` possui duas funções principais no ecossistema Linux: **criar arquivos vazios** e **atualizar os carimbos de data/hora** (timestamps) de arquivos que já existem.

---

### 🟢 1. Criando Arquivos Vazios
Sendo muito utilizado no dia a dia, você pode usar o comando simplesmente para criar novos arquivos do zero.

**Criar um único arquivo:**
```bash
touch arquivo1.txt
```

**Criar vários arquivos ao mesmo tempo:**
Basta separá-los por espaço.
```bash
touch arquivo1.txt arquivo2.log
```

---

### ⏱️ 2. Atualizando Carimbos de Data/Hora (Timestamps)
A função *original* do comando `touch` é manipular os horários de acesso e modificação dos arquivos/diretórios. 

Se você usar o `touch` apontando para um arquivo **já existente**, ele atualizará a data e hora do mesmo para o instante exato em que você executou o comando.

```bash
touch arquivo_ja_existente.txt
```

#### Flags Avançadas de Data e Hora

Para um controle mais preciso em atualizações de estado ou sincronização, o `touch` traz essas opções muito úteis:

| Parâmetro (Flag) | O que faz | Exemplo de Código |
| :---: | :--- | :--- |
| `-r` *(reference)* | Define os horários de um arquivo para corresponderem exatamente aos de outro arquivo (um arquivo de **referência**). Útil para sincronizar arquivos relacionados. | `touch -r arquivo1.txt arquivo2.txt` |
| `-d` *(date)* | Permite que você defina um carimbo para uma **data e hora específicas** de forma manual. | `touch -d "2023-01-01 12:30:00" arquivo1.txt` |

> **💡 Nota:** Lidar com os horários dos arquivos é indispensável quando se automatizam backups e ao trabalhar com ferramentas que rastreiam as últimas modificações dos sistemas (como sistemas CI/CD ou Makefiles).

---

## 📄 Comando `file`

O comando `file` serve para inspecionar e mostrar uma descrição detalhada sobre qual é o tipo e formato do conteúdo de um arquivo (sem que você precise abri-lo).

**Exemplo básico:**
```bash
file arquivo1.txt
```

---

## 🐈 Comando `cat` *(concatenate)*

O `cat` é uma das ferramentas mais clássicas do ecossistema Linux. A maioria das pessoas o utiliza simplesmente para ler textos no terminal.

### 📖 1. Exibir o Conteúdo
Ele lê o arquivo e "despeja" todo o seu conteúdo bruto rapidamente na tela de uma só vez.
```bash
cat arquivo.txt
```

### 🔗 2. Concatenar (Juntar) Múltiplos Arquivos
A principal função do comando (a qual dá origem ao nome dele) é a de concatenar. Executando o comando dessa forma, ele exibirá o conteúdo somado dos dois - ou mais - arquivos juntos:
```bash
cat arquivo1.txt arquivo2.txt
```

### ✍️ 3. Criar Arquivos e Gravar Texto
Quando usado junto com o operador de redirecionamento de saída (`>`), o `cat` funciona como um bloco de notas de terminal ultrarrápido para criar arquivos novos!

```bash
cat > arquivo_novo.txt
```
> **⚠️ Cuidado!** Após jogar esse comando, a tela ficará esperando você digitar seu texto. Quando terminar sua nota, dê `Enter` para pular para uma linha nova e pressione as teclas `Ctrl+D` juntas para salvar e sair.
> 
> **Mas tenha muita atenção**: Utilizar o operador `>` apontando para um arquivo existente vai **sobrescrevê-lo na mesma hora**, apagando todo seu conteúdo antigo.

#### Flags Comuns do `cat`

| Parâmetro (Flag) | O que faz |
| :---: | :--- |
| `-n` *(number)* | Vai mostrar a saída numerando lado a lado todas as linhas (começando na linha 1). |
| `-b` *(blank)* | Quase igual à anterior, mas a numeração pula (ignora) linhas que estiverem vazias/em branco. |

---

## 📖 Comando `less`

O comando `less` permite abrir e visualizar o conteúdo de um arquivo de texto de forma **paginada** (uma tela por vez). Ele é muito mais eficiente que o `cat` para arquivos grandes, pois não "cospe" e carrega o arquivo inteiro de uma só vez no seu terminal.

**Exemplo de uso:**
```bash
less arquivo.txt
```

### 🧭 Navegação e Controles Básicos
Dentro do ambiente de leitura do `less`, o mouse não funciona como nos blocos de notas tradicionais. Você usa atalhos de teclado para se mover:

| Teclas / Atalhos | Ação |
| :---: | :--- |
| `↑` `↓` / `PageUp` `PageDown` | Navega pelo texto livremente (linha a linha ou página a página). |
| <kbd>g</kbd> *(minúsculo)* | Salta para a **primeira linha** (início do arquivo). |
| <kbd>G</kbd> *(Shift + g)* | Pula direto para a **última linha** (final do arquivo). |
| <kbd>h</kbd> *(help)* | Exibe um resumo útil de axílio com todos os comandos de navegação. |
| <kbd>q</kbd> *(quit)* | **Encerra** a visualização e sai de volta para o terminal. |

### 🔍 Buscando Texto
Um dos maiores super-poderes do `less` é pesquisar textos. Para fazer uma busca, digite `/` (barra) seguido do texto que deseja encontrar e aperte `Enter`. O terminal irá destacar todas as ocorrências na tela!

**Recursos de Busca Avançada:**
| Comando | O que faz |
| :---: | :--- |
| `/palavra` | Pesquisa pela palavra do seu ponto **para a frente** (para baixo). |
| `?palavra` | Pesquisa pela palavra do seu ponto **para trás** (para cima). |
| <kbd>n</kbd> *(next)* | Salta automaticamente para o **próximo** resultado da pesquisa. |
| <kbd>N</kbd> *(prev)* | Retorna e salta para o resultado **anterior** da pesquisa. |

---

## 🕒 Comando `history`

Esse comando exibe o registro histórico completo de quais comandos recentes você executou no seu shell.

```bash
history
```

> **🔥 Super Dica:** Pressionando as teclas <kbd>Ctrl</kbd> + <kbd>R</kbd> simultaneamente em seu terminal, é possível ativar uma **pesquisa reversa**. Você começa a digitar algumas letras e ele busca automaticamente qual foi a última vez que um comando do histórico possuiu aquela palavra!

### ⚙️ Gerenciando a Lista de Histórico
Além de poder apenas olhar o que você fez, você pode fazer "manutenções" na sua lista usando algumas flags específicas:

| Parâmetro | Ação / O que faz |
| :---: | :--- |
| `history -c` | **Limpar Histórico:** Remove todos os rastreios em memória e exibe uma listagem em branco para a sessão atual. *(c = clear)* |
| `history -w` | **Salvar na Mão:** Salva à força o histórico dessa sessão atual do terminal direto no banco de dados raiz dele (arquivo `~/.bash_history`). Muito útil para não perder seus comandos antes de forçar o fechamento de um programa. |
| `history -d <N>` | **Excluir Linha:** Apaga somente uma linha específica baseada no número. Exemplo: `history -d 101` deleta o seu 101º comando da memória. |

---

## 🧹 Comando `clear`

Dentre outras ferramentas essenciais de dia a dia no terminal, a mais famosa para limpeza da visão e foco. 

À medida que a sua janela do terminal for ficando poluída e repleta de textos subindo, chame o comando para rolar a tela, apagando a visibilidade e deixando ela como nova!
```bash
clear
```
> **💡 Dica Bônus:** Na esmagadora maioria dos terminais Linux, apertar <kbd>Ctrl</kbd> + <kbd>L</kbd> faz exatamente a mesma ação de disparar um `clear` de forma instânea!