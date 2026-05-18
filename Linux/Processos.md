# ⚙️ Gerenciamento de Processos

Entender como os processos funcionam é fundamental para administrar, monitorar e solucionar problemas em um sistema Linux. 

## 🧠 O que é um Processo?

A analogia mais fácil é pensar na diferença entre uma **Receita de Bolo** e o **Bolo sendo assado**:
- O **Programa** (como o navegador ou o `ls`) é o arquivo estático no disco rígido. É a "receita". Ele não faz nada sozinho.
- Quando você executa o programa, o Linux joga o código na memória RAM e dá vida a ele. Essa instância "viva" é o **Processo** (o bolo assando). 

> **💡 Dica:** Se você abrir três janelas de terminal e rodar o comando `cat` nas três, você tem apenas **um** programa no disco, mas **três processos** distintos rodando na RAM, cada um com seu próprio identificador e recursos alocados.

### O Papel do Kernel
O Kernel Linux é o "Gerente Geral" do sistema. Ele é responsável por:
1. **Alocar Recursos:** Dar um pedaço de RAM isolado para cada processo (para que um não interfira no outro).
2. **Escalonador (Scheduler):** Dar fatias de tempo da CPU para centenas de processos. Ele pausa e retoma processos tão rápido que nos dá a *ilusão* de concorrência simultânea.
3. **Limpeza (Coveiro):** Quando um processo morre, o Kernel recolhe a memória e a devolve para o sistema.

---

## 👁️ Monitorando Processos (`ps` e `top`)

O comando `ps` (*Process Status*) tira uma "foto" instantânea dos processos que estão rodando no exato milissegundo em que você aperta Enter.

### Uso Básico
```bash
ps
```
Isso mostra apenas os processos rodando **na janela de terminal atual**. Para ver o sistema inteiro, usamos duas "receitas" clássicas:

### A Visão do Consumo (`ps aux`)
Usa a sintaxe estilo BSD (sem hífen):
- `a`: Todos os processos de todos os usuários.
- `u`: Formato detalhado para o usuário (mostra colunas úteis como `%CPU` e `%MEM`).
- `x`: Inclui processos "soltos" que não estão amarrados a um terminal (daemons).

```bash
ps aux | grep nginx
```
*(No dia a dia, quase sempre combinamos o `ps` com o `grep` para encontrar processos específicos!)*

### A Visão da Hierarquia (`ps -ef`)
Usa a sintaxe System V:
- `-e`: Todos os processos do sistema.
- `-f`: Formato completo, que inclui o importantíssimo **PPID** (ID do Processo Pai), ajudando a descobrir "quem iniciou quem".

### Monitoramento em Tempo Real (`top`)
Enquanto o `ps` é uma foto, o `top` (ou `htop`) é um "vídeo". Ele fornece uma visão dinâmica e em tempo real dos processos, atualizando a cada poucos segundos para mostrar quem está consumindo mais recursos no momento.
```bash
top
```

---

## 🔌 Terminal de Controle (TTY / PTS)

Ao rodar o `ps`, você notará a coluna **TTY** (*Teletype*). O TTY indica a qual terminal aquele processo está "amarrado".
- **TTY1, TTY2...**: Terminais físicos (telas pretas nativas).
- **PTS/0, PTS/1...**: Pseudo-terminais (as janelinhas emuladas que você abre no ambiente gráfico ou VS Code).

> **⚠️ A Regra do Cordão Umbilical:** A maioria dos processos está vinculada ao terminal que os iniciou. Se você clicar no "X" e fechar a janela do terminal, o sistema envia um sinal matando todos os processos atrelados a ela.

**Os Daemons (`?`):**
Servidores Web ou Banco de Dados precisam rodar 24h por dia em segundo plano. Para não serem mortos se alguém fechar o terminal, eles cortam esse "cordão umbilical". Na coluna TTY do `ps`, eles aparecem com um `?`, significando que são independentes.

---

## 🧬 Criação e Terminação de Processos

