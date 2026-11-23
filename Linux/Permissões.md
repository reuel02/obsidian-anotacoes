# 🔒 Permissões de Arquivo e Diretório

No Linux, tudo é considerado um arquivo (até mesmo diretórios e dispositivos), e gerenciar o acesso a esses arquivos é uma habilidade crítica. Entender as permissões de arquivo é fundamental para a segurança e administração do sistema. 

---

## 🔎 Introdução às Permissões

Quando listamos arquivos em formato detalhado, vemos uma sequência de caracteres que definem suas permissões. Vejamos um exemplo usando o comando `ls -l`:

```bash
ls -l Desktop/
# drwxr-xr-x 2 pete penguins 4096 Dez 1 11:45 .
```

Este resultado fornece uma riqueza de informações, mas vamos nos concentrar na primeira coluna, `drwxr-xr-x`, que representa o tipo de arquivo e suas permissões.

### Decodificando a String de Permissão
A string de permissão tem quatro partes principais. O primeiro caractere indica o tipo de arquivo:
- `d`: Diretório.
- `-` (hífen): Arquivo regular.

Os **nove caracteres seguintes** representam as permissões reais. Eles são divididos em três conjuntos de três caracteres cada. Para deixar mais claro, podemos visualizá-los assim:

`d | rwx | r-x | r-x`

Cada caractere nesses conjuntos corresponde a uma permissão específica:
- `r` (*Read*): Permissão de leitura.
- `w` (*Write*): Permissão de gravação.
- `x` (*Execute*): Permissão de execução.
- `-` (hífen): Nenhuma permissão concedida.

> **💡 Dica:** A significância dessas permissões pode mudar ligeiramente dependendo de ser um arquivo ou um diretório. Por exemplo, a permissão de execução (`x`) em um diretório permite que você "entre" nele (usando `cd`), enquanto em um arquivo, permite que você o execute como um programa.

### Níveis de Acesso: Usuário, Grupo e Outros
Os três conjuntos de permissões se aplicam a diferentes níveis de acesso na seguinte ordem:

1. **Usuário (Proprietário)**: O primeiro conjunto (`rwx`) se aplica ao proprietário do arquivo (no exemplo acima, `pete`). O proprietário tem permissões de leitura, gravação e execução.
2. **Grupo**: O segundo conjunto (`r-x`) se aplica ao grupo associado ao arquivo (no exemplo, `penguins`). Os membros deste grupo têm permissões de leitura e execução, mas não podem gravar no arquivo.
3. **Outros**: O conjunto final (`r-x`) se aplica a todos os outros usuários no sistema. Eles têm permissões de leitura e execução.

---

## 🛠️ Modificando Permissões (`chmod`)

Quando você precisar modificar os direitos de acesso a arquivos ou diretórios, a principal ferramenta que usará é o comando `chmod` (*change mode*). Entender como alterar permissões é uma habilidade fundamental. O comando oferece dois métodos: **modo simbólico** e **modo numérico**.

### Modo Simbólico
O modo simbólico é frequentemente considerado mais legível porque usa letras para representar os alvos e as permissões:
- `u` (*user/owner* - usuário/proprietário)
- `g` (*group* - grupo)
- `o` (*others* - outros)
- `a` (*all* - todos: usuário, grupo e outros)

Para adicionar ou remover permissões, usamos os sinais de `+` e `-`:

```bash
# Adiciona a permissão de execução para o dono do arquivo
chmod u+x myfile

# Remove a permissão de escrita para o grupo
chmod g-w myfile

# Adiciona permissão de escrita para o dono E para o grupo simultaneamente
chmod ug+w myfile
```

### Modo Numérico (Octal)
O modo numérico permite definir todas as permissões para o usuário, grupo e outros simultaneamente usando um número de três dígitos, onde as permissões são representadas por valores matemáticos:
- `4`: leitura (`r`)
- `2`: escrita (`w`)
- `1`: execução (`x`)

Para definir um conjunto, você soma os números (ex: ler, escrever e executar = 4 + 2 + 1 = `7`).

