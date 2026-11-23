# 🚀 Linux Cheatsheet: Guia de Sobrevivência Rápida

Este é o seu **cérebro externo**. Use esta folha de cola rápida no dia a dia para lembrar daquele comando que fugiu da memória durante uma emergência ou tarefa.

## 🧭 Navegação Básica

| Comando | O que faz | Exemplo Rápido |
| :--- | :--- | :--- |
| `pwd` | Mostra o caminho completo de onde você está. | `pwd` |
| `cd` | Entra em um diretório. | `cd /home/usuario/Documentos` |
| `cd ..` | Volta um diretório (sobe um nível para a pasta "pai"). | `cd ..` |
| `cd ~` | Vai direto para a pasta pessoal do seu usuário (home). | `cd ~` |
| `ls` | Lista os arquivos e pastas locais. | `ls` |
| `ls -al` | Lista tudo (inclusive ocultos `-a`) com detalhes de peso/dono (`-l`). | `ls -al` |
| `clear` | Limpa toda a poluição visual da tela do terminal. | `clear` |

## 📂 Manipulação de Arquivos e Pastas

| Comando | O que faz | Exemplo Rápido |
| :--- | :--- | :--- |
| `touch` | Cria um arquivo vazio (ou atualiza a data/hora de um existente). | `touch nota.txt` |
| `mkdir` | Cria um novo diretório (pasta). | `mkdir nova_pasta` |
| `mkdir -p` | Cria uma árvore de diretórios inteira de uma só vez (pastas dentro de pastas). | `mkdir -p pasta_pai/pasta_filha/` |
| `cp` | Copia arquivos ou diretórios. | `cp origem.txt /destino/` |
| `cp -r` | Copia diretórios **com conteúdo dentro** (recursivo). | `cp -r pasta_origem/ /destino/` |
| `mv` | Move arquivos de local **OU** apenas renomeia-os. | `mv antigo.txt novo.txt` |
| `rm` | Deleta um arquivo (cuidado, não existe lixeira!). | `rm lixo.txt` |
| `rm -rf` | Deleta uma pasta cheia, à força bruta e sem perguntar. **Extremo perigo!** | `rm -rf pasta_lotada/` |
| `rmdir` | Deleta uma pasta, mas apenas e unicamente se ela estiver vazia. (Seguro) | `rmdir pasta_vazia` |

## 📖 Leitura e Exibição de Conteúdo

| Comando | O que faz | Exemplo Rápido |
| :--- | :--- | :--- |
| `echo` | Imprime um texto na tela (ecoa). | `echo "Olá Mundo"` |
| `file` | Inspeciona sem abrir e diz qual é o real formato de um arquivo. | `file imagem.jpg` |
| `cat` | Lê um arquivo inteiro de uma vez e cospe o texto todo no terminal. | `cat config.txt` |
| `cat >` | Cria um arquivo e deixa você digitar texto ali mesmo. (Salve com `Ctrl+D`). | `cat > novo_texto.txt` |
| `less` | Abre arquivos pesados em modo de leitura paginada. Navegue com as setas, saia com <kbd>q</kbd>. | `less log_gigante.log` |

## ⚙️ Gerenciamento de Processos e Tarefas

| Comando | O que faz | Exemplo Rápido |
| :--- | :--- | :--- |
| `ps aux` | Tira uma "foto" de todos os processos do sistema agora (Uso de RAM/CPU). | `ps aux` |
| `top` | Mostra os processos em "vídeo" em tempo real (como o Gerenciador de Tarefas). | `top` |
| `kill -15` | Pede educadamente para um processo salvar os dados e fechar (SIGTERM). | `kill 1234` |
| `kill -9` | Corta a energia e assassina o processo à força imediata (SIGKILL). | `kill -9 1234` |
| `&` *(no fim)* | Manda o comando rodar direto escondido no segundo plano (fundo). | `sleep 100 &` |
| `jobs` | Lista todas as tarefas ocultas rodando em segundo plano no momento. | `jobs` |
| `fg` | Puxa uma tarefa do fundo de volta pra tela (foreground). | `fg %1` |
| `bg` | Manda uma tarefa pausada voltar a rodar no fundo (background). | `bg` |

## ⌨️ Atalhos de Ouro do Teclado

| Atalho de Teclado | O que faz / Qual o poder |
| :---: | :--- |
| <kbd>Ctrl</kbd> + <kbd>C</kbd> | **Mata:** Interrompe abruptamente o que estiver rodando na sua tela no momento. |
| <kbd>Ctrl</kbd> + <kbd>Z</kbd> | **Pausa:** Suspende e congela o que estiver rodando na tela e devolve seu terminal. |
| <kbd>Ctrl</kbd> + <kbd>R</kbd> | **Busca no Histórico:** Modo de pesquisa reversa. Comece a digitar para achar comandos antigos. |
| <kbd>Ctrl</kbd> + <kbd>L</kbd> | **Vassoura:** Atalho ninja que limpa a tela de uma vez (mesmo efeito do comando `clear`). |
| <kbd>Ctrl</kbd> + <kbd>D</kbd> | **Encerra Sessão:** Sai do usuário atual ou fecha a gravação do texto do comando `cat >`. |

---
> *"Na dúvida, não sofra tentando adivinhar. Todo grande mestre consulta o manual com `man comando` ou pesquisando no Google!"*
