**1. Garantir que a base está limpa e atualizada:** Antes de começar qualquer tarefa, você volta para a base.

```
git checkout main
git pull origin main
```

**2. Criar a branch isolada da tarefa:** Use o ID do Linear para manter o padrão. O parâmetro `-b` cria a branch e já te move para ela.

```
git checkout -b eng-1-setup-prisma
```

**3. Executar o código e registrar as mudanças (Commit):** Após configurar o que precisava no projeto, você empacota as mudanças usando a convenção de _Conventional Commits_. O "Fixes ENG-1" avisa o Linear para fechar a tarefa automaticamente.

```
git add .
git commit -m "feat: adiciona configuracao inicial do prisma no projeto. Fixes ENG-1"
```

**4. Enviar a missão para a nuvem e abrir o Pull Request:**

```
git push origin eng-1-setup-prisma
```

Após o `push`, você vai até o GitHub. Ele mostrará um botão verde brilhante dizendo _"Compare & pull request"_. Você clica nele, revisa o próprio código (um excelente exercício de autocrítica) e clica em "Merge". Depois, apaga a branch antiga, pois a missão foi concluída.