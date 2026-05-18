# ✂️ Comandos de Manipulação de Texto

No Linux, gerenciar, filtrar e processar arquivos de texto é uma das tarefas mais comuns do dia a dia do DevOps. Aqui estão os utilitários mais poderosos para manipular dados via terminal, geralmente associados ao uso do `Pipe (|)`.

---

## 🔎 Filtros Essenciais

### `grep` (Pesquisa e Filtragem Avançada)
O comando `grep` permite pesquisar em arquivos ou fluxos de dados por linhas que correspondam a um padrão específico.

**Uso Básico do `grep`**
```bash
# Encontra todas as linhas que contêm a palavra "fox"
grep fox sample.txt
```

**Correspondência de Padrões Avançada com `-e`**
O sinalizador `-e` diz explicitamente ao `grep` que o próximo argumento é o padrão. Útil ao procurar padrões que começam com um hífen (`-`), que poderiam ser interpretados como uma opção.
```bash
grep -e "-v" /path/to/some/file.conf
```

**Flags Úteis do `grep`**
- **Pesquisa Insensível a Maiúsculas/Minúsculas**: Use a flag `-i`.
  ```bash
  grep -i somepattern somefile
  ```
- **Contar Linhas Correspondentes**: Use a flag `-c` para contar em vez de exibir.
  ```bash
  grep -c fox sample.txt
  ```
- **Mostrar Apenas a Correspondência**: Use a flag `-o` para ver apenas a parte da linha que corresponde.
  ```bash
  grep -o fox sample.txt
  ```
- **Pesquisar Padrões de um Arquivo**: Use a flag `-f` para ler padrões de um arquivo.
  ```bash
  grep -f patterns.txt sample.txt
  ```

**Combinando `grep` com Outros Comandos**
A verdadeira força do `grep` é desbloqueada quando combinado com *pipes* (`|`).
```bash
# Filtrar variáveis de ambiente para encontrar aquelas relacionadas ao usuário
env | grep -i User

# Encontrar todos os arquivos que terminam em .txt em um diretório
ls /somedir | grep '.txt$'
```

### `head` e `tail` (Leitura de Pontas)
Muitas vezes você precisa inspecionar o conteúdo de arquivos muito grandes (como logs do sistema) sem que a sua tela seja bombardeada de textos incontroláveis.
- **`head`**: Exibe o início do arquivo (por padrão, as 10 primeiras linhas).
- **`tail`**: Exibe o final do arquivo (por padrão, as 10 últimas linhas).

```bash
# Lendo as 15 primeiras linhas:
head -n 15 /var/log/syslog

# Lendo as 20 últimas linhas:
tail -n 20 /var/log/syslog
```

> **🔥 Monitoramento em Tempo Real:** Uma das funcionalidades mais poderosas é usar a flag `-f` (*follow*) no `tail`. Ele não encerra o comando, mas fica esperando e imprimindo na tela novas linhas assim que elas são adicionadas ao arquivo. Ideal para ver logs rodando ao vivo!
> ```bash
> tail -f /var/log/syslog
> ```

### `wc` (Word Count)
Conta linhas (`-l`), palavras (`-w`) e caracteres (`-c`) de um arquivo ou da saída de outro comando.
```bash
# Conta exatamente quantas linhas existem no arquivo (ou na listagem de um diretório)
ls -la | wc -l
```

---

## 🔪 Recorte e Junção Simples

### `cut` (Extração de Colunas)
O `cut` extrai porções de texto de um arquivo. 
- **Por caractere (`-c`)**: Extrai uma posição exata de caractere. Ex: Extrair o 5º caractere com `cut -c 5`.
- **Por campo/coluna (`-f`)**: Extrai texto baseado em colunas delimitadas.

**Exemplo (Cortando por campo delimitado):**
Por padrão, o `cut` usa a tabulação (`TAB`) como delimitador entre os campos. Você pode alterar isso para qualquer outro caractere (como `,` ou `;`) usando a flag `-d`.
```bash
# Extrai a 1ª coluna (-f 1) de um texto onde as palavras são separadas por ponto-e-vírgula (-d ";")
cut -f 1 -d ";" arquivo.txt
```

### `paste` (Mesclagem Horizontal)
Enquanto o `cat` junta as linhas de arquivos de cima para baixo (verticalmente), o comando `paste` mescla as linhas de arquivos **lado a lado** (horizontalmente).
```bash
# Com a flag -s, ele junta todas as linhas do arquivo em uma única linha separada por espaços
paste -d ' ' -s arquivo.txt
```

---

## 🧩 Divisão e União Avançada

### `split` (Dividindo Arquivos Grandes)
Pega um arquivo gigante e quebra em pedaços menores e mais fáceis de manusear.
```bash
# Divide o arquivo em várias partes (por padrão, quebra a cada 1000 linhas)
split arquivo_gigante.log
```
> **Dica:** Você pode personalizar o limite da quebra especificando a contagem de linhas com a flag `-l` ou dividindo por tamanho de arquivo com a flag `-b` (ex: `-b 10M` para pacotes de 10 megabytes).

### `join` (Juntando por Campo Comum)
O comando `join` é maravilhoso! Pense nele como um "PROCV" do Excel ou um "JOIN" de banco de dados SQL direto no seu terminal. Ele combina linhas de dois arquivos com base em um campo idêntico comum entre eles.

