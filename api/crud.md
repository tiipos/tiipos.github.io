# [](#header) API : CRUD de gentilezas <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [1. Objetivos](#1-objetivos)
- [2. As rotas de gentilezas](#2-as-rotas-de-gentilezas)
- [Parte 1 Adicionando a entidade Task e a rota /tasks GET](#parte-1-adicionando-a-entidade-task-e-a-rota-tasks-get)
  - [Acessando o diretório do projeto e configurando](#acessando-o-diret%c3%b3rio-do-projeto-e-configurando)
  - [criando tabela e inserindo dados](#criando-tabela-e-inserindo-dados)
  - [criando e testando a rota](#criando-e-testando-a-rota)
  - [retornando os dados corretos na rota](#retornando-os-dados-corretos-na-rota)
  - [Publicando no repositório local e remoto](#publicando-no-reposit%c3%b3rio-local-e-remoto)
- [Parte 2 Adicionando o método POST e DELETE](#parte-2-adicionando-o-m%c3%a9todo-post-e-delete)
  - [Adicionando o método POST](#adicionando-o-m%c3%a9todo-post)
  - [Verficando se foi enviado uma Gentileza](#verficando-se-foi-enviado-uma-gentileza)
  - [Inserindo a Gentileza](#inserindo-a-gentileza)
  - [Publicando as modificações no repositório local e remoto](#publicando-as-modifica%c3%a7%c3%b5es-no-reposit%c3%b3rio-local-e-remoto)
  - [Adicionando o método DELETE](#adicionando-o-m%c3%a9todo-delete)
  - [Apagando uma Gentileza](#apagando-uma-gentileza)
- [Modularizando o código-fonte das rotas e auto carregando](#modularizando-o-c%c3%b3digo-fonte-das-rotas-e-auto-carregando)
  - [Adicionando biblioteca, criando diretório e movendo arquivos](#adicionando-biblioteca-criando-diret%c3%b3rio-e-movendo-arquivos)
  - [Editando arquivos](#editando-arquivos)
  - [Publicando modificações no repositório local e remoto](#publicando-modifica%c3%a7%c3%b5es-no-reposit%c3%b3rio-local-e-remoto)

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

### Acessando o diretório do projeto e configurando

 **código na máquina**

```sh
cd [nickname]/2019-behappy-api

git pull

git config --global --unset user.name
git config --global --unset user.email

git config user.name "Leonardo MINORA"
git config user.email "leonardo.minora@gmail.com"

```

**Baixando o projeto do Git**

```sh
git clone https://github.com/tiipos/2019-behappy-api.git

cd [nickname]/2019-behappy-api

git config --global --unset user.name
git config --global --unset user.email

git config user.name "Leonardo MINORA"
git config user.email "leonardo.minora@gmail.com"

```

**Baixando bibliotecas, instalando novas e executando a API**

```sh
npm install --global knex

pnpm install @hapi/joi

pnpm run dev-start
```

**testando a conexão**

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
{"version":"0.0.2","title":"API do App BeHappyWithMe"}
```

### criando tabela e inserindo dados

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

### criando e testando a rota

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
import tasks_route from "./tasks"

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

### Publicando no repositório local e remoto

```sh
git add migrations seeds src/routes/tasks.js src/routes/index.js
git commit -m "Add na rota de Gentilezas o verbo GET"
git pull origin master
git push origin master
```

## Parte 2 Adicionando o método POST e DELETE

### Adicionando o método POST

**testando o método POST no path /tasks** Erro 404

```sh
curl http://127.0.0.1:8000/tasks -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 404 Not Found
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 60
< Date: Sat, 27 Jul 2019 01:51:43 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"statusCode":404,"error":"Not Found","message":"Not Found"}
```

**adicionando o método POST no path /tasks**

arquivo `./src/routes/tasks.js`

```js
import knex from "../config/knex";

const tasks_route = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) =>
      knex
        .from("tasks")
        .select("oid", "title", "description")
        .then(results => reply.response(results))
        .catch(err => console.log(err))
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) => ({
      status: "201",
      data: "Ok"
    })
  }
];

export default tasks_route;
```

**testando o método POST no path /tasks** Ok 201

```sh
curl http://127.0.0.1:8000/tasks -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 201 Created
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 35
< Date: Sat, 27 Jul 2019 01:50:43 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"status":"201","data":"Ok"}
```

### Verficando se foi enviado uma Gentileza

arquivo `src/routes/tasks.js`

```js
import Joi from "@hapi/joi";

import knex from "../config/knex";

const delete_response = (result, task_id) => {
  let response = { data: ":D" };
  if (result == 1) {
    response = {
      status: "200",
      data: "Gentileza apagada",
      links: [
        {
          rel: `/linkrels/tasks/${task_id}/undelete`,
          uri: `/tasks/${task_id}/undelete`
        }
      ]
    };
  } else {
    response = {
      status: "400",
      data: "No data to delete"
    };
  }
  return response;
};

const delete_response_code = result => (result == 1 ? 200 : 400);

const tasks_route = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) =>
      knex
        .from("tasks")
        .select("oid", "title", "description")
        .then(results => reply.response(results))
        .catch(err => console.log(err))
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) => ({
      status: "201",
      description: "Ok"
    }),
    options: {
      validate: {
        payload: Joi.object({
          title: Joi.string().required(),
          description: Joi.string().required()
        }),
        headers: {
          "content-type": Joi.string().required()
        },
        options: {
          allowUnknown: true
        }
      }
    }
  }
];

export default tasks_route;
```

**testando a validação sem cabeçalho**

```sh
curl http://127.0.0.1:8000/tasks -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 400 Bad Request
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 82
< Date: Sat, 27 Jul 2019 02:11:34 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"statusCode":400,"error":"Bad Request","message":"Invalid request headers input"}

```

**testando a validação com cabeçalho e sem objeto json**

```sh
curl http://127.0.0.1:8000/tasks -v --request POST --header "content-type: application/json"
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 400 Bad Request
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 82
< Date: Sat, 27 Jul 2019 02:13:45 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"statusCode":400,"error":"Bad Request","message":"Invalid request headers input"}

```

**testando a validação COM cabeçalho e COM objeto json**

```sh
curl http://localhost:8000/tasks -v -X POST -H 'Content-Type: application/json' --data '{ "title": "this is a title", "description": "this is a description" }'
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 201 Created
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 82
< Date: Sat, 27 Jul 2019 02:13:45 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"status":"201","description":"Ok"}

```

### Inserindo a Gentileza

arquivo `src/routes/tasks.js`

```js
import Joi from "@hapi/joi";

import knex from "../config/knex";

const tasks_route = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) =>
      knex
        .from("tasks")
        .select("oid", "title", "description")
        .then(results => reply.response(results))
        .catch(err => reply.response(err))
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) =>
      knex("tasks")
        .insert(request.payload)
        .then(oid =>
          reply
            .response({
              status: 201,
              task: {
                oid: oid[0],
                title: request.payload.title,
                description: request.payload.description
              },
              links: [
                {
                  rel: `/linkrels/tasks/${oid[0]}/show`,
                  uri: `/tasks/${oid[0]}`
                },
                {
                  rel: `/linkrels/tasks/${oid[0]}/delete`,
                  uri: `/tasks/${oid[0]}`
                },
                {
                  rel: `/linkrels/tasks/${oid[0]}/done`,
                  uri: `/tasks/${oid[0]}/done`
                }
              ]
            })
            .code(201)
        )
        .catch(err => reply.response(err)),
    options: {
      validate: {
        payload: Joi.object({
          title: Joi.string().required(),
          description: Joi.string().required()
        }),
        headers: {
          "content-type": Joi.string().required()
        },
        options: {
          allowUnknown: true
        }
      }
    }
  }
];

export default tasks_route;
```

**adicionando gentileza**

```sh
curl http://localhost:8000/tasks -v -X POST -H 'Content-Type: application/json' --data '{ "title": "this is a title", "description": "this is a description" }'
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> Content-Length: 70
> 
* upload completely sent off: 70 out of 70 bytes
< HTTP/1.1 201 Created
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 269
< Date: Sat, 27 Jul 2019 05:00:03 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"status":201,"task":{"oid":5,"title":"this is a title","description":"this is a description"},"links":[{"rel":"/linkrels/tasks/10/show","uri":"/tasks/10"},{"rel":"/linkrels/tasks/10/delete","uri":"/tasks/10"},{"rel":"/linkrels/tasks/10/done","uri":"/tasks/10/done"}]}
```

**verificando as gentilezas**

```sh
curl http://localhost:8000/tasks -v
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
< content-length: 849
< accept-ranges: bytes
< Date: Sat, 27 Jul 2019 05:53:09 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
[{"oid":1,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias"},{"oid":2,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias"},{"oid":3,"title":"Zap","description":"Envie um zap aos seus amigos em 6 horas"},{"oid":4,"title":"this is a title","description":"this is a description"}]

