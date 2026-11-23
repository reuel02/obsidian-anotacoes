
Algumas funções no PowerApps não são delegadas e não conseguem processar registros acima do limite padrão configurado. 500 até 2000 mil registros.

É importante considerar a delegação para uma fonte de dados que possivelmente tenha mais de 2000 registros. Não considerar a delegação nesses casos pode ocasionar um mal funcionamento da aplicação e inconsistência nos dados processados.

### Exemplos de Funções não delegáveis

- First, FirstN, Last, LastN
- Choices
- Concat
- Collect, ClearCollect (nenhuma dessas funções retorna um aviso de delegação, mas elas não são delegáveis)
- CountIf, RemoveIf, UpdateIf
- GroupBy, Ungroup

### Funções delegáveis parcialmente aceitas

As funções de modelagem de tabela abaixo são parcialmente delegáveis. Isso significa que as fórmulas em seus argumentos podem ser delegadas. No entanto, a saída dessas funções está sujeita ao limite de registros de não delegação.

- AddColumns
- DropColumns
- ShowColumns
- RenameColumns

Compreender sua fonte de dados e seu impacto potencial na funcionalidade do aplicativo é fundamental ao criar aplicativos. Neste módulo, exploramos o conceito de delegação no Power Apps: o que é, os fatores que influenciam sua disponibilidade e as maneiras de ajustar os limites de delegação. Estas são as principais lições:

- A delegação aumenta o desempenho minimizando as trocas de dados entre o Power Apps e a fonte de dados, permitindo que os cálculos ocorram nos dados de back-end, e não no Power Apps.

- A capacidade de delegar varia de acordo com a função, o predicado e a fonte de dados usada.

- Quando a delegação não é possível, o Power Apps alerta o criador do aplicativo no Power Apps Studio, mas não notifica o usuário do aplicativo.

- Por padrão, uma consulta não delegável retorna 500 registros, mas esse limite pode ser aumentado para no máximo 2.000 a fim de se obter uma recuperação de dados mais ampla.