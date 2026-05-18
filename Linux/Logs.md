# 📋 Logs: O Diário do Sistema

Entender o registro de sistema (*system logging*) é um dos pilares mais vitais para quem trabalha com servidores e quer aprender Linux a fundo. O Kernel, os serviços em segundo plano e os daemons do seu servidor estão vivos e trabalhando silenciosamente 24h por dia. 

Toda essa atividade frenética é anotada rigorosamente e salva em arquivos de texto legíveis por humanos que chamamos de **Logs**. Eles são, literalmente, a caixa-preta e o diário de bordo do seu sistema operacional.

---

## 🩺 Para Que Servem os Logs?
Os logs são o primeiro lugar (e muitas vezes o único) que um profissional DevOps olha quando um servidor cai ou um banco de dados para de responder. Eles são essenciais para:
- Monitorar a saúde invisível do sistema.
- Investigar causas de travamentos e bugs (*troubleshooting*).
- Auditar segurança (Ex: *Quem tentou logar como root 50 vezes ontem à noite?*).

> **📁 Onde Eles Moram?**
> A gigantesca maioria dos logs no Linux fica concentrada num único lugar: a pasta `/var/log`. O prefixo `/var` significa *Variable*, ou seja, um diretório feito especificamente para arquivos que crescem e mudam o tempo todo!

---

## 🕵️‍♂️ Quem Escreve os Logs? (O Protocolo `syslog`)
Como toda essa fofoca gerada por centenas de programas é coletada sem virar uma bagunça completa? Através de um serviço de protocolo central chamado **`syslog`**.

A magia acontece através de um daemon principal (geralmente chamado de **`rsyslogd`** nos Linux mais modernos) que fica eternamente acordado no escuro. 
Ele atua como um grande "Porteiro Central". Quando um programa qualquer no seu PC faz algo de importante, ele não escreve no disco; ele grita pro porteiro. O `rsyslogd` ouve a mensagem de evento, filtra, e com base nas suas configurações, decide:
- Guardar a mensagem no arquivo oficial.
- Exibir um alerta vermelho direto na tela do console.
- Ignorar o aviso e jogá-lo no lixo.

---

## 📖 Lendo um Arquivo de Log
Embora softwares grandes (como Apache ou Docker) adorem criar suas próprias pastinhas separadas lá dentro de `/var/log/`, a linha de log oficial do sistema segue uma anatomia muito clara e padronizada.

**A Anatomia Clássica:**
Uma linha de log tradicional sempre entrega 4 respostas vitais: `[Quando?] [Onde?] [Quem?] [O que Aconteceu?]`

**Exemplo Prático (Espiando o Log do Sistema):**
```bash
# Abrimos o arquivo com o comando 'less' para paginar a leitura
pete@icebox:~$ less /var/log/syslog

Jan 27 07:41:32 icebox anacron[4650]: Job `cron.weekly' started
```

> **💡 Dissecando a Linha do Terminal Acima:**
> - **Quando?** No dia `Jan 27` pontualmente às `07:41:32`
> - **Onde?** No nosso servidor/máquina chamada `icebox`
> - **Quem?** O processo chamado `anacron` (que no momento estava usando o PID de número `4650`)
> - **O que rolou?** Ele nos relatou o evento: *"Job `cron.weekly` started"* (A rotina de serviços semanais foi iniciada).

---

## ⚙️ Mergulhando no `rsyslog`
Como conversamos, o serviço `syslog` tradicional ganhou um upgrade enorme nos últimos anos. O **Rsyslog** é a versão "anabolizada" e moderna que vem rodando como padrão em quase todas as distribuições Linux atuais.

O `rsyslog` coleta a esmagadora maioria das mensagens (com exceção das mensagens confidenciais de senhas e autenticação) e as despeja no grande poço central: o arquivo `/var/log/syslog` (ou `/var/log/messages` no CentOS/RedHat).

### 🎛️ As Regras do Porteiro (Configurações)
O porteiro não joga as coisas nos arquivos de forma aleatória; ele obedece a regras estritas. 
Para descobrir exatamente quais arquivos o seu sistema está alimentando no momento, você precisa espionar as regras de configuração do `rsyslog`, que moram dentro da pasta `/etc/rsyslog.d/`.

Vamos dar uma olhada no arquivo de configuração padrão do sistema:

```bash
pete@icebox:~$ less /etc/rsyslog.d/50-default.conf