```

### Publicando as modificações no repositório local e remoto

```sh
git add src/routes/tasks.js
git commit -m "Add na rota de Gentilezas o verbo POST"
git pull origin master
git push origin master
```

### Adicionando o método DELETE

**testando o método DELETE no path /tasks/ID** Erro 404

```sh
curl http://127.0.0.1:8000/tasks/4 -v -X DELETE
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 404 Not Found
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 60
< Date: Sat, 27 Jul 2019 01:51:43 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"statusCode":404,"error":"Not Found","message":"Not Found"}
```

**adicionando o método DELETE no path /tasks/ID**

arquivo `./src/routes/tasks.js`

```js
import knex from "../config/knex";

const tasks_route = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) =>
      knex
        .from("tasks")
        .select("oid", "title", "description")
        .then(results => reply.response(results))
        .catch(err => console.log(err))
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) =>
      knex("tasks")
        .insert(request.payload)
        .then(oid =>
          reply
            .response({
              status: 201,
              task: {
                oid: oid[0],
                title: request.payload.title,
                description: request.payload.description
              },
              links: [
                {
                  rel: `/linkrels/tasks/${oid[0]}/show`,
                  uri: `/tasks/${oid[0]}`
                },
                {
                  rel: `/linkrels/tasks/${oid[0]}/delete`,
                  uri: `/tasks/${oid[0]}`
                },
                {
                  rel: `/linkrels/tasks/${oid[0]}/done`,
                  uri: `/tasks/${oid[0]}/done`
                }
              ]
            })
            .code(201)
        )
        .catch(err => reply.response(err)),
    options: {
      validate: {
        payload: Joi.object({
          title: Joi.string().required(),
          description: Joi.string().required()
        }),
        headers: {
          "content-type": Joi.string().required()
        },
        options: {
          allowUnknown: true
        }
      }
    }
  },
  {
    method: "DELETE",
    path: "/tasks/{id}",
    handler: (request, reply) => ({
      status: "200",
      data: `apagada gentileza ${request.params.task_id}`
    })
  }
];

