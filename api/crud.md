# [](#header) API : CRUD de gentilezas <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [1. Objetivos](#1-Objetivos)
- [2. As rotas de gentilezas](#2-As-rotas-de-gentilezas)
- [Parte 1 Adicionando a entidade Task e a rota /tasks GET](#Parte-1-Adicionando-a-entidade-Task-e-a-rota-tasks-GET)
- [Parte 2](#Parte-2)

## 1. Objetivos

1. Fazer uso correto dos verbos HTTP
   1. GET : recuperar dados
   2. POST : criar novos dados
   3. PUT : atualizar dados existentes
   4. DELETE : apagar dados existentes
2. Programar rotas _human friendly_ com diversos verbos
3. Recuperação de dados da requisição

## 2. As rotas de gentilezas

- `/tasks` **GET** : recupera todas as tarefas que não foram apagadas
- `/tasks` **POST** : cria nova tarefa passando objeto json
- `/tasks/{ID}` **GET** : recupera a atividade com indentificador igual a _ID_
- `/tasks/{ID}` **PUT** : modifica a tarefa com identificador _ID_ passando objeto json
- `/tasks/{ID}/done` **PUT** : marca a tarefa com identificador _ID_ como realizada
- `/tasks/{ID}/undone` **PUT** : marca a tarefa com identificador _ID_ como NÃO realizada
- `/tasks/{ID}` **DELETE** : marca a tarefa com identificador _ID_ como apagada

## Parte 1 Adicionando a entidade Task e a rota /tasks GET

**Acessando o diretório do projeto e configurando** _código na máquina_

```sh
cd [nickname]/2019-behappy-api

git pull

git config --global --unset user.name
git config --global --unset user.email

git config user.name "Leonardo MINORA"
git config user.email "leonardo.minora@gmail.com"

```

ou **Baixando o projeto do Git e configurando** _código na máquina_

```sh
git clone https://github.com/tiipos/2019-behappy-api.git

cd [nickname]/2019-behappy-api

git config --global --unset user.name
git config --global --unset user.email

git config user.name "Leonardo MINORA"
git config user.email "leonardo.minora@gmail.com"

```

**baixando bibliotecas e instalando novas**

```sh
npm install --global knex

pnpm install

pnpm run dev-start
```

testando a conexão

```sh
 curl --verbose http://localhost:8000/
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
{"version":"0.0.1","title":"API do App BeHappyWithMe"}
```

**criando tabela e inserindo dados**

```sh
knex migrate:make tasks
knex seed:make tasks
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
knex migrate:latest
knex seed:run
```

**criando e testando a rota**

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

testando a conexão

```sh
$ curl --verbose http://localhost:8000/tasks
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
```

**retornando os dados corretos na rota**

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

testando a conexão

```sh
$ curl --verbose http://localhost:8000/tasks
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
```

## Parte 2