> **⚠️ Requisito Crucial:** Para que o `join` funcione corretamente, as colunas usadas para a junção em ambos os arquivos devem estar **ordenadas previamente**.

**Exemplo Prático:**
Se o `arquivo1.txt` tem os campos "ID e Nome" e o `arquivo2.txt` tem "ID e Sobrenome", ao juntar:
```bash
join arquivo1.txt arquivo2.txt
```
Ele mesclará as informações na mesma linha baseando-se no ID (que por padrão deve ser a 1ª coluna). Você pode especificar colunas diferentes para usar como chave com as flags `-1` (para o arquivo 1) e `-2` (para o arquivo 2). Ex: `join -1 2 -2 1` (Juntar usando a 2ª coluna do arq1 com a 1ª coluna do arq2).

---

## 📏 Espaçamento: `expand` e `unexpand`

Espaçamentos inconsistentes criam um pesadelo visual. Dependendo do editor do programador, as tabulações (`TAB`) podem ter tamanhos diferentes, bagunçando todo o alinhamento.
- **`expand`**: Converte os `TABs` em espaços convencionais (por padrão, 8 espaços por TAB).
- **`unexpand`**: Faz o oposto, convertendo blocos contínuos de espaço de volta para tabulações visando reduzir o tamanho do arquivo.

**Salvando o resultado:**
Lembre-se que o comando apenas imprime a mudança na tela do terminal. Para salvar o conserto, você deve redirecionar a saída para um novo arquivo:
```bash
expand codigo.py > codigo_formatado.py
```
> **Dica:** O `unexpand` só converte os espaços iniciais da linha. Para forçar a conversão de *todas as instâncias* de espaço perdidas no meio do arquivo, não esqueça da flag `-a`.


### `sort` (Ordenação)
Esse comando ordena arquivos de texto em ordem alfabética.

Usando as flags `-r` (*reverse*) ou `-n` (*numeric*) é possível alterar o tipo de ordenação.

---

### `tr` (Tradução e Exclusão)
Esse comando traduz ou exclui caracteres da entrada padrão. É uma ferramenta útil para manipulação simples de texto e é frequentemente usada com *pipes* para processar a saída de outros comandos.

O uso mais comum do `tr` é substituir um conjunto de caracteres por outro. Por exemplo, você pode facilmente traduzir todos os caracteres minúsculos para maiúsculos.
```bash
echo "hello world" | tr a-z A-Z
# HELLO WORLD
```

**Excluindo Caracteres com `-d`**
Outra funcionalidade poderosa é a capacidade de excluir caracteres específicos usando a opção `-d` (*delete*). Isso é particularmente útil para limpar texto. Por exemplo, se você deseja remover todos os dígitos de uma *string*:
```bash
echo "My address is 123 Main Street" | tr -d '0-9'
# My address is  Main Street
```

**Comprimindo Caracteres Repetidos**
O comando `tr` também pode comprimir caracteres repetidos em uma única ocorrência usando a opção `-s` (*squeeze*). Isso é ótimo para normalizar texto com espaços em branco extras.
```bash
echo "Hello      World,   how   are   you?" | tr -s ' '
# Hello World, how are you?
```

---

### `uniq` (Remoção de Duplicatas)
O comando `uniq` (*unique*) é uma ferramenta essencial para ajudar a filtrar e gerenciar linhas duplicadas dentro de um arquivo de texto.

**Remoção Básica de Duplicatas**
A função principal do comando `uniq` é remover linhas adjacentes duplicadas.
```bash
uniq reading.txt
```

**Opções Avançadas de Filtragem**
O comando `uniq` também fornece várias opções para uma análise mais detalhada:
- Para contar as ocorrências de cada linha, use a flag `-c` (*count*):
  ```bash
  uniq -c reading.txt
  ```
- Para exibir apenas as linhas que **não** são repetidas (únicas), use a flag `-u` (*unique*):
  ```bash
  uniq -u reading.txt
  ```
- Para exibir apenas as linhas que **são** repetidas, use a flag `-d` (*duplicated*):
  ```bash
  uniq -d reading.txt
  ```

> **⚠️ A Importância da Ordenação:** O `uniq` só detecta linhas duplicadas se elas estiverem **diretamente adjacentes**. Se as duplicatas estiverem espalhadas pelo arquivo, o `uniq` não as identificará. Para resolver isso, você deve primeiro ordenar o conteúdo do arquivo com `sort`.
> ```bash
> sort reading.txt | uniq
> ```

---

### `wc` e `nl` (Contagem e Numeração)
Dois utilitários fundamentais para analisar arquivos de texto.

**Contagem com o Comando `wc`**
O comando `wc` (*word count*) fornece um resumo do conteúdo de um arquivo.
```bash
wc /etc/passwd
#  96     265    5925 /etc/passwd
# Linhas, palavras e bytes, respectivamente.
```

**Obtendo Contagens Específicas**
- `-l`: Mostra apenas a contagem de linhas.
- `-w`: Mostra apenas a contagem de palavras.
- `-c`: Mostra apenas a contagem de bytes.
```bash
wc -l /etc/passwd
# 96
```

**Numerando Linhas com o Comando `nl`**
O `nl` (*number lines*) lê um arquivo e exibe seu conteúdo com números de linha adicionados ao início de cada linha. Especialmente útil para revisar *scripts* ou arquivos de configuração.
```bash
nl file1.txt
#      1 i
#      2 like
#      3 turtles
```