### O Modelo Fork-Exec (A Mitose)
No Linux, processos não nascem do nada. Eles se dividem como células:
1. **Clonagem (`fork`)**: Um Processo Pai faz uma cópia exata de si mesmo (o Processo Filho). O filho ganha um `PID` próprio, mas guarda o `PPID` apontando para o pai.
2. **Mutação (`execve`)**: O Filho sofre uma "lavagem cerebral", descarta o código original e carrega o código do novo programa que precisa rodar.

> **O Ancestral Supremo:** O processo `PID 1` (tradicionalmente `init`, hoje `systemd`) é o primeiro processo criado pelo Kernel ao ligar o PC. Ele é o ancestral de todos os processos do sistema.

### O Fim do Ciclo (`wait` e `_exit`)
Quando um programa termina, ele chama a função `_exit`. Ele morre e libera os recursos, mas seu "corpo" com o boletim de resultado fica aguardando. O Linux exige que o Processo Pai reconheça o corpo executando a função `wait` (reaping/colheita). Só então o corpo some do sistema.

### Zumbis 🧟 vs. Órfãos 🧒
- **Zumbi (`Z`):** O filho morreu, mas o pai (mal programado) esqueceu de chamar o `wait`. O corpo fica travado na tabela do Kernel como Zumbi. Não gasta CPU, mas ocupa vagas de PIDs no sistema.
- **Órfão:** O filho está vivo, mas o pai fechou de repente. Imediatamente, o ancestral supremo (`PID 1`) adota o órfão, garantindo que ele não vire um zumbi quando terminar.

---

## 🚦 Sinais (Signals) e o comando `kill`

Sinais são a principal forma de comunicação entre processos (IPC). Pense neles como "notificações push" para os programas. E o comando `kill`, apesar do nome, deveria se chamar `enviar_sinal`, pois ele serve apenas para ser o carteiro dessas mensagens.

Os 5 Sinais Fundamentais do DevOps:
- **`SIGTERM` (Sinal 15) - O Pedido Educado:** É o sinal padrão. Pede ao programa para salvar tudo e fechar com calma. Ex: `kill 12345` (ou `kill -15 12345`).
- **`SIGKILL` (Sinal 9) - O Assassino:** Corta a energia do processo. A mensagem vai direto pro Kernel e o processo é obliterado sem aviso. Só use em emergências. Ex: `kill -9 12345`.
- **`SIGINT` (Sinal 2) - Interrupção Manual:** É o que acontece quando você aperta `Ctrl+C` no teclado.
- **`SIGHUP` (Sinal 1) - O Recarregador Silencioso:** Usado como um truque para serviços rodando em segundo plano. Diz ao Nginx ou Apache: *"Recarregue suas configurações sem reiniciar o servidor."*
- **`SIGSEGV` (Sinal 11) - O Escudo do Sistema:** Enviado pelo Kernel matando programas que tentam acessar áreas de memória proibidas (Segmentation Fault).

> **O Truque Ninja (`kill -0`):** Não envia sinal nenhum, apenas "pinga" o processo para checar se ele existe e se você tem permissão para acessá-lo. Muito usado em scripts de automação!

---

## 📊 Estados dos Processos (A Coluna STAT)

Ao rodar `ps aux`, a coluna `STAT` é o "laudo médico" do processo:
- **`R` (Running / Correndo):** Está usando ativamente a CPU ou está na rampa pronto para entrar nela.
- **`S` (Sleep / Sono Leve):** 99% dos processos. Está dormindo esperando algo acontecer (um clique de mouse ou pacote de rede). É interrompível (acorda se receber um `kill`).
- **`D` (Deep Sleep / Coma Ininterrupto):** ⚠️ *Perigo*. Entrou em coma esperando uma peça física de hardware (geralmente o HD) responder. **Nenhum sinal funciona nele, nem o `kill -9`**. Se acumular processos em `D`, é provável que seu HD esteja queimando.
- **`T` (Stopped / Trancado):** Congelado no tempo. Acontece quando você aperta `Ctrl+Z` ou manda um `SIGSTOP` (Sinal 19).
- **`Z` (Zombie):** Processo morto esperando colheita do Pai.

---

## ⚖️ Niceness (Educação / Prioridades)

O Kernel divide o bolo da CPU de acordo com a "Educação" (*Niceness*) dos processos. A escala vai de **-20 a 19**:

