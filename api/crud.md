# [](#header-1) API : Estrutura inicial da API

## Sumário

1. [Objetivos](#goals)
2. [Estrutura inicial](#api)
3. [Código-fonte](#src)
4. [Prática desta aula](#lab)

## [](#goals) 1. Objetivos

1. Fazer uso correto dos verbos HTTP
   1. GET : recuperar dados
   2. POST : criar novos dados
   3. PUT : atualizar dados existentes
   4. DELETE : apagar dados existentes
2. Programar rotas _human friendly_ com diversos verbos
3. Recuperação de dados da requisição

## [](#api) 2. Projeto da API

- `/tasks` **GET** : recupera todas as tarefas que não foram apagadas
- `/tasks` **POST** : cria nova tarefa passando objeto json com _title_ e _description_
- `/tasks/{ID}` **GET** : recupera a atividade com indentificador igual a _ID_
- `/tasks/{ID}` **PUT** : modifica a tarefa com identificador _ID_ passando objeto json com _title_ e _description_
- `/tasks/{ID}/done` **PUT** : marca a tarefa com identificador _ID_ como realizada
- `/tasks/{ID}/undone` **PUT** : marca a tarefa com identificador _ID_ como NÃO realizada
- `/tasks/{ID}` **DELETE** : marca a tarefa com identificador _ID_ como apagada

## [](#src) 3. Código-fonte

- [repositório do código-fonte](https://github.com/tiipos/tarefas-api/tree/crud)
- [instalado no heroku](https://tarefas-api.herokuapp.com/)

lembrando a **estrutura do projeto**

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

**/src/routes/tasks.js**

```js
import knex from "../config/knex";

const requestHandler = (request, reply) => {
  return (
    knex
      .from("tasks")
      // .where({ deleted: false })
      .select("oid", "title", "description", "done")
      .then(results => reply.response(results))
      .catch(err => console.log(err))
  );
};

const tasks = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) => {
      return requestHandler(request, reply);
    }
  },
  {
    method: "GET",
    path: "/tasks/",
    handler: (request, reply) => {
      return requestHandler(request, reply);
    }
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) => {
      try {
        let { title, description } = JSON.parse(request.payload);
        if (title === undefined) {
          title = "";
          return reply.response({ error: "undefined title" }).code(400);
        }
        if (description === undefined) {
          description = "";
        }
        const task = {
          title: title,
          description: description,
          deleted: false,
          done: false
        };
        return knex
          .into("tasks")
          .insert(task)
          .returning("oid")
          .then(result => {
            task.oid = result[0];
            return reply.response({ status: "inserted", data: task }).code(201);
          })
          .catch(err => {
            return reply.response(err).code(400);
          });
      } catch (err) {
        return reply
          .response({ error: "undefined task in json object" })
          .code(400);
      }
    }
  },
  {
    method: "PUT",
    path: "/tasks/{task_id}",
    handler: (request, reply) => {
      try {
        const { title, description } = JSON.parse(request.payload);
        const id = request.params.task_id;
        let task = {};
        if (title != undefined) {
          task.title = title;
        }
        if (description != undefined) {
          task.description = description;
        }
        return knex("tasks")
          .where("oid", id)
          .update(task)
          .then(result =>
            knex("tasks")
              .where("oid", id)
              .select("oid", "title", "description", "deleted", "done")
              .then(result =>
                reply.response({ status: "updated", data: result[0] }).code(200)
              )
          )
          .catch(err => reply.response(err).code(401));
      } catch (err) {
        return reply.response(err).code(401);
      }
    }
  },
  {
    method: "PUT",
    path: "/tasks/{task_id}/done",
    handler: (request, reply) => {
      try {
        const id = request.params.task_id;
        let task = { done: true };
        return knex("tasks")
          .where("oid", id)
          .update(task)
          .then(result =>
            knex("tasks")
              .where("oid", id)
              .select("oid", "title", "description", "deleted", "done")
              .then(result =>
                reply.response({ status: "done", data: result[0] }).code(200)
              )
          )
          .catch(err => reply.response(err).code(401));
      } catch (err) {
        return reply.response(err).code(401);
      }
    }
  },
  {
    method: "PUT",
    path: "/tasks/{task_id}/undone",
    handler: (request, reply) => {
      try {
        const id = request.params.task_id;
        let task = { done: false };
        return knex("tasks")
          .where("oid", id)
          .update(task)
          .then(result =>
            knex("tasks")
              .where("oid", id)
              .select("oid", "title", "description", "deleted", "done")
              .then(result =>
                reply.response({ status: "undone", data: result[0] }).code(200)
              )
          )
          .catch(err => reply.response(err).code(401));
      } catch (err) {
        return reply.response(err).code(401);
      }
    }
  },
  {
    method: "DELETE",
    path: "/tasks/{task_id}",
    handler: (request, reply) => {
      const id = request.params.task_id;
      return knex("tasks")
        .where("oid", id)
        .update({ deleted: true })
        .then(result => {
          console.log(result);
          if (result === 0) {
            return reply
              .response({
                status: "not deleted",
                message: "task not found!"
              })
              .code(409);
          } else {
            return knex("tasks")
              .where("oid", id)
              .select("oid", "title", "description", "deleted", "done")
              .then(result =>
                reply.response({ status: "deleted", data: result[0] }).code(200)
              );
          }
        })
        .catch(err => reply.response(err).code(401));
    }
  }
];

export default tasks;
```

## [](#lab) 4. Prática desta aula

1. Clone o seu repositório no desktop `git clone https://github.com/{GIHUB_USERNAME}/tarefas-api.git`
2. Preparando para programar
   1. Instale o pnpm `npm i -g pnpm knex`
   2. Acesse o diretório da API `cd tarefas-api`
   3. Instale as bibliotecas `pnpm install`
   4. Execute a API `pnpm start`
3. Adicine a rota para os usuários `/users/`
   1. Recuperar dados do usuário por _ID_
   2. Recuperar dados do usuário por _email_. Observação, se não tiver colocado email na estrutura da tabela, ótima oportunidade para colocar. Lembre também de atualizar o _seed_ e executar as mudanças.
   3. Recuperar dados do usuário por _username_. Observação, se não tiver colocado apelido na estrutura da tabela, ótima oportunidade para colocar. Lembre também de atualizar o _seed_ e executar as mudanças.
   4. Atualizar dados do usuário pelo _username_.
   5. Apagar dados do usuário pelo _username_.
4. Teste a API localmente
   1. Usando o Postman acesse as URLs e veja as respostas.
   2. Lembre de testar passando dados errados, ou sem dados.
5. Publique seu código
   1. Adicione as modificações no git `git add . && git commit -m "add crud para usuários"`
   2. Publique no github `git push`
6. Instale no heroku.com
   1. Acesse o site heroku.com e faça login
   2. Em deploy, conecte com o repositório da API no Github
   3. Teste com o Postman usando as URLs do herokuapp.com

## [](#obs) Observações

- Sugestões e modificações desta página pode e devem ser feitas pelo [Github](https://github.com/tiipos/tiipos.github.io/) com _pull request_