# Log by facility (Separação por tipo de evento):
auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/syslog
#cron.*                         /var/log/cron.log
#daemon.*                       -/var/log/daemon.log
kern.*                          -/var/log/kern.log
mail.*                          -/var/log/mail.log
```

> **💡 Entendendo a Regra (Seletor vs Ação):**
> Olhe a formatação do bloco acima. Ela é dividida em duas colunas invisíveis.
> - **Coluna da Esquerda (O Seletor):** Diz ao sistema *qual evento* ele deve observar. Exemplo: `kern.*` significa "Preste atenção em todo e qualquer evento (`*`) disparado pelo Kernel".
> - **Coluna da Direita (A Ação):** Informa o *destino final*. Exemplo: Se o evento do Kernel ocorrer, pegue o texto e guarde no arquivo `- /var/log/kern.log`.
> 
> *(Lembre-se: Alguns programas modernos ignoram o rsyslog e cuidam dos próprios arquivos sozinhos. Portanto, sempre vasculhe a pasta `/var/log` inteira quando estiver caçando um problema).*

---

### 🧪 Testando os Logs Manualmente (`logger`)
Vamos ver a engrenagem funcionar de verdade? Você mesmo pode injetar uma mensagem manual no sistema e testar se o porteiro está anotando tudo certinho. Fazemos isso usando o pequeno utilitário nativo chamado `logger`:

```bash
# Injeta uma mensagem manual na corrente do Syslog
logger -s "Hello, o servidor está vivo!"
```

Se você abrir o seu arquivo `/var/log/syslog` (usando o `less` ou o `tail`) um segundo após rodar o comando acima, você verá a sua exata mensagem cravada na última linha do diário oficial do sistema!

---

## 📚 Arquivos de Registro Geral (Por onde começar?)

A pasta `/var/log` pode abrigar dezenas de arquivos diferentes, mas quando o seu sistema Linux apresenta um erro fantasma e você não faz ideia de qual programa está falhando, existem dois arquivos mestre de "Registro Geral" que são o seu porto seguro inicial para *Troubleshooting* (Solução de Problemas).

### 1️⃣ O Bate-Papo Diário (`/var/log/messages`)
Em muitas distribuições Linux (tradicionalmente na família CentOS/Red Hat), este arquivo atua como a caixa de entrada padrão do servidor.
- **O que ele coleta:** Ele captura uma ampla gama de eventos e mensagens informativas "não-críticas" do kernel, daemons e serviços. É como um bate-papo do sistema dizendo *"Serviço X iniciou"*, *"Rotina Y foi feita"*.
- **Quando usar:** Excelente para ter uma visão geral da saúde e atividade diária do servidor.

### 2️⃣ O Diário Abrangente (`/var/log/syslog`)
Na família Debian/Ubuntu, o `syslog` é o rei absoluto.
- **O que ele coleta:** Ele contém uma coleção incrivelmente abrangente de eventos (compondo muitas vezes as mesmas mensagens que o `messages` guardaria). O `syslog` abraça **tudo** o que acontece na máquina, exceto as mensagens confidenciais de autenticação e senhas (que vão para um arquivo hiper-protegido).
- **Quando usar:** É o arquivo perfeito para depuração pesada, quando você precisa rastrear a causa raiz de um problema complexo numa linha do tempo segundo a segundo.

> **⚠️ Atenção à Evolução (O Futuro dos Logs):**
> Embora a leitura de arquivos de texto puros no `/var/log` seja a base imutável que todo profissional Linux deve saber, as distribuições hiper-modernas vêm sofrendo mutações. Alguns sistemas novos pararam de usar o `syslog` clássico em favor do moderno **`systemd-journald`**. Esse sistema novo não escreve arquivos de texto puro legíveis, mas sim logs binários que você precisa ler usando um comando especial chamado `journalctl`.

---

## 🧠 Lendo a Mente do Sistema (Registro do Kernel)

O Kernel do Linux é o cérebro absoluto do sistema operacional. Ele é o responsável por gerenciar a memória e conversar com todas as placas e cabos físicos ligados ao hardware. Por ser a peça central, acessar o diário pessoal dele é a sua arma definitiva para Solução de Problemas em Hardware.

### 🔄 O "Ring Buffer" da Memória e o `dmesg`
No exato milissegundo em que você aperta o botão de ligar o servidor, antes mesmo dos discos rígidos funcionarem direito, o Kernel já está acordado testando a placa-mãe, memória e as portas USB. Ele guarda tudo o que descobre em um espaço temporário da memória RAM chamado de **Buffer de Anel** (*Ring Buffer*).

Você pode espiar esse buffer a qualquer momento na tela disparando o famoso comando:
```bash
# Cospe na tela todos os logs do hardware desde o momento que o PC foi ligado
dmesg
```
*(Parte do que esse comando exibe é salva no arquivo `/var/log/dmesg`, porém esteja ciente de que esse arquivo costuma ser apagado e reescrito do zero a cada nova reinicialização da máquina! Se você espetou um pendrive novo e ele não funcionou, o `dmesg` é o primeiro lugar para olhar).*

### 🗄️ O Cofre Persistente (`kern.log`)
E se você quiser investigar um erro gravíssimo de Kernel que ocorreu há 3 dias e a máquina já reiniciou de lá pra cá? O `dmesg` não vai te ajudar, porque ele limpa a memória no boot.

Para ter um registro de longo prazo confiável, nós lemos o arquivo oficial contínuo:
```bash
/var/log/kern.log
```
Este arquivo é o destino definitivo e persistente para os eventos do Kernel (inclusive ele também salva o histórico das saídas antigas do `dmesg`). Se você precisa investigar um evento do passado remoto que já desapareceu do buffer temporário, este é o lugar certo para procurar!

> **🔧 O Diferencial Profissional (Por que isso importa?)**
> Entender como rastrear a saúde do Kernel separa os amadores dos administradores experientes. Quando uma placa de rede cai misteriosamente, você não precisará chutar a causa. Examinando o `kern.log` ou a saída do `dmesg`, você será capaz de ler o erro exato do driver de hardware falhando e corrigir o problema na raiz.

---

## 🔐 Investigação Policial (Registro de Autenticação)

Saber exatamente **quem** logou no seu servidor, **quando** fez isso e **como** conseguiu acesso é o pilar número 1 da Segurança da Informação. O Linux não perdoa e anota absolutamente tudo relacionado a senhas, usuários e credenciais em arquivos ultrassecretos dedicados exclusivamente à **Autenticação**.

### 🛡️ O Cofre de Segurança (`auth.log`)
Por questões óbvias de auditoria, o sistema nunca mistura fofocas bobas do dia a dia (como *"A impressora ligou"*) com alertas críticos (como *"Tentativa de invasão detectada na conta Root"*). 

Na imensa família Debian/Ubuntu, o destino de todas as informações de credenciais é o arquivo:
```bash
/var/log/auth.log
```
Ler este arquivo é o feijão com arroz de qualquer DevOps ou Analista de Segurança buscando diagnosticar por que um usuário não consegue logar ou caçando incidentes suspeitos na rede.

*(⚠️ **Cuidado com a Distribuição:** Em servidores da família Red Hat/CentOS, esse arquivo muda de nome. Ele é tradicionalmente chamado de `/var/log/secure`).*

### 🔍 Dissecando um Log de Segurança
Vamos dar uma olhada em como uma linha de log de autenticação se parece na vida real e como extrair informações vitais dela:

**A Linha no Arquivo:**
```bash
Jan 31 10:37:50 icebox pkexec: pam_unix(polkit-1:session): session opened for user root by (uid=1000)
```

> **💡 O que ocorreu na prática? (O Boletim de Ocorrência)**
> Ler logs de segurança é literalmente uma investigação policial. Cada pedaço da linha revela um culpado:
> - **O Momento do Fato:** O evento aconteceu no dia `Jan 31`, pontualmente às `10:37:50`.
> - **O Local do Crime:** Ocorreu na nossa máquina chamada `icebox`.
> - **A Arma do Crime:** O programa acionado foi o `pkexec` (que usou o módulo de checagem `pam_unix`).
> - **A Ação Final:** *"session opened for user root by (uid=1000)"*. Ou seja, um usuário comum (dono do ID 1000) acabou de conseguir privilégios absolutos e abriu uma sessão encarnando o poderoso usuário Root!

---

## ♻️ Gerenciamento e Limpeza (O Comando `logrotate`)

Os arquivos de log operam como uma bomba-relógio de armazenamento no disco rígido. Softwares pesados geram uma quantidade insana de texto por minuto. Se ninguém fizer nada, o arquivo `syslog` vai crescer infinitamente até engolir 100% do armazenamento do servidor e causar uma pane geral (*Crash*). 

Além disso, tentar usar o `less` ou buscar uma palavra dentro de um arquivo de texto de 50 Gigabytes de peso é virtualmente impossível.

### 🔄 O Que é a Rotação de Logs?
A solução da indústria para esse crescimento infinito chama-se "Rotação de Logs" (*Log Rotation*). No mundo Linux, a ferramenta rainha e padrão absoluto para fazer isso é o utilitário automatizado **`logrotate`**.

O `logrotate` é ativado todo dia de madrugada (usando um agendador de tarefas chamado Cron) para fazer o trabalho sujo. A rotação geralmente obedece a um ciclo de 4 etapas automáticas:

1. **Renomeia o Antigo:** Ele pega o arquivo gigante atual (ex: `app.log`) e o isola renomeando-o (ex: para `app.log.1`).
2. **Cria uma Folha em Branco:** Ele cria um `app.log` zerado e limpo, para que o sistema comece a escrever as coisas do dia de hoje sem travar.
3. **Comprime o Passado:** Ele pega os logs dos dias anteriores e passa na prensa do `gzip` (que estudamos antes!) para economizar 90% do HD (ex: `app.log.2.gz`).
4. **Joga Fora o Lixo:** Ele deleta implacavelmente os arquivos comprimidos que já ultrapassaram o limite de idade (ex: joga fora tudo que for mais antigo que 30 dias).

> **💡 A Máquina Perfeita:** Com isso, o arquivo de "hoje" fica sempre levinho para você ler, os de "ontem" ficam salvos comprimidos caso precise auditar, e seu disco rígido nunca sofre de super-lotação!

### ⚙️ Como Configurar o `logrotate`
As configurações do `logrotate` seguem a exata mesma organização elegante de pastas modulares que você já aprendeu ao estudar o `apt` e o `rsyslog`:

- **A Configuração Global:** Mora no arquivo mestre `/etc/logrotate.conf`.
- **A Pasta Modular (`logrotate.d`):** Regras de limites de rotação para cada software individual ficam separadas como arquivinhos dentro do diretório `/etc/logrotate.d/`. Quando você criar a sua própria aplicação que gera muitos logs, é só soltar um arquivo de limites lá dentro e o sistema cuidará da limpeza sozinho!