```bash
chmod 755 myfile
```
Como esse comando funciona? Vamos detalhar o número `755`:
- **7 (Usuário)**: 4 + 2 + 1 ➔ Permissões de leitura, escrita e execução (`rwx`).
- **5 (Grupo)**: 4 + 0 + 1 ➔ Permissões de leitura e execução (`r-x`).
- **5 (Outros)**: 4 + 0 + 1 ➔ Permissões de leitura e execução (`r-x`).

> **⚠️ Considerações de Segurança:** Embora o `chmod` seja essencial, é crucial usá-lo com cuidado. Definir recursivamente permissões totais (`chmod -R 777 /diretorio`) é uma prática perigosa que concede a todos acesso total de leitura, escrita e execução. Sempre aplique o **princípio do menor privilégio**, concedendo apenas as permissões estritamente necessárias.

---

## 👥 Permissões de Propriedade (`chown` e `chgrp`)

Em um sistema Linux, cada arquivo e diretório é atribuído a um proprietário e um grupo. Gerenciar a propriedade é fundamental para controlar o acesso.

### Alterando a Propriedade do Usuário (`chown`)
Para transferir a propriedade de um arquivo para um usuário diferente, usa-se o comando `chown` (*change owner*). Geralmente, você precisa de privilégios de superusuário (`sudo`).

```bash
# Altera o proprietário do myfile para o usuário 'patty'
sudo chown patty myfile
```

### Alterando a Propriedade do Grupo (`chgrp`)
Da mesma forma, você pode alterar o grupo associado a um arquivo usando o comando `chgrp` (*change group*). Isso permite que todos os membros do novo grupo tenham acesso.

```bash
# Define o grupo do myfile para 'whales'
sudo chgrp whales myfile
```

### Alterando Usuário e Grupo Simultaneamente
Para maior eficiência, o comando `chown` permite alterar tanto a propriedade do usuário quanto a do grupo em uma única etapa, separando os nomes por dois pontos (`:`).

```bash
sudo chown patty:whales myfile
```

---

## 🎭 Permissões Especiais Avançadas

Além das permissões clássicas de Leitura, Escrita e Execução, o Linux oferece três permissões especiais avançadas que cobrem casos de uso bem específicos de administração de sistemas.

### 1. `SUID` (Set User ID)

O **SUID** resolve o seguinte problema: e se um usuário precisar executar um programa que necessita temporariamente de permissões de *root* (como alterar a própria senha no `/etc/shadow`), mas não podemos dar a senha de *root* a ele?

Quando você adiciona o bit SUID em um arquivo executável, você diz ao Linux: *"Sempre que alguém executar este programa, não use as permissões de quem o executou. Em vez disso, **rode o programa usando as permissões do dono (owner) do arquivo**."*

Como o comando `/usr/bin/passwd` pertence ao `root`, ele tem a flag SUID ativada:
```bash
ls -l /usr/bin/passwd
# -rwsr-xr-x 1 root root 47032 Dez 1 11:45 /usr/bin/passwd
```
> **Atenção visual:** Note o **`s`** minúsculo (`-rws...`) no lugar do `x` do proprietário. É ele que garante os privilégios temporários.

**Modificando o SUID:**
```bash
# Simbólico
sudo chmod u+s myfile
# Numérico (O SUID tem valor 4)
sudo chmod 4755 myfile
```

#### Entendendo as Permissões de Processo (Real UID vs Efetivo UID)
Se o `passwd` roda como root, por que o João não consegue mudar a senha da Maria? Isso ocorre devido aos três diferentes identificadores (`UID`) associados a cada processo:
- **UID Efetivo:** Dá os direitos de acesso ao processo. Quando o João roda o `passwd`, o SUID altera o UID Efetivo para `0` (Root), permitindo editar o `/etc/shadow`.
- **UID Real:** Rastreia quem *realmente* iniciou o programa. O UID Real do João permanece sendo o dele (ex: `1000`). O programa `passwd` verifica esse número internamente e só permite alterar a senha ligada a esse UID Real.
- **UID Salvo:** Permite que o programa troque temporariamente entre o UID Real e o UID Efetivo, garantindo que o programa só use o "poder de root" nos milissegundos exatos em que precisar.

