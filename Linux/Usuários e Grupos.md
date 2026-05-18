# 👥 Usuários e Grupos

Em qualquer sistema operacional multiusuário, gerenciar usuários e grupos é um conceito fundamental. Esta é uma parte central dos conceitos básicos do Linux, projetada para controle de acesso e permissões. Quando um processo é executado, ele o faz como o usuário que o iniciou. Da mesma forma, o acesso a arquivos e a propriedade dependem de permissões, impedindo que um usuário acesse os documentos privados de outro.

Cada usuário em um sistema Linux recebe um diretório pessoal (*home directory*), geralmente localizado em `/home/username`. Este diretório é onde seus arquivos e configurações específicos do usuário são armazenados, embora o caminho exato possa variar entre as distribuições Linux.

O sistema identifica usuários com um ID de Usuário (`UID`) e grupos com um ID de Grupo (`GID`). Embora usemos nomes de usuário legíveis por humanos, o sistema operacional depende desses IDs numéricos exclusivos para todas as tarefas relacionadas a permissões. Grupos são simplesmente coleções de usuários, facilitando o gerenciamento de permissões para várias contas de uma só vez.

---

## 🦸 O Superusuário e o Comando `sudo`

Dentro da hierarquia de usuários e grupos no Linux, um usuário se destaca acima de todos os outros: o `root`, também conhecido como superusuário. O usuário `root` tem poder ilimitado, capaz de acessar qualquer arquivo e gerenciar qualquer processo. Operar como `root` continuamente é arriscado, pois um erro simples pode danificar o sistema.

Para mitigar esse risco, usuários autorizados podem executar comandos com privilégios de `root` usando o comando `sudo` (*superuser do*). Isso permite tarefas administrativas sem fazer login como o usuário `root`. Entender como usar o `sudo` corretamente é essencial para a administração do sistema.

### Root
No Linux, certas tarefas administrativas exigem privilégios elevados. Esses privilégios pertencem a uma conta especial conhecida como usuário `root`. Embora você possa fazer login diretamente como `root`, é frequentemente mais seguro e gerenciável obter acesso de superusuário temporariamente.

### O Comando `su`
Além do comando `sudo`, você pode usar `su` (*substitute user*) para obter privilégios de superusuário. Quando executado sem um nome de usuário, o `su` tenta abrir uma nova sessão de *shell* para o usuário `root`, solicitando a senha do `root`.
```bash
su
```
Você também pode usar este comando para mudar para qualquer outro usuário no sistema, desde que saiba a senha dele.

> **⚠️ Riscos de um Shell Root Persistente:** Usar `su` para abrir um *shell* `root` tem desvantagens significativas. Operar continuamente como usuário `root` aumenta o risco de cometer um erro crítico que altera o sistema. Além disso, as ações realizadas em um *shell* `root` não são registradas sob sua conta de usuário pessoal, dificultando a auditoria das alterações do sistema. Por essas razões, a melhor prática é usar `sudo` para comandos individuais que exigem acesso de superusuário.

### O Arquivo `sudoers`
Então, como o sistema determina quem tem permissão para usar `sudo`? O acesso é controlado por um arquivo de configuração localizado em `/etc/sudoers`. Este arquivo lista os usuários e grupos que têm permissão para executar comandos como superusuário.

> **💡 Dica:** Para editar este arquivo com segurança, você deve **sempre** usar o comando `visudo`. Este utilitário abre o arquivo `sudoers` em um editor de texto e realiza uma verificação de sintaxe antes de salvar, o que ajuda a prevenir erros de configuração que poderiam bloquear seu acesso administrativo.

---

## 🪪 Entendendo o `/etc/passwd`

No Linux, nomes de usuário são rótulos legíveis por humanos, mas o sistema identifica os usuários com um ID de Usuário (`UID`) exclusivo. O mapeamento entre nomes de usuário e UIDs é armazenado no arquivo `/etc/passwd`, um componente crítico para o gerenciamento de usuários.

Para visualizar seu conteúdo, você pode usar um comando simples:
```bash
cat /etc/passwd
```
Este arquivo exibe uma lista de todos os usuários do sistema e informações detalhadas sobre eles. Cada linha representa uma única conta de usuário.

### Desmembrando os Campos do `/etc/passwd`
A linha típica neste arquivo, muitas vezes a primeira, se parece com isto:
```text
root:x:0:0:root:/root:/bin/bash
```
Esta entrada para o usuário `root` contém sete campos separados por dois pontos (`:`). Entender a estrutura do `/etc/passwd` é fundamental para gerenciar usuários:

1. **Nome de Usuário**: O nome de *login* do usuário (ex: `root`).
2. **Senha**: Um espaço reservado para a senha criptografada do usuário. A senha real não é armazenada aqui por razões de segurança.
   - Um `x` indica que a senha criptografada está no arquivo `/etc/shadow`.
   - Um `*` (asterisco) significa que a conta está bloqueada e não pode ser usada para *login*.
   - Um campo em branco significa que o usuário não tem senha.
3. **ID de Usuário (`UID`)**: O identificador numérico exclusivo para o usuário. O usuário `root` sempre tem um UID de `0`.
4. **ID do Grupo (`GID`)**: O identificador numérico para o grupo primário do usuário.
5. **Campo GECOS**: Um campo de comentário que tradicionalmente contém informações extras como o nome completo do usuário, número de telefone ou localização do escritório. É delimitado por vírgulas.
6. **Diretório Home**: O caminho absoluto para o diretório pessoal do usuário (ex: `/root`).
7. **Shell Padrão**: O interpretador de linha de comando padrão do usuário, que é executado no *login* (ex: `/bin/bash`).