export default tasks_route;
```

**testando o método DELETE no path /tasks/ID** Ok 200

```sh
curl http://127.0.0.1:8000/tasks/4 -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Type: application/json
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 35
< Date: Sat, 27 Jul 2019 01:50:43 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"status":"200","data":"apagada gentileza 4"}
```

### Apagando uma Gentileza

arquivo `src/routes/tasks.js`

```js
import knex from "../config/knex";

const tasks_route = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) =>
      knex
        .from("tasks")
        .select("oid", "title", "description")
        .then(results => reply.response(results))
        .catch(err => console.log(err))
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) =>
      knex("tasks")
        .insert(request.payload)
        .then(oid =>
          reply
            .response({
              status: 201,
              task: {
                oid: oid[0],
                title: request.payload.title,
                description: request.payload.description
              },
              links: [
                {
                  rel: `/linkrels/tasks/${oid[0]}/show`,
                  uri: `/tasks/${oid[0]}`
                },
                {
                  rel: `/linkrels/tasks/${oid[0]}/delete`,
                  uri: `/tasks/${oid[0]}`
                },
                {
                  rel: `/linkrels/tasks/${oid[0]}/done`,
                  uri: `/tasks/${oid[0]}/done`
                }
              ]
            })
            .code(201)
        )
        .catch(err => reply.response(err)),
    options: {
      validate: {
        payload: Joi.object({
          title: Joi.string().required(),
          description: Joi.string().required()
        }),
        headers: {
          "content-type": Joi.string().required()
        },
        options: {
          allowUnknown: true
        }
      }
    }
  },
  {
    method: "DELETE",
    path: "/tasks/{task_id}",
    handler: (request, reply) =>
      knex(table_name)
        .where("oid", request.params.task_id)
        .del()
        .then(results =>
          reply
            .response(delete_response(results, request.params.task_id))
            .code(delete_response_code(results))
        )
        .catch(error => {
          reply.response(delete_response(0, 0)).code(400);
        })
  }
];

