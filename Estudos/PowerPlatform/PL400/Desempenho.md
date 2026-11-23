### Sobre Desempenho

Geralmente os maiores problemas de desempenho no Power Apps envolvem processamento ou consulta a fonte de dados.

No geral é importante analisar se o aplicativo faz consultas redundantes a fonte de dados, número excessivo de atualizações ou pesquisas. Tudo isso impacta diretamente no desempenho da aplicação.

Embora as fontes de dados possam ser os maiores gargalos, há outras alterações facilmente esquecidas que você pode fazer para ter um desempenho ideal. Alguns outros problemas comuns são:

- **Tamanho do ativo**: quando você está criando o aplicativo, é recomendável incluir os logotipos da empresa e outros ativos visuais. Ao adicionar esses ativos ao aplicativo, assegure-se de que eles estejam otimizados para o tamanho do aplicativo. Quanto maior a resolução de um arquivo, maior será o tamanho dele e mais recursos o aplicativo precisará para armazenar e exibir a imagem. Use uma ferramenta de edição de imagem com o objetivo de redimensionar os arquivos ao tamanho necessário para o aplicativo.

- **Republicar seu aplicativo** – A equipe do Power Apps está constantemente atualizando o Power Apps para lançar novos recursos e aumentar o desempenho. Seu aplicativo só poderá se beneficiar dessas melhorias se você abri-lo e publicá-lo novamente. O aplicativo permanecerá na versão em que foi publicado até que você faça isso. Portanto, revisitar periodicamente o aplicativo para migrar para a versão mais recente proporcionará a você o melhor desempenho possível.

- **Criar aplicativos com foco**: o Power Apps é compatível com a criação de aplicativos com um número ilimitado de telas. No entanto, ter muitas delas não é uma boa ideia. Crie aplicativos focados em um público-alvo e um processo específicos. Dessa maneira, você otimiza a experiência do usuário para um único público-alvo, facilita a criação e a solução de problemas no aplicativo e reduz o tamanho dele. Se você tiver um aplicativo para tudo, considere dividi-lo em aplicativos menores por função.
  
### Boas práticas para aprimorar o desempenho com fontes de dados

#### Usar coleções para armazenar dados em cache

É importante analisar se o aplicativo faz muitas buscas a mesma fonte de dados, para melhorar o desempenho é possível usar uma coleção para armazenar uma fonte de dados que é consultada várias vezes na aplicação, evitando a consulta frequente a mesma fonte de dados e melhorando o desempenho da aplicação.
##### Fique atento à delegação

Lembre-se de que a função Collect não é delegável. Isso significa que, por padrão, somente os primeiros 500 itens serão retornados da fonte de dados e armazenados na coleção. Planeje-se para essa limitação ao implementar o uso de coleções no aplicativo.

#### Usar a função Concurrent para carregar várias fontes de dados

Anteriormente, você aprendeu a usar coleções para armazenar dados em cache no aplicativo. Ao implementar essa funcionalidade no aplicativo, é comum carregar várias coleções quando o aplicativo é iniciado. A propriedade **OnStart** pode ser semelhante ao exemplo a seguir.

Fórmula do PowerApps

```
ClearCollect(colDepartmentList, Filter(DepartmentList, Status = "Active")); 
ClearCollect(colCompanyList, CompanyList); 
ClearCollect(colRegions, RegionList)
```

Nesse exemplo, você criará três coleções, mas cada uma será processada por vez. Portanto, se cada uma levar três segundos para ser processada, o usuário precisará esperar nove segundos para o aplicativo ser iniciado.

A função Concurrent permite processar todas essas chamadas ao mesmo tempo. É possível alterar o código como indicado a seguir.

Fórmula do PowerApps

```
Concurrent(
ClearCollect(colDepartmentList, Filter(DepartmentList, Status = "Active")),
ClearCollect(colCompanyList, CompanyList),
ClearCollect(colRegions, RegionList)
)
```

Agora, as três fórmulas são executadas ao mesmo tempo, reduzindo o tempo de carregamento para três segundos. A função Concurrent é uma ótima forma de evitar grandes atrasos de chamadas assíncronas.
#### OnStart versus OnVisible

OnStart e OnVisible fazem parte do kit de ferramentas para criar ótimos aplicativos. No entanto, quanto ao desempenho, eles podem ter um impacto significativo.

- **OnStart**: é uma propriedade no nível de aplicativo. As fórmulas dessa propriedade são executadas uma vez (quando o aplicativo é iniciado). Depois, nunca mais são executadas. Todas as fórmulas devem ser processadas antes que o aplicativo seja aberto. Isso geralmente é usado para inicializar dados de que você precisará em todo o aplicativo.

- **OnVisible**: essa é uma propriedade por tela. As fórmulas dessa propriedade são executadas sempre que um usuário navega para a tela. A tela é renderizada antes da conclusão do processamento da fórmula.

### Testar e solucionar problemas 

Para fazer isso, você pode usar a função **Monitor** integrada. Assim, você poderá ver as chamadas individuais à rede feitas pelo aplicativo e os detalhes, como a duração de cada chamada. Do ponto de vista do desempenho, isso pode ser importante.

Para acessar o **Monitor**, você pode selecionar o ícone **Ferramentas avançadas** na barra de ferramentas do lado esquerdo ou selecionar o ícone **Verificador de aplicativos** na barra de comandos. Com ambas as opções, você verá um link para **Abrir Monitor**.

Depois que você selecionar **Abrir Monitor**, uma sessão de monitor será aberta em uma guia separada do navegador com uma lista de eventos em branco e uma mensagem informando: "Você iniciou uma nova sessão do Monitor".

O Monitor agora rastreia e registra todas as ações do aplicativo, inclusive o desempenho da rede. Você pode usar isso para ver as chamadas para a fonte de dados, quanto tempo elas estão demorando e quais informações estão chegando/saindo.

Um exemplo de como você pode usar isso é determinar se um atraso de desempenho, conforme medido pelo controle de Timer do exemplo anterior, está ocorrendo no aplicativo, na rede ou na fonte de dados. Você coloca o aplicativo no modo de Visualização e começa a usá-lo como um usuário faria. No exemplo abaixo, usamos nosso exemplo com o botão e o controle de Timer para ver quanto tempo leva para obter os dados da fonte e criar a coleção. Observe que as informações retornadas fornecem carimbos de data/hora, categoria, operação, resultado, informações de resultado, status, duração (em milissegundos), fonte de dados e o controle selecionado (do aplicativo).