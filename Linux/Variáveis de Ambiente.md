# 🌐 Comando `env` e Variáveis de Ambiente

Seu sistema Linux usa **variáveis de ambiente** para armazenar informações vitais que o *shell* e outros processos do sistema podem acessar. Essas variáveis contêm dados sobre as configurações e preferências da sua sessão atual.

---

## 🔍 Explorando Variáveis de Ambiente Básicas

Você pode visualizar o valor de uma variável específica colocando o símbolo de cifrão (`$`) antes do nome dela.

**Exemplo 1: Diretório Home**
```bash
echo $HOME
```
> Exibirá o caminho absoluto do seu diretório pessoal (ex: `/home/pete`).

**Exemplo 2: Usuário Atual**
```bash
echo $USER
```
> Exibirá o nome do seu usuário atual logado. Essas informações vêm diretamente do seu ambiente do shell!

---

## 📋 Comando `env`: Listando o Ambiente

Para inspecionar detalhadamente como o seu *shell* está configurado e ver **todas** as variáveis definidas para a sua sessão no momento, use o comando:

```bash
env
```
O comando retornará uma lista enorme no formato de chave-valor. Aqui está um pequeno exemplo do que você vai encontrar:
```text
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/bin
PWD=/home/user
USER=pete
```

---

## 🛤️ A Importância da Variável `PATH`

A variável `$PATH` é, de longe, uma das engrenagens mais cruciais de funcionamento do sistema Linux. 

```bash
echo $PATH
```
> **O que ela faz?** O output será uma lista de vários diretórios separados por dois-pontos (`:`). Sempre que você digita um comando no terminal (como `ls` ou `cat`), o sistema vasculha **exatamente esses diretórios** (nessa exata ordem) em busca do arquivo executável daquele comando. 

**Problema Comum:** 
Se você instalar um programa manualmente em uma pasta não-padrão (ex: `/opt/meu_app/bin`) e tentar digitar o comando dele no terminal, você receberá um erro de `"command not found"`. Isso acontece porque a pasta do seu novo programa não está listada no seu `$PATH`.

**A Solução:**
Para que o sistema passe a reconhecer o seu programa de qualquer lugar, basta adicionar o caminho da pasta onde ele está na sua variável `$PATH`.

---

## 🛠️ Criando Variáveis de Ambiente

### ⏱️ Temporárias (Para a Sessão Atual)
Para criar uma variável que dure apenas enquanto o seu terminal estiver aberto, use o comando `export`:

```bash
export MEU_TESTE="teste"
echo $MEU_TESTE
```
> **Atenção:** Assim que você fechar a janela ou aba desse terminal (e a sessão encerrar), a variável deixará de existir para sempre.

### ♾️ Persistentes (Permanentes entre Sessões)
Para que a sua variável sobreviva ao fechamento do terminal e esteja disponível sempre que você iniciar uma nova janela, você precisa salvá-la de forma definitiva em um **arquivo de inicialização do shell**.

**No caso do Bash (padrão):**
1. Abra o arquivo oculto de configuração usando um editor de texto:
```bash
nano ~/.bashrc
```
2. Desça até o final do arquivo e adicione a declaração da sua variável:
```bash
export MEU_TESTE="teste"
```
3. Salve e saia do editor (no Nano: <kbd>Ctrl</kbd>+<kbd>X</kbd>, depois <kbd>Y</kbd>, e <kbd>Enter</kbd>).
4. Recarregue as configurações do arquivo atual (para ativá-lo sem precisar reiniciar a máquina):
```bash
source ~/.bashrc
```

> **💡 Nota sobre outros Shells:**
> Dependendo do terminal que você usa, o arquivo de configuração mestre muda!
> - **Bash:** `~/.bashrc`
> - **Zsh:** `~/.zshrc` (Muitos usuários de Mac e devs mais avançados utilizam este)
> - **Fish:** `~/.config/fish/config.fish`