export default tasks_route;
```

**testando apagar uma gentileza**

```sh
curl http://127.0.0.1:8000/tasks/1 -v -X DELETE
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> DELETE /tasks/1 HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 116
< Date: Mon, 29 Jul 2019 13:24:59 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"status":"200","data":"Gentileza apagada","links":[{"rel":"/linkrels/tasks/1/undelete","uri":"/tasks/1/undelete"}]}
```

**testando apagar novamente**

```sh
curl http://127.0.0.1:8000/tasks/1 -v -X DELETE
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> DELETE /tasks/1 HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 400 Bad Request
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 43
< Date: Mon, 29 Jul 2019 13:31:11 GMT
< Connection: keep-alive
< 
* Connection #0 to host 127.0.0.1 left intact
{"status":"400","data":"No data to delete"}
```

## Modularizando o código-fonte das rotas e auto carregando

### Adicionando biblioteca, criando diretório e movendo arquivos

```sh
### Instala a biblioteca de autocarga
pnpm install hapi-router

### Cria diretório para rotas de Gentilezas
mkdir src/routes/tasks
### Move rotas de Gentilezas para o diretório src/routes/tasks (get)
git mv src/routes/tasks.js src/routes/tasks/tasks_get.js
### Duplica arquivo de rotas de Gentilezas (post)
cp src/routes/tasks/tasks_get.js src/routes/tasks/tasks_post.js
### Duplica arquivo de rotas de Gentilezas (delete)
cp src/routes/tasks/tasks_get.js src/routes/tasks/tasks_delete.js
### Apaga arquivo com link para todas as rotas
git rm src/routes/index.js
```

### Editando arquivos

arquivo `src/server.js`

```js
import Hapi from "@hapi/hapi";

const server = new Hapi.Server({
  port: process.env.PORT || 8000,
  debug: { request: ["*"] }
});

const init = async () => {
  await server.register({
    plugin: require("hapi-router"),
    options: {
      routes: "src/routes/**/*.js"
    }
  });

  await server.start();
  console.log("Server is running");
  console.log(server.info);
};

init();
```

arquivo `src/routes/tasks/tasks_get.js`

```js
import knex from "../../config/knex";

export default {
  method: "GET",
  path: "/tasks",
  handler: (request, reply) =>
    knex
      .from("tasks")
      .select("oid", "title", "description")
      .then(results => reply.response(results))
      .catch(err => reply.response(err))
};
```

arquivo `src/routes/tasks/tasks_post.js`

```js
import Joi from "@hapi/joi";

import knex from "../../config/knex";

export default {
  method: "POST",
  path: "/tasks",
  handler: (request, reply) =>
    knex("tasks")
      .insert(request.payload)
      .then(oid =>
        reply
          .response({
            status: 201,
            task: {
              oid: oid[0],
              title: request.payload.title,
              description: request.payload.description
            },
            links: [
              {
                rel: `/linkrels/tasks/${oid[0]}/show`,
                uri: `/tasks/${oid[0]}`
              },
              {
                rel: `/linkrels/tasks/${oid[0]}/delete`,
                uri: `/tasks/${oid[0]}`
              },
              {
                rel: `/linkrels/tasks/${oid[0]}/done`,
                uri: `/tasks/${oid[0]}/done`
              }
            ]
          })
          .code(201)
      )
      .catch(err => reply.response(err)),
  options: {
    validate: {
      payload: Joi.object({
        title: Joi.string().required(),
        description: Joi.string().required()
      }),
      headers: {
        "content-type": Joi.string().required()
      },
      options: {
        allowUnknown: true
      }
    }
  }
};
```

arquivo `src/routes/tasks/tasks_delete.js`

```js
import knex from "../../config/knex";

const table_name = "tasks";

const delete_response = (result, task_id) => {
  let response = { data: ":D" };
  if (result == 1) {
    response = {
      status: "200",
      data: "Gentileza apagada",
      links: [
        {
          rel: `/linkrels/tasks/${task_id}/undelete`,
          uri: `/tasks/${task_id}/undelete`
        }
      ]
    };
  } else {
    response = {
      status: "400",
      data: "No data to delete"
    };
  }
  return response;
};

const delete_response_code = result => (result == 1 ? 200 : 400);

export default {
  method: "DELETE",
  path: "/tasks/{task_id}",
  handler: (request, reply) =>
    knex(table_name)
      .where("oid", request.params.task_id)
      .del()
      .then(results =>
        reply
          .response(delete_response(results, request.params.task_id))
          .code(delete_response_code(results))
      )
      .catch(error => {
        reply.response(delete_response(0, 0)).code(400);
      })
};
```

### Publicando modificações no repositório local e remoto

```sh
git add package.json src/server.js src/routes/tasks
git commit -m "Modularizando rotas e fazendo autocarga"
git pull origin master
git push origin master
```
