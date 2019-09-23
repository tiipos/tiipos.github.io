# API : CRUD de gentilezas : Primeira rota /tasks GET <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Parte 1 Adicionando a entidade Task e a rota /tasks GET](#parte-1-adicionando-a-entidade-task-e-a-rota-tasks-get)
  - [criando tabela e inserindo dados](#criando-tabela-e-inserindo-dados)
  - [criando e testando a rota GET](#criando-e-testando-a-rota-get)
  - [retornando os dados corretos na rota](#retornando-os-dados-corretos-na-rota)
  - [Ajustando o projeto para publicar no Heroku](#ajustando-o-projeto-para-publicar-no-heroku)
  - [Publicando no repositório local e remoto](#publicando-no-reposit%c3%b3rio-local-e-remoto)

## Parte 1 Adicionando a entidade Task e a rota /tasks GET

**testando a conexão**

```console
user@pc:dir$ curl http://localhost:8000/tasks --verbose --request GET
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET / HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 54
< accept-ranges: bytes
< Date: Thu, 27 Jun 2019 17:58:20 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"version":"0.0.2","title":"API do App BeHappyWithMe"}

user@pc:dir$
```

### criando tabela e inserindo dados

```sh
./node_modules/.bin/knex migrate:make tasks
./node_modules/.bin/knex seed:make tasks
```

arquivo `./migrations/*_tasks.js`

```sh
exports.up = function(knex, Promise) {
  return knex.schema.createTable("tasks", (table) => {
        table.increments("oid").primary();
        table.string("title").notNullable();
        table.string("description").nullable();
        table.timestamps();
  });
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTable("tasks");
};
```

arquivo `./seeds/tasks.js`

```sh
exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex("tasks").del()
    .then(function () {
      // Inserts seed entries
      return knex("tasks").insert([
        {title: "Bom dia", description: "Você deu bom dia para alguém há 44 dias"},
        {title: "Ligação", description: "Você ligou para seus amigos há 44 dias"},
        {title: "Zap", description: "Envie um zap aos seus amigos em 6 horas"}
      ]);
    });
};
```

```sh
./node_modules/.bin/knex migrate:latest
./node_modules/.bin/knex seed:run
```

### criando e testando a rota GET

**criando e editando o arquivo**

```sh
touch src/routes/tasks.js
```

arquivo `./src/routes/tasks.js`

```js
import knex from "../config/knex";

const tasks_route = {
  method: "GET",
  path: "/tasks",
  handler: (request, reply) => ({
    status: "200",
    description: "Ok"
  })
};

export default tasks_route;
```

**adicionando a rota ao servidor**

arquivo `./src/routes/index.js`

```js
import root from "./root";
import tasks_route from "./tasks";

export { root, tasks_route };
```

arquivo `./src/server.js`

```js
import Hapi from "@hapi/hapi";

import { root, tasks_route } from "./routes";

const server = new Hapi.Server({
  port: process.env.PORT || 8000
});

const init = async () => {
  server.route([].concat(root).concat(tasks_route));

  await server.start();
  console.log("Server is running");
};

init();
```

**testando a nova rota**

```console
user@pc:dir$ curl http://localhost:8000/tasks --verbose --request GET
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 255
< accept-ranges: bytes
< Date: Thu, 27 Jun 2019 17:58:33 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"version": "0.0.1","title": "API do App BeHappyWithMe"}

user@pc:dir$
```

### retornando os dados corretos na rota

arquivo `./src/routes/tasks.js`

```js
import knex from "../config/knex";

const tasks_route = {
  method: "GET",
  path: "/tasks",
  handler: (request, reply) =>
    knex
      .from("tasks")
      .select("oid", "title", "description")
      .then(results => reply.response(results))
      .catch(err => console.log(err))
};

export default tasks_route;
```

**testando a rota com os dados corretos**

```console
user@pc:dir$ curl http://localhost:8000/tasks --verbose --request GET
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 255
< accept-ranges: bytes
< Date: Thu, 27 Jun 2019 17:58:33 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
[{"oid":1,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias"},{"oid":2,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias"},{"oid":3,"title":"Zap","description":"Envie um zap aos seus amigos em 6 horas"}]

user@pc:dir$
```

### Ajustando o projeto para publicar no Heroku

arquivo `package.json`

```json
{
  "name": "tarefas-api",
  "version": "0.0.1",
  "description": "API do App Lista de tarefas",
  "main": "bootstrap.js",
  "engines": {
    "node": "^12.0.0"
  },
  "scripts": {
    "dev-start": "nodemon bootstrap",
    "start": "node bootstrap",
    "postinstall": "knex migrate:latest && knex seed:run"
  },
  "keywords": ["api", "todo", "tarefas", "hapi"],
  "author": "L A MINORA",
  "license": "ISC",
  "dependencies": {
    "@hapi/hapi": "^18.3.1",
    "@hapi/joi": "^15.1.0",
    "babel-core": "^6.26.3",
    "babel-plugin-syntax-async-functions": "^6.13.0",
    "babel-polyfill": "^6.26.0",
    "babel-preset-env": "^1.7.0",
    "babel-register": "^6.26.0",
    "hapi-auto-route": "^2.0.7",
    "hapi-router": "^5.0.0",
    "knex": "^0.15.2",
    "nodemon": "^1.18.4",
    "sqlite3": "^4.0.2"
  }
}
```

### Publicando no repositório local e remoto

```sh
git add migrations seeds src/routes/tasks.js src/routes/index.js
git commit -m "Add na rota de Gentilezas o verbo GET"
git pull origin master
git push origin master
```
