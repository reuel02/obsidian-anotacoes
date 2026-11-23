# Overview do Projeto: Sistema de Gestão de Treinamentos Obrigatórios (Training Record)

## 1. Contexto e Objetivo do Projeto

Atualmente, o controle da **Training Record (TR)** (`FORM-SDB-HR-RDC-005-E-R04`) e o cruzamento com a **Matriz HSE** (`FORM-SDB-HSE-373-E-R10`) e a **Matriz Técnica de Capacitação** são realizados manualmente por meio de planilhas. 

### Principais Dores do Processo Atual (Levantamento LEMAT)

1. **Atraso na atualização da TR:** Alta demanda operacional e necessidade de lançar treinamentos manualmente colaborador por colaborador.
2. **Recebimento tardio de evidências:** Demora no fluxo de listas de presença e certificados entre fornecedores externos, instrutores e RH Treinamentos.
3. **Desalinhamento de status de colaboradores:** Falta de integração ágil sobre admissões, desligamentos (Security/RH) e afastamentos temporários como férias, atestados e transferências (CLAP).
4. **Divergências de Matriz vs. Função:** Dificuldade em manter a integridade entre mudanças de função, versões da matriz e histórico de treinamentos de colaboradores Saipem e Subcontratados.
5. **Complexidade para Auditorias:** Esforço manual para consolidar indicadores, Horas-Homem (HH), percentual de aderência à matriz e rastreabilidade para auditorias internas (HSE/Corporativo) e externas (Clientes).

### Entidades Envolvidas no Fluxo

* **RH Treinamentos:** Gestão operacional, agendamento de turmas, validação de certificados e monitoramento da TR.
* **HSE:** Definição dos requisitos técnicos/legais (NRs, PGR) e gestão das versões da Matriz de Treinamentos.
* **Saúde Ocupacional:** Gestão de vencimentos do ASO (PCMSO).
* **CLAP:** Atualização de disponibilidade do colaborador (férias, atestados, licenças, transferências).
* **Security:** Controle de ativos/inativos, subcontratados e liberação de crachá de operação.
* **Gestores das Áreas:** Planejamento, liberação de equipe e solicitação de treinamentos específicos/designações.
* **Fornecedor Externo:** Apoio operacional no lançamento de evidências e emissão de certificados.

---

## 2. Premissa de Arquitetura: Como transformar a Matriz em Dados?

A decisão central de modelagem deste sistema é **não criar uma coluna para cada treinamento** (modelo de planilha horizontal), mas sim utilizar um **modelo relacional normalizado** (linhas em tabela de junção).

Em vez desta estrutura rígida:

| Função | Treinamento A | Treinamento B |
| :--- | :---: | :---: |
| Operador | Sim | Sim |

Adotamos a estrutura relacional:

| Função | Treinamento Exigido |
| :--- | :--- |
| Operador | Treinamento A |
| Operador | Treinamento B |

**Vantagens:** Escalabilidade infinita sem alterar estrutura de banco de dados, versionamento histórico de matrizes e facilidade para cruzar dados no Power BI e na aplicação.

---

## 3. Modelagem da Base de Dados (Arquitetura em 4 Camadas)

A arquitetura foi dividida em **9 tabelas** organizadas em 4 blocos lógicos:

| Bloco Lógico                           | Tabelas                                                                               | Papel no Sistema                                                                                                                |
| :------------------------------------- | :------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------ |
| **1. Quem e o Quê (Cadastros Base)**   | `1. Funções`<br>`2. Colaboradores`<br>`3. Treinamentos`                               | Dados mestres de pessoas, cargos e catálogo de treinamentos/ASO.                                                                |
| **2. O que é Exigido (Governança)**    | `4. Versões de Matriz`<br>`5. Requisitos da Matriz`<br>`7. Aplicabilidade Individual` | Regras da Matriz por função e controle de exceções/designações individuais auditáveis.                                          |
| **3. Motor de Conformidade (Status)**  | `8. Matriz Individual (PIT)`                                                          | Fotografia em tempo real do status de cada requisito por colaborador (*Conforme, Em Atenção, Expirado, Planejado, Dispensado*). |
| **4. Operação e Histórico (Execução)** | `9. Turmas / Agendamentos`<br>`6. Treinamentos Realizados`                            | Gestão de convocações em lote, listas de presença e histórico imutável de certificados.                                         |