- **+19 (Muito Educado / Baixa Prioridade):** "Pode dar CPU para os outros, eu espero as sobras". Ótimo para backups ou renderizações longas em segundo plano.
- **0 (Padrão):** Um programa comum.
- **-20 (Egoísta / Alta Prioridade):** "Saiam da frente que eu preciso da CPU agora!". Usado para processos críticos (como controle de áudio). Apenas o `root` pode definir níveis egoístas (negativos).

```bash
# Iniciar um processo já com prioridade baixa (Educado)
nice -n 15 tar -czf backup.tar.gz /home

# Dar uma bronca e alterar a educação de um processo já em andamento
renice 10 -p 3245
```

---

## 🗂️ A Matrix do Linux: O Sistema de Arquivos `/proc`

A filosofia suprema do Linux é: **Tudo é arquivo**.
O diretório `/proc` não existe fisicamente no disco rígido (tem 0 bytes de tamanho). Ele é um "holograma" gerado em tempo real pelo Kernel na memória RAM.

Se você digitar `ls /proc`, verá centenas de pastas nomeadas com números (ex: `1`, `450`, `12345`). Esses são os **PIDs** dos processos em execução!
Você pode literalmente andar dentro da "cabeça" de um processo:
```bash
# Mostra quanta memória o processo está usando e seu status
cat /proc/12345/status

# Mostra exatamente qual comando iniciou aquele processo
cat /proc/12345/cmdline
```

> **O Segredo Revelado:** Ferramentas incríveis como o `ps`, `top` ou `htop` não fazem mágica. Elas simplesmente entram no `/proc`, leem silenciosamente todos esses arquivos de texto usando coisas parecidas com o `cat` e organizam tudo numa tabela bonita na tela!

---

## 🕹️ Controle de Tarefas (Job Control)

No Linux, você frequentemente esbarra em comandos ou execuções que demoram horas (como backups longos e transferências). Em vez de esperar com a janela do seu terminal travada e inutilizável, você pode usar o **controle de tarefas** para enviar comandos para o segundo plano (*background*) e continuar trabalhando tranquilamente no mesmo local.

### 🏃 Executando direto no Fundo (`&`)
Para iniciar um processo pesado sem travar sua tela, basta adicionar o caractere `&` (e comercial) no final do comando. O terminal retornará pronto para outro comando imediatamente.
```bash
sleep 1000 &
sleep 1001 &
sleep 1002 &
```

### 📋 Listando Tarefas (`jobs`)
Você pode visualizar todos os trabalhos que estão rodando em segundo plano usando este comando nativo.
```bash
$ jobs

[1]    Running     sleep 1000 &
[2]-   Running     sleep 1001 &
[3]+   Running     sleep 1002 &
```
> **💡 Entendendo o Retorno:** A primeira coluna `[1]` fornece o ID da tarefa. O símbolo `+` indica a tarefa em background iniciada mais recentemente, e o `-` marca a penúltima.

### ⏸️ Suspendendo e Recuperando o Terminal (`Ctrl+Z` e `bg`)
E se um comando já estiver rodando e travando sua tela, mas você decidir que precisa do terminal de volta sem perder o que já processou?

1. **Pause:** Suspenda o processo em execução com o atalho <kbd>Ctrl</kbd> + <kbd>Z</kbd>.
2. **Esconda:** Use o comando `bg` *(background)* para mandar a tarefa voltar a correr invisível.

```bash
pete@icebox ~ $ sleep 1003
^Z
[4]+    Stopped     sleep 1003

pete@icebox ~ $ bg
[4]+    sleep 1003 &
```

### 🎯 Trazendo de Volta e Finalizando

**1. Resgatar (`fg`):**
Para "pescar" um processo escondido de volta para a tela, use o comando `fg` *(foreground)*. Você deve especificar qual tarefa deseja pelo ID usando o `%`. 
*(Se executar só o `fg` puro, ele puxa o mais recente com sinalzinho de +)*.
```bash
fg %1
```

**2. Matar o Fundo (`kill`):**
Precisa parar o processo definitivamente lá atrás? O `kill` atua aqui, mas ao invés de PIDs longos, você referencia diretamente a tarefa dele com `%`.
```bash
kill %1
```