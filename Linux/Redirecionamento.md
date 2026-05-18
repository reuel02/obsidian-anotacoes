# 🔀 Redirecionamento de Fluxos (I/O) e Pipes

## 🔄 Entendendo `stdin`, `stdout` e `stderr`

Todo processo de linha de comando no Linux opera com pelo menos dois fluxos de dados fundamentais: entrada padrão (`stdin`) e saída padrão (`stdout`). Um programa lê dados do `stdin` e escreve seus resultados no `stdout`. Entender como controlar ambos é crucial para um trabalho eficaz na linha de comando.

---

### 🟢 `stdout` (Saída Padrão)
É a saída padrão de um comando. Podemos manipular a saída de um programa e redirecioná-la ou anexá-la a um arquivo como nos exemplos abaixo:

**Redirecionando a saída (`>`)** do comando echo para o arquivo `peanuts.txt`. Isso criará um arquivo novo ou **sobrescreverá** o arquivo se ele já existir:
```bash
echo "Hello World" > peanuts.txt
```

**Anexando a saída (`>>`)** ao arquivo `peanuts.txt`. Isso adicionará a saída ao final do arquivo, sem sobrescrever nenhum conteúdo anterior:
```bash
echo "Adicionando uma linha" >> peanuts.txt
```

---

### 📥 `stdin` (Entrada Padrão)
É a entrada padrão de um programa. Geralmente vem do teclado, mas pode ser redirecionada de um arquivo.

**Exemplo Prático com `cat` e `stdin` (`<`)**
Vamos revisitar o arquivo `peanuts.txt` da lição anterior. Considere o seguinte comando:
```bash
cat < peanuts.txt > banana.txt
```
Aqui está uma análise do que acontece:
1. A parte `< peanuts.txt` diz ao shell para redirecionar o `stdin` para o comando `cat`, fazendo com que ele leia de `peanuts.txt` em vez do teclado.
2. O comando `cat` processa sua entrada (neste caso, o conteúdo do arquivo).
3. A parte `> banana.txt` redireciona a saída padrão (`stdout`) do `cat` para um novo arquivo chamado `banana.txt`.

Em última análise, o conteúdo de `peanuts.txt` é copiado para `banana.txt`. Este exemplo demonstra efetivamente como gerenciar fluxos de entrada e saída simultaneamente.

---

### 🔴 `stderr` (Erro Padrão)

**O que é Erro Padrão no Linux?**
No Linux, o `stderr` é um fluxo de saída padrão usado por programas para enviar mensagens de erro e diagnósticos. Ele é **completamente separado** do fluxo de saída padrão (`stdout`), que é usado para a saída normal do programa. 

Por padrão, tanto `stdout` quanto `stderr` enviam sua saída para a tela do seu terminal, razão pela qual você vê a mensagem de erro diretamente misturada com a saída normal. Para controlar o `stderr` e separá-lo, você precisa de um método de redirecionamento diferente.

#### 🔢 Entendendo Descritores de Arquivo
Para gerenciar fluxos de E/S (Entrada/Saída), o sistema usa *descritores de arquivo*. Um descritor de arquivo é um número (ID) que o kernel usa para identificar um arquivo ou fluxo aberto. 

Os descritores de arquivo padrão são:
- `0`: **stdin** (entrada padrão)
- `1`: **stdout** (saída padrão)
- `2`: **stderr** (erro padrão)

O número `2` é o descritor de arquivo dedicado para erros, e podemos usá-lo para controlar para onde as mensagens de falha vão.

#### 📁 Redirecionando `stderr` para um Arquivo
Para redirecionar apenas os erros para um arquivo, você usa o descritor de arquivo `2` colado com o operador `>` (`2>`). 
```bash
ls /fake/directory 2> erros.txt
```
> **Resultado:** O seu terminal ficará silencioso (não mostrará o erro) e a mensagem de "diretório não encontrado" será guardada de forma invisível dentro de `erros.txt`.

---

### 🔀 Combinando `stdout` e `stderr`

E se você quiser capturar **tanto a saída normal quanto as mensagens de erro** no mesmo arquivo? Você pode conseguir isso redirecionando ambos os fluxos ao mesmo tempo.

**O modo clássico:**
```bash
ls /fake/directory /etc/passwd > output.txt 2>&1
```
Vamos analisar isso:
- `> output.txt` redireciona o `stdout` (descritor 1) para o arquivo `output.txt`.
- `2>&1` redireciona o `stderr` (descritor 2) para **o mesmo local** para o qual o `stdout` (descritor 1) está apontando atualmente.
> **⚠️ A ordem é importante!** O `2>&1` envia o erro para o destino atual da saída normal.

**O modo moderno e mais curto:**
No Bash moderno, você pode redirecionar ambos simultaneamente usando `&>`:
```bash
ls /fake/directory /etc/passwd &> output.txt
```

---

### 🗑️ O "Buraco Negro": Descartando Mensagens de Erro

Às vezes, você pode querer executar um comando e ignorar completamente quaisquer mensagens de erro que poluam sua tela. Para fazer isso, você pode redirecionar o `stderr` para um arquivo especial do sistema chamado `/dev/null`. Ele funciona como um "buraco negro" e simplesmente descarta todos os dados gravados nele.

```bash
ls /fake/directory 2> /dev/null
```
Este comando será executado, a saída de erro será enviada para o vazio e descartada, deixando sua tela 100% limpa.

---

## 🔗 O Poder do Pipe (`|`)

O operador *pipe* `|` pega a saída padrão (`stdout`) do comando à sua esquerda e a injeta diretamente como entrada padrão (`stdin`) para o comando à sua direita.

**Exemplo:**
```bash
ls -la /etc | less
```
> Neste caso, nós canalizamos (*piped*) a longa lista de arquivos de `/etc` gerada pelo `ls` diretamente para o leitor paginado `less`, permitindo que você role a tela com calma.

---

### 🔀 Comando `tee`: Dividindo a Saída

E se você quiser ver a saída do comando na sua tela e também salvá-la em um arquivo ao mesmo tempo? É aqui que entra o `tee`. A combinação do Pipe com o `tee` é um clássico para registro de *logs* e monitoramento no Linux.

```bash
ls | tee peanuts.txt
```
> O comando `tee` (pense na conexão "T" de um cano de água) divide o fluxo em duas direções: envia a listagem para o terminal e grava exatamente o mesmo conteúdo dentro de `peanuts.txt`.

#### ⛓️ Encadeamento Avançado (Pipe + Tee + Filtros)

Você pode criar fluxos de trabalho avançados encadeando múltiplos pipes. Um padrão comum é usar o `tee` no meio de uma cadeia de comandos longa. Isso permite salvar um resultado intermediário no meio do caminho enquanto continua a processar os dados.

```bash
ls -la /etc | tee etc_listing.txt | grep "conf"
```
**Este comando faz 3 coisas simultaneamente:**
1. Lista o conteúdo detalhado da pasta `/etc`.
2. Canaliza essa saída para o `tee`, que tira uma cópia salvando tudo em `etc_listing.txt` e repassa o fluxo original adiante.
3. A saída que sobreviveu ao `tee` é canalizada para o filtro `grep`, que mostra na sua tela **apenas** as linhas que contenham a palavra `"conf"`.

> Dominar esses comandos melhorará significativamente sua eficiência como DevOps na linha de comando.