### Usuários do Sistema e Contas Especiais
Ao inspecionar o arquivo `/etc/passwd`, você notará muitas contas que não pertencem a usuários humanos. Estas são **contas de sistema** usadas para executar serviços ou processos específicos com permissões limitadas, aumentando a segurança do sistema. Por exemplo, o usuário `daemon` é usado para executar processos em segundo plano (*daemons*).

### Editando o Arquivo `/etc/passwd`
> **⚠️ Cuidado:** Embora tecnicamente você possa editar o arquivo `/etc/passwd` diretamente usando um editor de texto ou o comando `vipw`, isso é **fortemente desencorajado**. Edições manuais podem facilmente introduzir erros de sintaxe, potencialmente bloqueando seu acesso ao sistema ou causando instabilidade. É sempre mais seguro e confiável usar utilitários de linha de comando dedicados (`useradd`, `usermod`, `userdel`) para gerenciar contas de usuário.

---

## 🔐 Entendendo o `/etc/shadow`

O arquivo `/etc/shadow` é usado para armazenar informações sobre a autenticação do usuário. Ele requer permissões de leitura de superusuário.

```bash
sudo cat /etc/shadow
# root:MyEPTEa$6Nonsense:15000:0:99999:7:::
```
Você notará que ele se parece muito com o conteúdo do `/etc/passwd`; no entanto, no campo da senha, você verá um *hash* da senha criptografada. Os campos são separados por dois pontos (`:`), da seguinte forma:

1. **Nome de usuário**
2. **Senha criptografada**
3. **Data da última alteração de senha**: Expressa como o número de dias desde 1º de janeiro de 1970. Um `0` significa que o usuário deve alterar sua senha na próxima vez que fizer *login*.
4. **Idade mínima da senha**: Dias que um usuário terá que esperar antes de poder alterar sua senha novamente.
5. **Idade máxima da senha**: Número máximo de dias antes que o usuário tenha que alterar sua senha.
6. **Período de aviso de senha**: Número de dias antes que uma senha expire que o usuário será avisado.
7. **Período de inatividade da senha**: Número de dias após a expiração de uma senha onde ainda é permitido o *login*.
8. **Data de expiração da conta**: Data em que a conta do usuário será desativada.
9. **Campo reservado** (para uso futuro).

*Nota:* Na maioria das distribuições atuais, a autenticação do usuário não depende apenas do arquivo `/etc/shadow`; existem outros mecanismos em vigor, como `PAM` (*Pluggable Authentication Modules*), que lidam com a autenticação.

---

## 👥 Entendendo o `/etc/group`

No Linux, o gerenciamento de permissões para múltiplos usuários é simplificado através do uso de grupos. O arquivo central para isso é o `/etc/group`, que define os grupos no sistema e seus membros.

### O que é o arquivo `/etc/group`?
É um arquivo de texto simples que contém a lista de todos os grupos de usuários. Cada grupo pode receber permissões específicas para arquivos e diretórios, permitindo que os administradores gerenciem os direitos de acesso de forma eficiente para vários usuários de uma só vez. 

### Visualizando Informações de Grupo
Para inspecionar o conteúdo deste arquivo, você pode usar:
```bash
cat /etc/group
# root:*:0:pete
```

### Estrutura do Arquivo `/etc/group`
Semelhante ao `/etc/passwd`, cada linha representa um único grupo e contém quatro campos separados por dois pontos (`:`).

1. **Nome do Grupo**: O nome exclusivo do grupo (ex: `root`).
2. **Senha do Grupo**: Recurso legado e raramente usado. Geralmente você verá um asterisco (`*`) ou um `x`.
3. **ID do Grupo (`GID`)**: O identificador numérico exclusivo do grupo. (ex: `0`).
4. **Lista de Usuários**: Uma lista separada por vírgulas de nomes de usuários que são membros deste grupo (ex: `pete`).

---

## 🛠️ Ferramentas de Gerenciamento de Usuários

Embora muitos ambientes corporativos dependam de sistemas dedicados para gerenciamento de identidade, entender os fundamentos do gerenciamento de usuários do Linux diretamente no terminal é uma habilidade crucial. 

### Adicionando Usuários
Para criar um novo usuário, você pode usar o comando `useradd`. É um utilitário que cria uma nova conta baseada nos valores padrão encontrados em `/etc/default/useradd`. 
*(Nota: Embora alguns sistemas também ofereçam `adduser`, que é um script mais interativo, `useradd` é o padrão universal).*

```bash
sudo useradd bob
```
Executar este comando adiciona uma entrada para o usuário "bob" no `/etc/passwd`, configura as associações de grupo padrão e cria uma entrada no `/etc/shadow` para armazenar a senha com segurança.

### Removendo Usuários
Para remover uma conta, você pode usar o comando `userdel`. Este comando reverte efetivamente as alterações feitas pelo `useradd`.

```bash
sudo userdel bob
```
> **Dica:** Por padrão, este comando não remove o diretório pessoal (*home*) do usuário. Você pode usar a flag `-r` (`userdel -r bob`) para garantir que o diretório *home* seja excluído junto com o usuário.

### Alterando Senhas
O comando `passwd` é usado para definir ou alterar a senha de um usuário.
```bash
passwd bob
```
- Um **usuário comum** pode executar este comando sem especificar nomes para alterar sua própria senha.
- O **administrador (`root`)** pode executá-lo passando o nome do usuário para alterar a senha de qualquer um. Ao fazer isso, o sistema solicitará a nova senha diretamente, sem pedir a antiga.