---

### Detalhamento das Tabelas

#### 1 - Funções
Cadastro padronizado de cargos conforme estrutura corporativa.

| Identificador da Função (PK) | Nome da Função CTCO  | Job Code | Professional Role      |
| :--------------------------- | :------------------- | :------- | :--------------------- |
| FUNC-001                     | Estagiário de TI     | 113005   | Information Technology |
| FUNC-002                     | Operador de Produção | 113007   | Operations             |

---

#### 2 - Colaboradores
Base unificada de efetivo próprio (Saipem) e terceiros (Subcontratados), integrando visões de RH, CLAP e Security.

| GHRSID (PK) | Nome          | Função (Lookup `Funções`) | Vínculo       | Empresa   | Unidade / Site | Projeto       | Departamento | Local / Expatriado | Tipo TERZI | Gênero | Situação Cadastral (RH/Security) | Disponibilidade (CLAP) | Crachá de Operação |
| :---------- | :------------ | :------------------------ | :------------ | :-------- | :------------- | :------------ | :----------- | :----------------- | :--------- | :----- | :------------------------------- | :--------------------- | :----------------- |
| 2043998     | Reuel Andrade | Estagiário de TI          | Saipem        | Saipem    | CTCO           | Não aplicável | TI           | Local              | -          | M      | Ativo                            | Disponível             | Sim                |
| 9001234     | Carlos Silva  | Operador de Produção      | Subcontratado | Empresa X | Guarujá        | Projeto Alfa  | Operações    | Local              | Terzi A    | M      | Ativo                            | Férias                 | Sim                |

---

#### 3 - Treinamentos (Catálogo de Requisitos)
Catálogo único contendo treinamentos normativos, técnicos, corporativos e exames condicionantes.
> **Decisão de Arquitetura:** O **ASO (Atestado de Saúde Ocupacional)** é tratado como um item/requisito dentro desta tabela (Categoria = Saúde Ocupacional), permitindo reaproveitar todo o motor de cálculo de vencimentos, alertas e bloqueios de conformidade.

| Código (PK) | Nome                       | Categoria | Descrição                             | Cluster (Tipo)             | Modalidade Padrão  | Nível do Treinamento |
| :---------- | :------------------------- | :-------- | :------------------------------------ | :------------------------- | :----------------- | :------------------- |
| TRN-NR35    | Trabalho em Altura - NR 35 | HSE       | Capacitação para trabalho acima de 2m | Legislação Brasileira (NR) | Interno ou Externo | Básico               |
| SAU-ASO     | ASO - Exame Periódico      | Saúde     | Atestado de Saúde Ocupacional (PCMSO) | Legislação Brasileira      | Externo            | Não Aplicável        |

*(Valores de Cluster/Tipo: HSE, Legislação Brasileira).*

---

#### 4 - Versões de Matriz

Controle de revisões das matrizes oficiais (ex: `FORM-SDB-HSE-373-E-R10` para HSE e Matriz Técnica de Capacitação).

| ID Matriz (PK) | Nome da Matriz                | Código do Documento | Revisão | Início de Vigência | Fim de Vigência | Situação  | Documento de Referência (Anexo/Link) |
| :------------- | :---------------------------- | :------------------ | :------ | :----------------- | :-------------- | :-------- | :----------------------------------- |
| MAT-HSE-10     | Matriz de Treinamento HSE     | FORM-SDB-HSE-373-E  | R10     | 01/01/2026         | -               | Publicada | Link SharePoint                      |
| MAT-TEC-01     | Matriz Técnica de Capacitação | FORM-SDB-TEC-001    | R02     | 01/03/2026         | -               | Publicada | Link SharePoint                      |

*(Valores de Situação: Rascunho, Publicada, Encerrada).*

---

