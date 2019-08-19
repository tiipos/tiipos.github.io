# [](#header) API : CRUD de gentilezas : Adicionando os verbos PUT e DELETE <!-- omit in toc -->

## Sumário <!-- omit in toc -->

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

## Parte 2 Adicionando o método POST e DELETE

### Adicionando o método POST

**testando o método POST no path /tasks** Erro 404

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks --verbose --request POST
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

user@pc:dir$
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

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks --verbose --request POST
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

user@pc:dir$
```

### Verficando se foi enviado uma Gentileza

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

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks --verbose --request POST
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

user@pc:dir$
```

**testando a validação com cabeçalho e sem objeto json**

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks -v --request POST --header "content-type: application/json"
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

user@pc:dir$
```

**testando a validação COM cabeçalho e COM objeto json**

```console
user@pc:dir$ curl http://localhost:8000/tasks --verbose --request POST -H 'Content-Type: application/json' --data '{ "title": "this is a title", "description": "this is a description" }'
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

user@pc:dir$
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

```console
user@pc:dir$ curl http://localhost:8000/tasks --verbose --request POST -H 'Content-Type: application/json' --data '{ "title": "this is a title", "description": "this is a description" }'
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

user@pc:dir$
```

**verificando as gentilezas**

```console
user@pc:dir$ curl http://localhost:8000/tasks -v
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

user@pc:dir$
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

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks/4 --verbose --request DELETE
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

user@pc:dir$
```

**adicionando o método DELETE no path /tasks/ID**

arquivo `./src/routes/tasks.js`

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
    handler: (request, reply) => ({
      status: "200",
      data: `apagada gentileza ${request.params.task_id}`
    })
  }
];

export default tasks_route;
```

**testando o método DELETE no path /tasks/ID** Ok 200

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks/4 --verbose --request DELETE
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

user@pc:dir$
```

### Apagando uma Gentileza

arquivo `src/routes/tasks.js`

```js
import Joi from "@hapi/joi";

import knex from "../config/knex";

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

const tasks_route = [
  {
    method: "GET",
    path: "/tasks",
    handler: (request, reply) =>
      knex
        .from(table_name)
        .select("oid", "title", "description")
        .then(results => reply.response(results))
        .catch(err => console.log(err))
  },
  {
    method: "POST",
    path: "/tasks",
    handler: (request, reply) =>
      knex(table_name)
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

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks/1 --verbose --request DELETE
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

user@pc:dir$
```

**testando apagar novamente**

```console
user@pc:dir$ curl http://127.0.0.1:8000/tasks/1 --verbose --request DELETE
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

user@pc:dir$
```

## Modularizando o código-fonte das rotas e auto carregando

### Adicionando biblioteca, criando diretório e movendo arquivos

```sh
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
