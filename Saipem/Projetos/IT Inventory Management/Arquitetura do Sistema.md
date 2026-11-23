

# Automações recomendadas

No MVP:

1. **Gerar código interno** durante o cadastro;
2. **Registrar movimentação automaticamente** quando status, localização ou responsável forem alterados;
3. **Avisar sobre estoque mínimo** para itens controlados por quantidade;
4. **Enviar confirmação de empréstimo ou entrega**;
5. **Notificar equipamentos aguardando regularização no ServiceNow**;
6. **Registrar falhas durante importações**.

Posteriormente:

- revisão periódica de inventário;
- notificação de item parado em manutenção;
- alerta de equipamento sem conferência física;
- geração automática de relatório mensal;
- importação programada do ServiceNow.



# Controle de acesso

Perfis recomendados:

## Administrador

- configura categorias e localizações;
- importa dados;
- corrige registros;
- administra usuários;
- acessa todos os relatórios.

## Operador de estoque

- cadastra;
- atualiza;
- movimenta;
- entrega;
- recebe;
- realiza inventário.

## Consulta

- visualiza equipamentos e indicadores;
- não altera os registros.

## Auditor/Gestor

- consulta histórico;
- visualiza divergências;
- exporta dados;
- acompanha indicadores.


## Etapa 3 — Protótipo

Criar as telas:

1. Home;
2. Estoque;
3. Detalhes;
4. Cadastro;
5. Movimentação;
6. Histórico;
7. Administração.

## Etapa 4 — Construção do MVP

- criar listas ou tabelas;
- configurar relações;
- construir o Canvas App;
- criar validações;
- criar fluxos no Power Automate;
- carregar dados de demonstração.

## Etapa 5 — Testes

Cenários mínimos:

- cadastrar equipamento com service tag;
- cadastrar equipamento sem service tag;
- impedir service tag duplicada;
- transferir equipamento;
- emprestar e devolver;
- enviar para manutenção;
- consultar histórico;
- restringir edição para usuário de consulta;
- importar dados válidos e inválidos.

## Etapa 6 — Apresentação

Roteiro da demonstração:

1. mostrar o problema atual;
2. apresentar a visão unificada;
3. localizar um equipamento por service tag;
4. localizar um item sem service tag;
5. cadastrar um novo ativo;
6. fazer uma movimentação;
7. mostrar o histórico;
8. mostrar indicadores;
9. explicar a futura integração com ServiceNow.