### 2. `SGID` (Set Group ID)

Se o SUID empresta permissões do usuário proprietário, o **SGID** empresta permissões do **grupo proprietário**. Ele tem dois comportamentos completamente diferentes:

**Em Arquivos Executáveis:**
Funciona de forma idêntica ao SUID. Quem executar o programa o fará com os privilégios do grupo dono do arquivo.

**Em Diretórios (Uso mais comum):**
É aqui que o SGID brilha no trabalho em equipe. No Linux, um arquivo novo nasce pertencendo ao grupo primário de quem o criou. Mas...
> **A Regra Mágica:** Se um diretório tem a flag SGID ativada, **qualquer arquivo ou subdiretório criado lá dentro vai herdar automaticamente o GRUPO do diretório pai**, e não o grupo de quem o criou!

Ideal para pastas compartilhadas de equipes (ex: a pasta `/var/www/html` do grupo `devs`). Qualquer arquivo criado lá nascerá pertencendo aos `devs`, permitindo que toda a equipe colabore sem erros de "Acesso Negado".

**Modificando o SGID:**
Note que o `s` aparecerá na parte de grupo (`drwxr-sr-x`).
```bash
# Simbólico
sudo chmod g+s meudiretorio/
# Numérico (O SGID tem valor 2)
sudo chmod 2775 meudiretorio/
```

### 3. `Sticky Bit` ("Bit Pegajoso")

Hoje em dia, o **Sticky Bit** só tem utilidade prática quando aplicado em diretórios, e ele serve para resolver um problema de segurança muito específico em pastas compartilhadas como o `/tmp`.

O diretório `/tmp` tem permissão total (`777`). Como a permissão de *Escrita* (`w`) numa pasta permite não só criar, mas **excluir** qualquer arquivo de lá, no modo normal, qualquer usuário poderia apagar os arquivos temporários de outros usuários, quebrando seus sistemas.

Quando ativamos o Sticky Bit, transformamos o diretório numa pasta de **"Exclusão Restrita"**.
> *"Você pode criar arquivos aqui, mas **só tem permissão para apagar ou renomear os arquivos que VOCÊ MESMO criou!**"*

Com o Sticky Bit, um arquivo só pode ser deletado pelo dono do arquivo, dono do diretório ou superusuário.

**Identificando e Modificando o Sticky Bit:**
Ele é visualizado como um `t` no final das permissões (`drwxrwxrw**t**`):
```bash
ls -ld /tmp
# drwxrwxrwt 17 root root 4096 Dez 15 11:45 /tmp

# Adicionando de forma Simbólica
chmod +t /tmp/pasta_compartilhada

# Adicionando de forma Numérica (O Sticky Bit tem valor 1)
chmod 1777 /tmp/pasta_compartilhada
```

*(Dica: Juntando SUID (4), SGID (2) e Sticky Bit (1), você dominou as permissões especiais!)*

---

## 🎛️ Definindo o Padrão (`umask`)

Todo arquivo criado no sistema vem com um conjunto padrão de permissões (geralmente `644` para arquivos e `755` para diretórios). Você pode alterar esse padrão usando o comando `umask` (*user file-creation mode mask*).

O detalhe do `umask` é que, em vez de **adicionar** permissões, ele funciona como uma "máscara que **subtrai**" as permissões dos valores máximos básicos (`666` para arquivos e `777` para diretórios).

```bash
umask 022
```
No exemplo acima (o padrão de muitas distros), a máscara `022` remove os direitos de gravação (`w`) para grupos (`2`) e outros (`2`). Logo, diretórios nascem como `755` (`777 - 022`) e arquivos como `644` (`666 - 022`).

> **Nota:** Para tornar a configuração do `umask` permanente, ela deve ser adicionada aos arquivos de inicialização do seu shell, como o `~/.bashrc` ou `~/.profile`.