#### 5 - Requisitos da Matriz

Tabela de junção que define quais treinamentos são exigidos para cada função em uma determinada versão de matriz.

| Versão da Matriz (Lookup) | Função (Lookup) | Treinamento (Lookup) | Critério de Aplicação | Prazo para Atendimento (Admissão/Mudança) | Regra de Reciclagem (Validade em Meses/Anos) | Carga Horária Formação (h) | Carga Horária Reciclagem (h) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| MAT-HSE-10 | Operador de Produção | TRN-NR35 | Obrigatório para a função | Imediato (Antes da atividade) | 24 meses | 8h | 8h |
| MAT-HSE-10 | Operador de Produção | SAU-ASO | Obrigatório geral | Na admissão | 12 meses | 0h | 0h |

---

#### 6 - Treinamentos Realizados (Histórico de Evidências)

Registro histórico e imutável de todas as capacitações e exames concluídos pelo colaborador ao longo do tempo (para fins de auditoria e cálculo de HH.

| Colaborador (Lookup) | Treinamento (Lookup) | Turma Origem (Lookup Opcional) | Tipo | Data de Início | Data de Conclusão | Carga Horária Realizada | Data de Vencimento Calculada | Modalidade Realizada | Instrutor | Localização | Nº My People | Certificado / Evidência (Anexo) | Situação da Validação |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Carlos Silva | TRN-NR35 | TUR-2026-089 | Inicial | 10/10/2026 | 10/10/2026 | 8h | 10/10/2028 | Interno | João Souza | CTCO Guarujá | MP-99812 | cert_nr35.pdf | Validado |

---

#### 7 - Aplicabilidade Individual (Exceções e Designações)

Registra desvios controlados em relação à matriz padrão da função.
* **Exemplo 1 (Exigência Adicional):** Colaborador pertence à **Brigada de Incêndio** ou **CIPA**. Além dos requisitos de sua função, recebe essa exigência extra.
* **Exemplo 2 (Não Aplicável / Suspensão):** Colaborador tem cargo de Operador, mas atua exclusivamente em atividade administrativa sem exposição ao risco.
* **Regra de Governança:** Nenhuma dispensa é livre. Toda decisão exige justificativa, anexo de evidência e aprovação formal da área responsável (HSE / Gestor).

| Colaborador (Lookup) | Treinamento / Requisito (Lookup) | Decisão | Motivo / Justificativa | Início da Aplicação | Fim da Aplicação | Responsável pela Validação | Evidência da Decisão (Anexo) | Status da Aprovação |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Carlos Silva | TRN-BRIGADA | Exigência Adicional | Designado como membro da Brigada | 01/02/2026 | 01/02/2028 | Coord. HSE | designacao.pdf | Aprovado |

*(Valores de Decisão: Exigência Adicional, Não Aplicável, Suspensão Temporária).*

---

#### 8 - Matriz Individual do Colaborador / PIT (Motor de Conformidade)

Tabela central de controle que consolida **o que é exigido vs. o que foi realizado** para cada colaborador. Em vez de manter múltiplas linhas do mesmo treinamento ao longo dos anos (papel da Tabela 6), esta tabela possui **apenas 1 registro ativo por Colaborador + Treinamento**, refletindo o status atual na Training Record.

| Colaborador (Lookup) | Treinamento (Lookup) | Origem da Exigência | Aplicabilidade Atual | Último Treinamento Realizado (Lookup `Tab 6`) | Turma Agendada (Lookup `Tab 9`) | Data da Última Realização | Data de Vencimento Atual | Status de Conformidade |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Carlos Silva | TRN-NR35 | Matriz da Função | Obrigatório | Reg #1049 | - | 10/10/2026 | 10/10/2028 | **Concluído (Conforme)** |
| Carlos Silva | TRN-NR33 | Matriz da Função | Obrigatório | - | TUR-2026-102 | - | - | **Planejado** |
| Carlos Silva | SAU-ASO | Matriz da Função | Obrigatório | Reg #0812 | - | 25/10/2025 | 25/10/2026 | **Em Atenção** |

---

#### 9 - Turmas / Agendamentos de Treinamento (Operação em Lote)
Criada para eliminar o gargalo operacional de atualização manual individual. Permite planejar treinamentos, convocar múltiplos colaboradores e concluir a realização em lote com upload único de Lista de Presença.

| Código da Turma (PK) | Treinamento (Lookup) | Tipo (Inicial / Reciclagem) | Data Prevista Início | Data Prevista Fim | Carga Horária | Modalidade (Interno/Externo) | Instrutor / Fornecedor | Localização | Status da Turma | Lista de Presença (Anexo Único) | Colaboradores Convocados (Relação N:N) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| TUR-2026-102 | TRN-NR33 | Reciclagem | 28/10/2026 | 29/10/2026 | 16h | Interno | Instrutor HSE | CTCO | Agendada | - | [Lista de Participantes] |

*(Valores de Status da Turma: Planejada/Agendada, Em Andamento, Concluída, Cancelada).*

---

## 4. Regras do Motor de Validação e Cálculos Automáticos

### 4.1. Como o sistema descobre quais treinamentos um colaborador precisa?
A geração e atualização da tabela **`8 - Matriz Individual do Colaborador`** segue a equação relacional:

$$\text{Exigências Ativas}(C) = \left( \text{Requisitos da Matriz Publicada}(F) \cup \text{Exigências Adicionais}(C) \right) - \text{Dispensas/Suspensões}(C)$$

1. **Ao admitir um colaborador ou alterar sua Função ($F$):** O sistema busca todos os itens na tabela `5 - Requisitos da Matriz` das matrizes vigentes e cria os vínculos na tabela `8 - Matriz Individual`.
2. **Ao aprovar uma Exceção na tabela `7 - Aplicabilidade Individual`:**
   * Se for *Exigência Adicional*, insere o requisito na tabela `8`.
   * Se for *Não Aplicável* ou *Suspensão*, altera a aplicabilidade na tabela `8` para "Dispensado/Suspenso" (retirando do cálculo de pendências).
3. **Ao agendar o colaborador em uma `9 - Turma`:** O status do requisito na tabela `8` muda automaticamente para **Planejado**.
4. **Ao concluir uma `9 - Turma` ou inserir certificado individual na `6 - Treinamentos Realizados`:** O sistema gera o registro histórico na tabela `6` e atualiza a tabela `8` com a nova `Data de Vencimento` e status **Concluído (Conforme)**.

### 4.2. Cálculo Automático de Status do Requisito
Para cada requisito obrigatório na tabela `8 - Matriz Individual`, o status é atualizado diariamente conforme as regras da Training Record:

* **Dispensado / Não Aplicável:** Possui exceção aprovada vigente na Tabela 7.
* **Planejado:** Colaborador está vinculado a uma Turma ativa/agendada (Tabela 9) para aquele treinamento.
* **Pendente (Sem Registro):** Requisito obrigatório que nunca foi realizado e não possui turma agendada.
* **Concluído (Conforme):** Possui treinamento validado e a `Data de Vencimento` é maior que $\text{Hoje} + 30\text{ dias}$ (ou treinamento sem validade).
* **Em Atenção (Próximo ao Vencimento):** `Data de Vencimento` está entre $\text{Hoje}$ e $\text{Hoje} + 30\text{ dias}$ *(parametrização ajustável por regra de antecedência)*.
* **Expirado (Vencido):** `Data de Vencimento` é menor que $\text{Hoje}$.

### 4.3. Indicadores de Gestão e Auditoria (KPIs)
Com a tabela `8` e a tabela `6` estruturadas, o sistema fornece nativamente:
* **% de Atendimento da Matriz (Aderência):** 
  $$\frac{\text{Total de Requisitos "Concluídos (Conformes)"}}{\text{Total de Requisitos Obrigatórios Ativos (excluindo Dispensados)}} \times 100$$
* **Horas-Homem (HH) de Treinamento:** Soma da `Carga Horária Realizada` na Tabela `6` por período, departamento, projeto ou empresa (Saipem vs. Subcontratadas).