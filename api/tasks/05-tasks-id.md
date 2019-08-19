# API : CRUD de gentilezas : Adicionando as rotas /tasks/{id} /tasks/{id}/done /tasks/{id}/undone <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Parte 3 Adicionando as rotas `/tasks/{task_id}`, `/tasks/{task_id}/done` e `/tasks/{task_id}/undone`](#parte-3-adicionando-as-rotas-taskstaskid-taskstaskiddone-e-taskstaskidundone)
  - [Refatorando o código](#refatorando-o-c%c3%b3digo)
  - [Adicionando a rota `tasks/{id}` verbo GET](#adicionando-a-rota-tasksid-verbo-get)
  - [Adicionando rota `tasks/{id}/done` e `tasks/{id}/undone`](#adicionando-rota-tasksiddone-e-tasksidundone)

## Parte 3 Adicionando as rotas `/tasks/{task_id}`, `/tasks/{task_id}/done` e `/tasks/{task_id}/undone`

### Refatorando o código

Crie o diretório com as entidades do sistema (`src/models`) e 2 arquivos (`src/models/index.js` e `src/models/tasks.js`)

```console
user@pc:2019-behappy-api$ mkdir src/models
user@pc:2019-behappy-api$ touch src/models/index.js
user@pc:2019-behappy-api$ touch src/models/tasks.js
user@pc:2019-behappy-api$
```

Edite o arquivo `src/models/tasks.js`

```js
import knex from "../config/knex";

const table_name = "tasks";

class Task {
  static getAll() {
    return knex
      .from(table_name)
      .select()
      .then(results => Task.deserialize(results))
      .catch(err => err);
  }

  static deserialize(json) {
    return json.map(data => {
      let task = new Task();
      task.oid = data.oid ? data.oid : 0;
      task.title = data.title ? data.title : "";
      task.description = data.description ? data.description : "";
      task.done = data.done ? true : false;
      task.delete = data.delete ? data.delete : false;
      return task;
    });
  }
}

export default Task;
```

Edite o arquivo `src/models/index.js`

```js
import Task from "./tasks";

export { Task };
```

Modifique o arquivo `src/routes/tasks/tasks_get.sj`

```js
import { Task } from "../../models";

export default {
  method: "GET",
  path: "/tasks",
  handler: (request, reply) => Task.getAll()
};
```

Teste se funcionou a refatoração

```console
user@pc:2019-behappy-api$ curl http://127.0.0.1:8000/tasks -v -X GET
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 256
< accept-ranges: bytes
< Date: Mon, 12 Aug 2019 12:07:00 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
[{"oid":22,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias"},{"oid":23,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias"},{"oid":24,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas"}]

user@pc:2019-behappy-api$
```

### Adicionando a rota `tasks/{id}` verbo GET

Adicione a rota

```console
touch src/routes/tasks/tasks_get_by_id.js
```

Modifique o arquivo `src/routes/models/tasks.js`

```js
import knex from "../config/knex";

const table_name = "tasks";

class Task {
  static getAll() {
    return knex
      .from(table_name)
      .select()
      .then(results => Task.deserialize(results))
      .catch(err => err);
  }

  static getById(id) {
    return knex(table_name)
      .where("oid", id)
      .select()
      .then(results => Task.deserialize(results))
      .catch(err => err);
  }

  static deserialize(json) {
    return json.map(data => {
      let task = new Task();
      task.oid = data.oid ? data.oid : 0;
      task.title = data.title ? data.title : "";
      task.description = data.description ? data.description : "";
      task.done = data.done ? true : false;
      task.delete = data.delete ? data.delete : false;
      return task;
    });
  }
}

export default Task;
```

Edite o arquivo `src/routes/tasks/tasks_get_by_id.js`

```js
import { Task } from "../../models";

const response_builder = data => {
  let response = {
    status: data.length >= 1 ? "200" : "401",
    data: {
      oid: data[0].oid ? data[0].oid : 0,
      title: data[0].title ? data[0].title : "",
      description: data[0].description ? data[0].description : ""
    },
    links: [
      {
        rel: `/linkrels/tasks/${data[0].oid}/${data[0].done ? "un" : ""}done`,
        uri: `/tasks/${data[0].oid}/${data[0].done ? "un" : ""}done`
      },
      {
        rel: `/linkrels/tasks/${data[0].oid}/${
          data[0].delete ? "un" : ""
        }delete`,
        uri: `/tasks/${data[0].oid}/${data[0].delete ? "un" : ""}delete`
      }
    ]
  };
  return response;
};
export default {
  method: "GET",
  path: "/tasks/{task_id}",
  handler: (request, reply) => {
    return Task.getById(request.params.task_id).then(tasks =>
      reply.response(response_builder(tasks))
    );
  }
};
```

Teste a rota

```console
user@pc:2019-behappy-api$ curl http://127.0.0.1:8000/tasks/22 -v -X GET
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> GET /tasks/22 HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 197
< accept-ranges: bytes
< Date: Mon, 12 Aug 2019 12:57:56 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
{"status":"200","data":{"oid":22,"title":"Bom dia","description":""},"links":[{"rel":"/linkrels/tasks/22/done","uri":"/tasks/22/done"},{"rel":"/linkrels/tasks/22/delete","uri":"/tasks/22/delete"}]}

user@pc:2019-behappy-api$
```

### Adicionando rota `tasks/{id}/done` e `tasks/{id}/undone`

Edite o arquivo `src/models/tasks.js`

```js
import knex from "../config/knex";

const table_name = "tasks";

class Task {
  static getAll() {
    return knex
      .from(table_name)
      .select()
      .then(results => Task.deserialize(results))
      .catch(err => err);
  }

  static getById(id) {
    return knex(table_name)
      .where("oid", id)
      .select()
      .then(results => Task.deserialize(results))
      .catch(err => err);
  }

  static undone(id) {
    return knex(table_name)
      .where("oid", id)
      .andWhere("done", true)
      .update("done", false)
      .then(tasks_updated => {
        if (tasks_updated > 0) return Task.getById(id);
        else return [];
      });
  }

  static done(id) {
    return knex(table_name)
      .where("oid", id)
      .andWhere("done", false)
      .update("done", true)
      .then(tasks_updated => {
        if (tasks_updated > 0) return Task.getById(id);
        else return [];
      });
  }

  static deserialize(json) {
    return json.map(data => {
      let task = new Task();
      task.oid = data.oid ? data.oid : 0;
      task.title = data.title ? data.title : "";
      task.description = data.description ? data.description : "";
      task.done = data.done ? true : false;
      task.delete = data.delete ? true : false;
      return task;
    });
  }
}

export default Task;
```

Edite o arquivo `src/routes/tasks/task_done.js`

```js
import { Task } from "../../models";

const response_builder = data => {
  let response = {};
  if (data.length > 0)
    response = {
      status: "200",
      data: {
        oid: data[0].oid ? data[0].oid : 0,
        title: data[0].title ? data[0].title : "",
        description: data[0].description ? data[0].description : ""
      },
      links: [
        {
          rel: `/linkrels/tasks/${data[0].oid}/undone`,
          uri: `/tasks/${data[0].oid}/undone`
        }
      ]
    };
  else
    response = {
      status: "406",
      data: "Gentileza não foi marcada como feita"
    };
  return response;
};

const response_code_builder = data => (data.length > 0 ? 200 : 406);

export default {
  method: "POST",
  path: "/tasks/{task_id}/done",
  handler: (request, reply) => {
    return Task.done(request.params.task_id).then(data => {
      return reply
        .response(response_builder(data))
        .code(response_code_builder(data));
    });
  }
};
```

Edite o arquivo `src/routes/tasks/task_undone.js`

```js
import { Task } from "../../models";

const response_builder = data => {
  let response = {};
  if (data.length > 0)
    response = {
      status: "200",
      data: {
        oid: data[0].oid ? data[0].oid : 0,
        title: data[0].title ? data[0].title : "",
        description: data[0].description ? data[0].description : ""
      },
      links: [
        {
          rel: `/linkrels/tasks/${data[0].oid}/done`,
          uri: `/tasks/${data[0].oid}/done`
        },
        {
          rel: `/linkrels/tasks/${data[0].oid}/delete`,
          uri: `/tasks/${data[0].oid}/delete`
        }
      ]
    };
  else
    response = {
      status: "406",
      data: "Gentileza não foi marcada como feita"
    };
  return response;
};

const response_code_builder = data => (data.length > 0 ? 200 : 406);

export default {
  method: "POST",
  path: "/tasks/{task_id}/undone",
  handler: (request, reply) => {
    return Task.undone(request.params.task_id).then(data =>
      reply.response(response_builder(data)).code(response_code_builder(data))
    );
  }
};
```

Teste as rotas

```consoles
### testando a rota done
user@pc:2019-behappy-api$
curl http://127.0.0.1:8000/tasks/23/done -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks/23/done HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 142
< Date: Mon, 12 Aug 2019 13:41:42 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
{"status":"200","data":{"oid":23,"title":"Ligação","description":""},"links":[{"rel":"/linkrels/tasks/23/undone","uri":"/tasks/23/undone"}]}

user@pc:2019-behappy-api$curl http://127.0.0.1:8000/tasks/23/done -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks/23/done HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 406 Not Acceptable
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 63
< Date: Mon, 12 Aug 2019 13:41:51 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
{"status":"406","data":"Gentileza não foi marcada como feita"}

### testando a rota undone
user@pc:2019-behappy-api$ curl http://127.0.0.1:8000/tasks/23/undone -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks/23/undone HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 199
< Date: Mon, 12 Aug 2019 13:42:21 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
{"status":"200","data":{"oid":23,"title":"Ligação","description":""},"links":[{"rel":"/linkrels/tasks/23/done","uri":"/tasks/23/done"},{"rel":"/linkrels/tasks/23/delete","uri":"/tasks/23/delete"}]}

user@pc:2019-behappy-api$ curl http://127.0.0.1:8000/tasks/23/undone -v -X POST
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to 127.0.0.1 (127.0.0.1) port 8000 (#0)
> POST /tasks/23/undone HTTP/1.1
> Host: 127.0.0.1:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 406 Not Acceptable
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 63
< Date: Mon, 12 Aug 2019 13:42:39 GMT
< Connection: keep-alive
<
* Connection #0 to host 127.0.0.1 left intact
{"status":"406","data":"Gentileza não foi marcada como feita"}

user@pc:2019-behappy-api$

```
