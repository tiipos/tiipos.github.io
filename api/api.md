# [](#header-1) API : Estrutura inicial da API

## Sumário

1. [Objetivos](#goals)
2. [Tecnologias usadas](#frameworks)
3. [Estrutura inicial](#api)
4. [Prática desta aula](#lab)

## [](#goals) 1. Objetivos

1. Ter e entender um código inicial da API
   1. Rotas
   2. Criar estutura do repositório de dados
   3. Criar dados iniciais da API
   4. Acesso a repositório de dados
2. Instalar (_deploy_) da API
3. Estender a API programando um exemplo

## [](#frameworks) 2. Tecnologias usadas

- [node](https://nodejs.org/) : interpretador javascript
- [babel](https://babeljs.io) (_transpiliung_) : responsável por transformar o código para [ES2015](https://babeljs.io/docs/en/learn/)
- [hapi](https://hapijs.com/) : framework para desenvolver API com JS
- [heroku](https://heroku.com/) : serviço usado para instalar a API
- [knex](https://knexjs.org) : framework para montar as consultas ao dados no repositório
- [nodemon](https://nodemon.io) : monitor de modificações do código para relançar API
- [sqlite3](https://www.sqlite.org/) : repositório de dados

## [](#api) 3. Estrutura inicial

- [repositório do código-fonte](https://github.com/tiipos/tarefas-api/tree/rotas)
- [instalado no heroku](https://tarefas-api.herokuapp.com/)

**estrutura do projeto**

- `/` : arquivos de configuração do projeto
  - `/.babelrc` : configuração do [Babel](https://babeljs.io) para fazer a transformação (_transpiling_) de código para [ES2015](https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript/ECMAScript_2015_support_in_Mozilla)
  - `/.gitignore` : configuração do Git para ignorar lista de arquivos e diretórios
  - `/.jshintrn` : configuração do [jshint](https://jshint.com/) para analizar o código-fonte do projeto
  - `/bootstrap.js` : arquivo js principal do projeto, responsável por configurar a transformação (_transpiling_) do código-fonte do projeto em tempo de execução
  - `/knexfile.js` : configuração do framework de persistência [knex](https://knexjs.org) (acesso ao banco, modo produção e desenvolvimento, diretório dos arquivos de _migrations_ e _seeds_, etc)
  - `/package.json` : arquivo de configuração de projetos js
- `/migrations` : contém arquivos js para montar o repositório (no nosso caso as tabelas)
  - `/migrations/*_Database.js` : código js para montar a tabela _tasks_
- `/seeds` : contém arquivos js para injetar dados no repositório
  - `/seeds/01_Tasks.js` : código js para injetar dados na tabela _tasks_
- `/src` : contém os códigos-fonte da API propriamente dita
  - `/src/config` : arquivos de configurações do servidor (API)
    - `/src/config/knex.js` : arquivo js de configuração do [knex](https://knexjs.org) (acesso ao banco)
  - `/src/routes` : códigos-fonte contendo as rotas e o código para manipular os dados
    - `/src/routes/index.js` : código-fonte js responsável por exportar todas as rotas
    - `/src/routes/root.js` : código-fonte js com a rota _root_ `/`
    - `/src/routes/tasks.js` : código-fonte js com a rota `/tasks{*}`
  - `/src/server.js` : arquivo js que configura o servidor (API)

## [](#lab) 4. Prática desta aula

1. Fork o [respositório](https://github.com/tiipos/tarefas-api.git)
2. Clone o seu repositório no desktop `git clone URL`
   1. Execute `git clone https://github.com/{GIHUB_USERNAME}/tarefas-api.git`
   2. Modifique o branch `git checkout --track origin/rotas`
   3. Configure o git para fazer push com o branch `git branch --set-upstream-to origin/rotas`
3. Preparando para programar
   1. Instale o pnpm `npm i -g pnpm knex`
   2. Instale as bibliotecas `pnpm install`
4. Cria no repositório de dados a tabela `Users` com o `Knex`
   1. Edite o arquivo `migrations/*_Datastructure.js`
   2. Modifique o código de `createTable("tasks", ...);` para `createTable("tasks", ...).createTable("users", function(table) { // código com os campos da tabela });`
   3. Crie tabela com o comando `knex migrate:latest`
5. Crie um seed para usuários ```
   1. Crie o seed com `knex seed:make 02_Users`
   2. Edite o arquivo `seeds/02_Users.js`
   3. Insira códigos para fazer inserção de 3 usuários
   4. Faça inserção dos dados executando `knex seeds:run 02_Users`
6. Crie uma rota para os usuários `/users/`
   1. Crie o arquivo `src/routes/users.js`
   2. Modifique o código com uma constante a ser exportada, use como referência o código `src/routes/tasks.js`
   3. Insira em `src/routes/index.js` a nova rota
   4. Modifique o arquivo `src/server.js`, adicionando a nova rota
7. Execute localmente a API e teste
   1. Instale as bibliotecas `pnpm install`
   2. Execute a API `pnpm start`
   3. Teste usando um navegador, acessando a URL `http://localhost:8000/users/`
8. Publique seu código
   1. Adicione as modificações no git `git add . && git commit -m "add rota para usuários"`
   2. Publique no github `git push`
9. Instale no heroku.com
10. Acesse o site heroku.com e faça login
11. Crie uma nova aplicação no heroku
12. Em deploy, conecte com o repositório da API no Github

## [](#obs) Observações

- Sugestões e modificações desta página pode e devem ser feitas pelo [Github](https://github.com/tiipos/tiipos.github.io/) com _pull request_
