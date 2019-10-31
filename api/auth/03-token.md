# API : Autenticação : Token <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Prepare o ambiente](#prepare-o-ambiente)
  - [Abra o terminal](#abra-o-terminal)
  - [Modifique a infra-estrutura inicial](#modifique-a-infra-estrutura-inicial)
  - [Modifique a rota de login](#modifique-a-rota-de-login)
  - [Modifique o servidor adicionando autenticação por token](#modifique-o-servidor-adicionando-autentica%c3%a7%c3%a3o-por-token)
  - [Inicie o servidor](#inicie-o-servidor)
- [Adicione rota segura](#adicione-rota-segura)
  - [Adicione o modelo Token ao projeto](#adicione-o-modelo-token-ao-projeto)
  - [Adicione a autenticação por token](#adicione-a-autentica%c3%a7%c3%a3o-por-token)
  - [Configure que a rota será autenticada por token](#configure-que-a-rota-ser%c3%a1-autenticada-por-token)
  - [Teste o ambiente](#teste-o-ambiente)
- [Vincule gentilezas com os usuários](#vincule-gentilezas-com-os-usu%c3%a1rios)
  - [Teste inicial com usuários diferentes](#teste-inicial-com-usu%c3%a1rios-diferentes)
  - [Adicione uma migração do banco](#adicione-uma-migra%c3%a7%c3%a3o-do-banco)
  - [Execute a migração da estrutura](#execute-a-migra%c3%a7%c3%a3o-da-estrutura)
  - [Atualize os dados: vincule usuários a gentilezas](#atualize-os-dados-vincule-usu%c3%a1rios-a-gentilezas)
  - [Atualize o código-fonte de rotas](#atualize-o-c%c3%b3digo-fonte-de-rotas)
  - [Teste novamente a API](#teste-novamente-a-api)
- [Atualize os métodos do modelo e as rotas adicionando usuário](#atualize-os-m%c3%a9todos-do-modelo-e-as-rotas-adicionando-usu%c3%a1rio)
  - [Modifique o arquivo ./src/models/tasks.js](#modifique-o-arquivo-srcmodelstasksjs)
  - [Modifique o arquivo ./src/routes/tasks/\*](#modifique-o-arquivo-srcroutestasks)

## Prepare o ambiente

### Abra o terminal

```sh
pnpm uninstall babel-core/register babel-polyfill

pnpm add @babel/core @babel/plugin-proposal-class-properties @babel/plugin-transform-async-to-generator @babel/polyfill @babel/preset-env @babel/register
pnpm add hapi-auth-jwt2 jsonwebtoken

rm ./mydb.sqlite
pnpm run db-migrate
pnpm run db-populate

```

### Modifique a infra-estrutura inicial

Modifique o arquivo `./.babelrc`

```
{
  "presets": ["@babel/env"],
  "plugins": ["@babel/plugin-transform-async-to-generator", "@babel/plugin-proposal-class-properties"]
}
```

Modifique o arquivo `./bootstrap.js`

```js
require("@babel/register");
require("@babel/polyfill");
require("./src/server");
```

### Modifique a rota de login

Modifique o arquivo `.src/routes/users/auth.js`

```js
import { User } from "../../models";

export default {
  method: ["GET", "POST"],
  path: "/auth",
  options: {
    auth: "simple"
  },
  handler: (request, reply) => request.auth.credentials
};
```

### Modifique o servidor adicionando autenticação por token

Modifique o arquivo `./src/server.js`

```js
import Hapi from "@hapi/hapi";
import { User, Token } from "./models";

const server = new Hapi.Server({
  port: process.env.PORT || 8000,
  debug: { request: ["*"] }
});

const basic_validate = async (request, username, password) => {
  const user = await User.login(username, password);

  if (user == undefined) {
    return { credentials: null, isValid: false };
  }
  const token = Token.add(user);
  return {
    credentials: { id: user.oid, username: user.login, token: token },
    isValid: true
  };
};

const token_validate = async (user, request) => {
  token = tokens.findByUser(user);
  if (token == undefined) {
    return { credentials: null, isValid: false };
  } else {
    const credentials = { id: user.id, username: user.login, token: token };
    return { credentials, isValid: true };
  }
};

const init = async () => {
  await server.register([require("@hapi/basic"), require("hapi-auth-jwt2")]);
  server.auth.strategy("simple", "basic", { validate: basic_validate });
  server.auth.strategy("token", "jwt", {
    key: Token.secret,
    validate: token_validate,
    verifyOptions: { algorithms: ["HS256"] }
  });

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

### Inicie o servidor

```console
user:dir $ pnpm run dev-start

> tarefas-api@0.0.1 dev-start /..../2019-behappy-api
> nodemon bootstrap

[nodemon] 1.19.2
[nodemon] to restart at any time, enter `rs`
[nodemon] watching dir(s): *.*
[nodemon] starting `node bootstrap.js`
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Server is running
{
  created: 1569254785148,
  started: 1569254785692,
  host: 'Computador.local',
  port: 8000,
  protocol: 'http',
  id: 'Computador.local:4033:k0wls5mk',
  uri: 'http://Computador.local:8000',
  address: '0.0.0.0'
}


```

**Observação**: `user:dir`, `....`, e `Computador` mudam de valor nos computadores.

## Adicione rota segura

### Adicione o modelo Token ao projeto

Crie o arquivo `./src/models/tokens.js`

```js
import JWT from "jsonwebtoken";

class Token {
  static secret = "NeverShareYourSecret";
  static data = new Map();

  static add(user, expiration = "24h") {
    const token = JWT.sign(user, Token.secret, { expiresIn: expiration });
    Token.data.set(user.email, token);
    return token;
  }

  static rm(user) {
    const token = Token.data.delete(user.email);
    return token;
  }

  static remove(user) {
    Token.rm(user);
  }

  static findByUser(user) {
    return Token.data.get(user.email);
  }
}

export default Token;
```

Modifique o arquivo `./src/models/index.js`

```js
import Task from "./tasks";
import Token from "./tokens";
import User from "./users";
import What from "./what";
import Who from "./who";

export { Task, Token, User, What, Who };
```

### Adicione a autenticação por token

Modifique o arquivo `./src/server.js`

```js
import Hapi from "@hapi/hapi";
import { User, Token } from "./models";

const server = new Hapi.Server({
  port: process.env.PORT || 8000,
  debug: { request: ["*"] }
});

const basic_validate = async (request, username, password) => {
  const user = await User.login(username, password);

  if (user == undefined) {
    return { credentials: null, isValid: false };
  }
  const token = Token.add(user);
  return {
    credentials: { id: user.oid, username: user.login, token: token },
    isValid: true
  };
};

const token_validate = async (user, request) => {
  token = tokens.findByUser(user);
  if (token == undefined) {
    return { credentials: null, isValid: false };
  } else {
    const credentials = { id: user.id, name: user.name, token: token };
    return { credentials, isValid: true };
  }
};

const init = async () => {
  await server.register([require("@hapi/basic"), require("hapi-auth-jwt2")]);
  server.auth.strategy("simple", "basic", { validate: basic_validate });
  server.auth.strategy("token", "jwt", {
    key: Token.secret,
    validate: token_validate,
    verifyOptions: { algorithms: ["HS256"] }
  });

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

### Configure que a rota será autenticada por token

Modifique o arquivo `./src/routes/tasks/task_delete.js`

```js
import { Task } from "../../models";

const response_message_builder = (result, task_id) => {
  console.log(result);
  if (result.length == undefined || result.length == 0) {
    return {
      status: "400",
      data: "No data to delete"
    };
  }
  return {
    status: "200",
    data: {
      oid: result[0].oid,
      title: result[0].title,
      description: result[0].description
    },
    links: [
      {
        rel: `/linkrels/tasks/${task_id}/undelete`,
        uri: `/tasks/${task_id}/undelete`
      }
    ]
  };
};

const delete_response_code = result => (result == 1 ? 200 : 400);

export default {
  method: "DELETE",
  path: "/tasks/{task_id}",
  options: {
    auth: "token"
  },
  handler: (request, reply) =>
    Task.delete(request.params.task_id).then(tasks =>
      reply
        .response(response_message_builder(tasks, request.params.task_id))
        .code(delete_response_code(tasks))
    )
};
```

Modifique o arquivo `./src/routes/tasks/task_done.js`

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
  options: {
    auth: "token"
  },
  handler: (request, reply) => {
    return Task.done(request.params.task_id).then(data => {
      return reply
        .response(response_builder(data))
        .code(response_code_builder(data));
    });
  }
};
```

Modifique o arquivo `./src/routes/tasks/task_get_by_id.js`

```js
import { Task } from "../../models";

const response_builder = data => {
  if (data.length == undefined || data.length == 0)
    return { status: "400", data: "Gentileza não encontrada" };
  else
    return {
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
};

const responde_code_builder = data => {
  if (data.length == undefined || data.length == 0) return 400;
  else return 200;
};

export default {
  method: "GET",
  path: "/tasks/{task_id}",
  options: {
    auth: "token"
  },
  handler: (request, reply) => {
    return Task.getById(request.params.task_id).then(tasks =>
      reply.response(response_builder(tasks)).code(responde_code_builder(tasks))
    );
  }
};
```

Modifique o arquivo `./src/routes/tasks/task_post.js`

```js
import Joi from "@hapi/joi";

import Task from "../../models/tasks";

const response_message_builder = (data, oid) => {
  return {
    status: 201,
    task: {
      oid: oid,
      title: data.title,
      description: data.description
    },
    links: [
      {
        rel: `/linkrels/tasks/${oid}/show`,
        uri: `/tasks/${oid}`
      },
      {
        rel: `/linkrels/tasks/${oid}/delete`,
        uri: `/tasks/${oid}`
      },
      {
        rel: `/linkrels/tasks/${oid}/done`,
        uri: `/tasks/${oid}/done`
      }
    ]
  };
};

export default {
  method: "POST",
  path: "/tasks",
  handler: (request, reply) => {
    return Task.create(request.payload).then(oid =>
      reply.response(response_message_builder(request.payload, oid)).code(201)
    );
  },
  options: {
    auth: "token",
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

Modifique o arquivo `./src/routes/tasks/task_undelete.js`

```js
import { Task } from "../../models";

const response_message_builder = (result, task_id) => {
  console.log(result);
  if (result.length == undefined || result.length == 0) {
    return {
      status: "400",
      data: "No data to delete"
    };
  }
  return {
    status: "200",
    data: {
      oid: result[0].oid,
      title: result[0].title,
      description: result[0].description
    },
    links: [
      {
        rel: `/linkrels/tasks/${task_id}/delete`,
        uri: `/tasks/${task_id}/delete`
      }
    ]
  };
};

const delete_response_code = result => (result == 1 ? 200 : 400);

export default {
  method: "POST",
  path: "/tasks/{task_id}/undelete",
  options: {
    auth: "token"
  },
  handler: (request, reply) =>
    Task.undelete(request.params.task_id).then(tasks =>
      reply
        .response(response_message_builder(tasks, request.params.task_id))
        .code(delete_response_code(tasks))
    )
};
```

Modifique o arquivo `./src/routes/tasks/task_undone.js`

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
  options: {
    auth: "token"
  },
  handler: (request, reply) => {
    return Task.undone(request.params.task_id).then(data =>
      reply.response(response_builder(data)).code(response_code_builder(data))
    );
  }
};
```

Modifique o arquivo `./src/routes/tasks/tasks_get.js`

```js
import { Task } from "../../models";

export default {
  method: "GET",
  path: "/tasks",
  options: {
    auth: "token"
  },
  handler: (request, reply) => Task.getAll()
};
```

### Teste o ambiente

**teste recuperar todas as gentilezas**

```console
user:dir $ curl http://localhost:8000/tasks -v
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 401 Unauthorized
< WWW-Authenticate: Token
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 76
< Date: Mon, 23 Sep 2019 17:45:21 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"statusCode":401,"error":"Unauthorized","message":"Missing authentication"}

user:dir $
```

**autentique para ganhar um token**

```console
user:dir $ curl http://localhost:8000/auth -v --user testador:secret
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'testador'
> GET /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic dGVzdGFkb3I6c2VjcmV0
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 466
< accept-ranges: bytes
< Date: Mon, 23 Sep 2019 17:55:11 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"id":4,"username":"testador","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjQsImxvZ2luIjoidGVzdGFkb3IiLCJlbWFpbCI6InRlc3RhZG9yQGdtYWlsLmNvbSIsIm5hbWUiOiJUZXN0YWRvciBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkQnkvZTRMRnBiR1Jva2JOdGJlTWx1LlgvRS5tQUU3b0RPamIzUk1Xai8yRHZHbDFnc2pia3EiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0xNiAxMjo0MzoxNyIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTE2IDEyOjQzOjE3IiwiaWF0IjoxNTY5MjYxMzExLCJleHAiOjE1NjkzNDc3MTF9.h_1AckpJLx_KtnMfP_ZcLaljBDoHwQTkhOTtRXesWSA"}

user:dir $
```

**teste novamente a rota segura por token**

```console
user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjQsImxvZ2luIjoidGVzdGFkb3IiLCJlbWFpbCI6InRlc3RhZG9yQGdtYWlsLmNvbSIsIm5hbWUiOiJUZXN0YWRvciBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkQnkvZTRMRnBiR1Jva2JOdGJlTWx1LlgvRS5tQUU3b0RPamIzUk1Xai8yRHZHbDFnc2pia3EiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0xNiAxMjo0MzoxNyIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTE2IDEyOjQzOjE3IiwiaWF0IjoxNTY5MjYxMzExLCJleHAiOjE1NjkzNDc3MTF9.h_1AckpJLx_KtnMfP_ZcLaljBDoHwQTkhOTtRXesWSA"
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjQsImxvZ2luIjoidGVzdGFkb3IiLCJlbWFpbCI6InRlc3RhZG9yQGdtYWlsLmNvbSIsIm5hbWUiOiJUZXN0YWRvciBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkQnkvZTRMRnBiR1Jva2JOdGJlTWx1LlgvRS5tQUU3b0RPamIzUk1Xai8yRHZHbDFnc2pia3EiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0xNiAxMjo0MzoxNyIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTE2IDEyOjQzOjE3IiwiaWF0IjoxNTY5MjYxMzExLCJleHAiOjE1NjkzNDc3MTF9.h_1AckpJLx_KtnMfP_ZcLaljBDoHwQTkhOTtRXesWSA
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 340
< accept-ranges: bytes
< Date: Mon, 23 Sep 2019 17:59:38 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
[{"oid":15,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias","done":false,"delete":false},{"oid":16,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias","done":false,"delete":false},{"oid":17,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false}]

user:dir $
```

## Vincule gentilezas com os usuários

**Observação**: `user:dir`, `Users/leo/src/disciplinas/TII-POS/2019-src`. e `20190923183001` são dados que devem mudar no computador do aluno.

### Teste inicial com usuários diferentes

usuário `testador`

```console
user:dir $ curl http://localhost:8000/auth -v --user testador:secret
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'testador'
> GET /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic dGVzdGFkb3I6c2VjcmV0
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 466
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 00:21:07 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"id":7,"username":"testador","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjcsImxvZ2luIjoidGVzdGFkb3IiLCJlbWFpbCI6InRlc3RhZG9yQGdtYWlsLmNvbSIsIm5hbWUiOiJUZXN0YWRvciBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZTdWWTBmaDVoWGE5YzJCUGFXQldRTi9kTjhnYzExTFciLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg0NDY3LCJleHAiOjE1NjkzNzA4Njd9.LQWollFT6g_euO4ba_HTlWjRP-6Rus_Ae9ohxjb_d8s"}

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjcsImxvZ2luIjoidGVzdGFkb3IiLCJlbWFpbCI6InRlc3RhZG9yQGdtYWlsLmNvbSIsIm5hbWUiOiJUZXN0YWRvciBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZTdWWTBmaDVoWGE5YzJCUGFXQldRTi9kTjhnYzExTFciLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg3Nzg5LCJleHAiOjE1NjkzNzQxODl9.Qr6Nb2EfOhCuVZOnUyfCR4ZUXrdZm5W0SDDPSPDgWCw"
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjcsImxvZ2luIjoidGVzdGFkb3IiLCJlbWFpbCI6InRlc3RhZG9yQGdtYWlsLmNvbSIsIm5hbWUiOiJUZXN0YWRvciBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZTdWWTBmaDVoWGE5YzJCUGFXQldRTi9kTjhnYzExTFciLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg3Nzg5LCJleHAiOjE1NjkzNzQxODl9.Qr6Nb2EfOhCuVZOnUyfCR4ZUXrdZm5W0SDDPSPDgWCw
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 446
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:18:31 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
[{"oid":9,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias","done":false,"delete":false},{"oid":10,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias","done":false,"delete":false},{"oid":11,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false},{"oid":12,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false}]

user:dir $
```

usuário `aluno`

```console
user:dir $ curl http://localhost:8000/auth -v --user aluno:53cr3t
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'aluno'
> GET /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic YWx1bm86NTNjcjN0
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 451
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:25:42 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"id":9,"username":"aluno","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A"}

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A"
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 446
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:26:57 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
[{"oid":9,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias","done":false,"delete":false},{"oid":10,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias","done":false,"delete":false},{"oid":11,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false},{"oid":12,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false}]

user:dir $
```

**todos os dados iguais**.

duvida, tente os comandos a seguir...

```console

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A" > testador.response
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 446
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:28:25 GMT
< Connection: keep-alive
<
{ [446 bytes data]
100   446  100   446    0     0  49782      0 --:--:-- --:--:-- --:--:-- 55750
* Connection #0 to host localhost left intact

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A" > aluno.response
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjksImxvZ2luIjoiYWx1bm8iLCJlbWFpbCI6ImFsdW5vQGdtYWlsLmNvbSIsIm5hbWUiOiJBbHVubyBmYWtlIiwicGFzc3dvcmQiOiIkMmIkMTAkeDVka3hxRmgxQThaVEhGYi43RnczZWM5ckRBOHREVFp2dEl4ZVpDbFdoRHgvUmJ3RU04VGkiLCJjcmVhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMDoxODo1NCIsInVwZGF0ZWRfYXQiOiIyMDE5LTA5LTI0IDAwOjE4OjU0IiwiaWF0IjoxNTY5Mjg4MzQyLCJleHAiOjE1NjkzNzQ3NDJ9.SiypzZLakGjwdlKKhKn8iwgJgINw4WZDHnMlAl0K73A
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 446
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:30:08 GMT
< Connection: keep-alive
<
{ [446 bytes data]
100   446  100   446    0     0  51193      0 --:--:-- --:--:-- --:--:-- 55750
* Connection #0 to host localhost left intact

user:dir $ diff testador.response aluno.response -s
Files testador.response and aluno.response are identical

user:dir $
```

### Adicione uma migração do banco

```console
user:dir $ pnpm run db-migrate-make TasksWithUsers

> tarefas-api@0.0.1 db-migrate-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:make "TasksWithUsers"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Using environment: development
Using environment: development
Created Migration: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/migrations/20190923183001_TasksWithUsers.js

user:dir  $
```

Edite o código-fonte `./migrations/*_TasksWithUsers.js`

```js
const table_name = "tasks";

exports.up = function(knex, Promise) {
  return knex.schema.alterTable(table_name, table => {
    table
      .integer("user")
      .notNullable()
      .default(0);
    table.foreign("user").references("users.oid");
  });
};

exports.down = function(knex, Promise) {
  return knex.schema.alterTable(table_name, table => {
    table.dropColumn("user");
  });
};
```

### Execute a migração da estrutura

```console
user:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
undefined
Batch 3 ran the following migrations:
20190916095748_TasksWithUsers.js

user:dir $
```

### Atualize os dados: vincule usuários a gentilezas

crie um _seed_ para atualizar os dados

```console
user:dir $ git mv seeds/what.js seeds/01_what.js
user:dir $ git mv seeds/who.js seeds/02_who.js
user:dir $ git mv seeds/users.js seeds/03_users.js
user:dir $ git mv seeds/tasks.js seeds/04_tasks.js

user:dir $ pnpm run db-seed-make 05-TasksWithUsers

> tarefas-api@0.0.1 db-seed-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:make "05-TasksWithUsers"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Created seed file: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/seeds/05-TasksWithUsers.js

user:dir $
```

Modifique o arquivo `./seeds/05-TasksWithUsers.js`

```js
const table_name = "tasks";
const user_table_name = "users";

exports.seed = function(knex) {
  return knex(table_name).then(function() {
    return knex(user_table_name)
      .where("login", "testador")
      .first("oid")
      .then(user => knex(table_name).update("user", user.oid));
  });
};
```

atualize os dados

```console
user:dir $ pnpm run db-populate

> tarefas-api@0.0.1 db-populate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:run

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Ran 5 seed files

user:dir $
```

### Atualize o código-fonte de rotas

Modifique o arquivo `./src/models/tasks.js`

```js
import knex from "../config/knex";

const table_name = "tasks";

class Task {
  static delete(oid) {
    return knex(table_name)
      .where("oid", oid)
      .andWhere("delete", false)
      .update("delete", true)
      .then(tasks_deleted => {
        if (tasks_deleted > 0) return Task.getById(oid);
        else return [];
      })
      .catch(error => []);
  }
  static undelete(oid) {
    return knex(table_name)
      .where("oid", oid)
      .andWhere("delete", true)
      .update("delete", false)
      .then(tasks_undeleted => {
        if (tasks_undeleted > 0) return Task.getById(oid);
        else return [];
      })
      .catch(error => []);
  }

  static create(data) {
    return knex(table_name)
      .insert(data)
      .then(oid => oid)
      .catch(err => -1);
  }

  static getAll(user_oid = 0) {
    return knex
      .from(table_name)
      .select()
      .where("user", user_oid)
      .then(results => Task.deserialize(results))
      .catch(err => err);
  }

  static getById(id) {
    return knex(table_name)
      .where("oid", id)
      .select()
      .then(results => {
        if (results.length == 0) return [];
        else return Task.deserialize(results);
      })
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
    if (json.length == undefined || json.length == 0) return {};
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

Modifique o arquivo `./routes/tasks/tasks_get.js`

```js
import { Task } from "../../models";

export default {
  method: "GET",
  path: "/tasks",
  options: {
    auth: "token"
  },
  handler: (request, reply) => {
    return Task.getAll(request.auth.credentials.id);
  }
};
```

**corriga o bug** no arquivo `./src/server.js`

```js
import Hapi from "@hapi/hapi";
import { User, Token } from "./models";

const server = new Hapi.Server({
  port: process.env.PORT || 8000,
  debug: { request: ["*"] }
});

const basic_validate = async (request, username, password) => {
  const user = await User.login(username, password);

  if (user == undefined) {
    return { credentials: null, isValid: false };
  }
  const token = Token.add(user);
  return {
    credentials: { id: user.oid, username: user.login, token: token },
    isValid: true
  };
};

const token_validate = async (user, request) => {
  const token = Token.findByUser(user);
  if (token == undefined) {
    return { credentials: null, isValid: false };
  } else {
    const credentials = { id: user.oid, name: user.name, token: token };
    // bug fixed
    //    const credentials = { id: user.id, name: user.name, token: token };
    return { credentials, isValid: true };
  }
};

const init = async () => {
  await server.register([require("@hapi/basic"), require("hapi-auth-jwt2")]);
  server.auth.strategy("simple", "basic", { validate: basic_validate });
  server.auth.strategy("token", "jwt", {
    key: Token.secret,
    validate: token_validate,
    verifyOptions: { algorithms: ["HS256"] }
  });

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

### Teste novamente a API

usuário `testador`

```console
user:dir $ curl http://localhost:8000/auth -v --user testador:secret
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'testador'
> GET /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic dGVzdGFkb3I6c2VjcmV0
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 469
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:55:05 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"id":10,"username":"testador","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEwLCJsb2dpbiI6InRlc3RhZG9yIiwiZW1haWwiOiJ0ZXN0YWRvckBnbWFpbC5jb20iLCJuYW1lIjoiVGVzdGFkb3IgZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHV6emtOMXBFUUphNDI3RDRPejB4VFVIWk8vQWRqTHJhIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI5MDEwNSwiZXhwIjoxNTY5Mzc2NTA1fQ.xssXa3iCGOyxvoMpedpY4e_xsLYMWRnkmpd6wZSUF90"}

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEwLCJsb2dpbiI6InRlc3RhZG9yIiwiZW1haWwiOiJ0ZXN0YWRvckBnbWFpbC5jb20iLCJuYW1lIjoiVGVzdGFkb3IgZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHV6emtOMXBFUUphNDI3RDRPejB4VFVIWk8vQWRqTHJhIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI5MDEwNSwiZXhwIjoxNTY5Mzc2NTA1fQ.xssXa3iCGOyxvoMpedpY4e_xsLYMWRnkmpd6wZSUF90"
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEwLCJsb2dpbiI6InRlc3RhZG9yIiwiZW1haWwiOiJ0ZXN0YWRvckBnbWFpbC5jb20iLCJuYW1lIjoiVGVzdGFkb3IgZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHV6emtOMXBFUUphNDI3RDRPejB4VFVIWk8vQWRqTHJhIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI5MDEwNSwiZXhwIjoxNTY5Mzc2NTA1fQ.xssXa3iCGOyxvoMpedpY4e_xsLYMWRnkmpd6wZSUF90
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 447
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:55:33 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
[{"oid":13,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias","done":false,"delete":false},{"oid":14,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias","done":false,"delete":false},{"oid":15,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false},{"oid":16,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false}]

user:dir $
```

usuário `aluno`

```console
user:dir $ curl http://localhost:8000/auth -v --user aluno:53cr3t
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'aluno'
> GET /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic YWx1bm86NTNjcjN0
> User-Agent: curl/7.54.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 454
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:52:22 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{"id":12,"username":"aluno","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEyLCJsb2dpbiI6ImFsdW5vIiwiZW1haWwiOiJhbHVub0BnbWFpbC5jb20iLCJuYW1lIjoiQWx1bm8gZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHVzQjlMLndFVU1NRndnNndrcnRGZzZuWnA4UWFPQVhxIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI4OTk0MiwiZXhwIjoxNTY5Mzc2MzQyfQ.pousPGUmST7sVtQbChrcjHTEo65ddmK_tw2OHKl5mtU"}

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEyLCJsb2dpbiI6ImFsdW5vIiwiZW1haWwiOiJhbHVub0BnbWFpbC5jb20iLCJuYW1lIjoiQWx1bm8gZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHVzQjlMLndFVU1NRndnNndrcnRGZzZuWnA4UWFPQVhxIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI4OTk0MiwiZXhwIjoxNTY5Mzc2MzQyfQ.pousPGUmST7sVtQbChrcjHTEo65ddmK_tw2OHKl5mtU"
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEyLCJsb2dpbiI6ImFsdW5vIiwiZW1haWwiOiJhbHVub0BnbWFpbC5jb20iLCJuYW1lIjoiQWx1bm8gZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHVzQjlMLndFVU1NRndnNndrcnRGZzZuWnA4UWFPQVhxIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI4OTk0MiwiZXhwIjoxNTY5Mzc2MzQyfQ.pousPGUmST7sVtQbChrcjHTEo65ddmK_tw2OHKl5mtU
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 2
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:53:44 GMT
< Connection: keep-alive
<
* Connection #0 to host localhost left intact
{}

user:dir $
```

**todos os dados diferentes**.

duvida, tente os comandos a seguir...

```console

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEwLCJsb2dpbiI6InRlc3RhZG9yIiwiZW1haWwiOiJ0ZXN0YWRvckBnbWFpbC5jb20iLCJuYW1lIjoiVGVzdGFkb3IgZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHV6emtOMXBFUUphNDI3RDRPejB4VFVIWk8vQWRqTHJhIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI5MDEwNSwiZXhwIjoxNTY5Mzc2NTA1fQ.xssXa3iCGOyxvoMpedpY4e_xsLYMWRnkmpd6wZSUF90" > testador-v2.response
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEwLCJsb2dpbiI6InRlc3RhZG9yIiwiZW1haWwiOiJ0ZXN0YWRvckBnbWFpbC5jb20iLCJuYW1lIjoiVGVzdGFkb3IgZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHV6emtOMXBFUUphNDI3RDRPejB4VFVIWk8vQWRqTHJhIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI5MDEwNSwiZXhwIjoxNTY5Mzc2NTA1fQ.xssXa3iCGOyxvoMpedpY4e_xsLYMWRnkmpd6wZSUF90
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 447
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:56:12 GMT
< Connection: keep-alive
<
{ [447 bytes data]
100   447  100   447    0     0  58978      0 --:--:-- --:--:-- --:--:-- 63857
* Connection #0 to host localhost left intact

user:dir $ curl http://localhost:8000/tasks -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEyLCJsb2dpbiI6ImFsdW5vIiwiZW1haWwiOiJhbHVub0BnbWFpbC5jb20iLCJuYW1lIjoiQWx1bm8gZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHVzQjlMLndFVU1NRndnNndrcnRGZzZuWnA4UWFPQVhxIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI4OTk0MiwiZXhwIjoxNTY5Mzc2MzQyfQ.pousPGUmST7sVtQbChrcjHTEo65ddmK_tw2OHKl5mtU" > aluno-v2.response
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /tasks HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOjEyLCJsb2dpbiI6ImFsdW5vIiwiZW1haWwiOiJhbHVub0BnbWFpbC5jb20iLCJuYW1lIjoiQWx1bm8gZmFrZSIsInBhc3N3b3JkIjoiJDJiJDEwJG95Uzd2bVNGTm5EdWRjaUZjSy8uaHVzQjlMLndFVU1NRndnNndrcnRGZzZuWnA4UWFPQVhxIiwiY3JlYXRlZF9hdCI6IjIwMTktMDktMjQgMDE6MzI6MTYiLCJ1cGRhdGVkX2F0IjoiMjAxOS0wOS0yNCAwMTozMjoxNiIsImlhdCI6MTU2OTI4OTk0MiwiZXhwIjoxNTY5Mzc2MzQyfQ.pousPGUmST7sVtQbChrcjHTEo65ddmK_tw2OHKl5mtU
>
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 2
< accept-ranges: bytes
< Date: Tue, 24 Sep 2019 01:54:20 GMT
< Connection: keep-alive
<
{ [2 bytes data]
100     2  100     2    0     0    263      0 --:--:-- --:--:-- --:--:--   285
* Connection #0 to host localhost left intact

user:dir $ diff testador-v2.response aluno-v2.response -s
1c1
< [{"oid":13,"title":"Bom dia","description":"Você deu bom dia para alguém há 44 dias","done":false,"delete":false},{"oid":14,"title":"Ligação","description":"Você ligou para seus amigos há 44 dias","done":false,"delete":false},{"oid":15,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false},{"oid":16,"title":"Zap","description":"Envie um zap aos seus pais em 6 horas","done":false,"delete":false}]
\ No newline at end of file
---
> {}
\ No newline at end of file

user:dir $
```

## Atualize os métodos do modelo e as rotas adicionando usuário

### Modifique o arquivo ./src/models/tasks.js

```js
```

### Modifique o arquivo ./src/routes/tasks/\*

Modifique o arquivo `./src/routes/tasks/task_delete.js`

```js
```

Modifique o arquivo `./src/routes/tasks/task_done.js`

```js
```

Modifique o arquivo `./src/routes/tasks/task_get_by_id.js`

```js
```

Modifique o arquivo `./src/routes/tasks/task_post.js`

```js
```

Modifique o arquivo `./src/routes/tasks/task_undelete.js`

```js
```

Modifique o arquivo `./src/routes/tasks/task_undone.js`

```js
```
