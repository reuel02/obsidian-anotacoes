
Passo a passo para a integração

1. **Acesse as configurações:** No Linear, clique no nome do seu workspace no canto superior esquerdo e selecione **Settings** (Configurações).
2. **Adicione a integração:** Na barra lateral, role até a seção _Workspace_ ou _Integrations_ e clique em **GitHub**.
3. **Autentique sua conta:** Clique para instalar e autenticar com sua conta do GitHub.
4. **Selecione os repositórios:** Escolha a organização do GitHub e selecione quais repositórios terão permissão para se conectar ao seu espaço de trabalho no Linear. [[1](https://www.youtube.com/watch?v=rc1xyt0xiKc&t=416), [2](https://translate.google.com/translate?u=https://linear.app/integrations/github&hl=pt&sl=en&tl=pt&client=sge)]

Como conectar tarefas aos códigos (vinculação)

Para que o Linear rastreie seu trabalho, você precisa referenciar o ID único da issue (ex: `ENG-123`) no seu versionamento: [[1](https://www.reddit.com/r/Linear/comments/1noqags/github_and_linear/?tl=pt-br), [2](https://translate.google.com/translate?u=https://linear.app/integrations/github&hl=pt&sl=en&tl=pt&client=sge)]

- **No nome da branch:** Crie a branch no formato `feature/ENG-123-nome-da-tarefa`.
- **No título do PR:** Coloque o ID da issue diretamente no título (ex: `ENG-123: Correção de bug no login`).
- **Em commits e mensagens de PR:** Utilize o ID do ticket na descrição do Pull Request ou mensagem de commit. Você também pode usar "palavras mágicas" para alterar o status da issue automaticamente (ex: `fixes ENG-123` ou `resolves ENG-123`)