# API : Autenticação : Introdução <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Objetivo](#objetivo)
- [Preparando o ambiente](#preparando-o-ambiente)
- [Modificando o package.json](#modificando-o-packagejson)
- [Repositório de usuários (users.js)](#reposit%c3%b3rio-de-usu%c3%a1rios-usersjs)
- [Servidor (server.js) com login e senha](#servidor-serverjs-com-login-e-senha)
  - [testando servidor](#testando-servidor)
  - [testando login sem senha](#testando-login-sem-senha)
  - [testando login com senha ERRADA](#testando-login-com-senha-errada)
  - [testando login com senha CORRETA](#testando-login-com-senha-correta)
- [Servidor (server.js) listando e adicionando usuários](#servidor-serverjs-listando-e-adicionando-usu%c3%a1rios)
  - [listando todos os usuários](#listando-todos-os-usu%c3%a1rios)
  - [adicionando novo usuário, verificando adição e testando login](#adicionando-novo-usu%c3%a1rio-verificando-adi%c3%a7%c3%a3o-e-testando-login)
- [Servidor (server.js) adicinando token](#servidor-serverjs-adicinando-token)
  - [testando URL protegido por token](#testando-url-protegido-por-token)
  - [listando os usuários](#listando-os-usu%c3%a1rios)
  - [autenticando para pegar token](#autenticando-para-pegar-token)
  - [usando o token para acessar URL protegida](#usando-o-token-para-acessar-url-protegida)
  - [problema](#problema)
- [Adicionando um banco de dados intermediário](#adicionando-um-banco-de-dados-intermedi%c3%a1rio)
  - [adicionando a persistência de autenticação](#adicionando-a-persist%c3%aancia-de-autentica%c3%a7%c3%a3o)
  - [testando um token válido mas NÃO AUTENTICADO](#testando-um-token-v%c3%a1lido-mas-n%c3%83o-autenticado)
  - [Autenticando e usando o token para acessar URL protegida](#autenticando-e-usando-o-token-para-acessar-url-protegida)
- [Referências](#refer%c3%aancias)


## Objetivo

**projeto exemplo de autenticação com login/senha e token**

## Preparando o ambiente

```sh
mkdir auth && cd $_

pnpm init -y
pnpm add bcrypt @hapi/hapi @hapi/joi @hapi/basic hapi-auth-jwt2 jsonwebtoken nodemon redis

touch server.js
touch users.js
touch tokens.js
touch user.json
```

## Modificando o package.json

```json
{
  "name": "auth",
  "version": "1.0.0",
  "description": "Projeto exemplo de autenticação em API via longin/senha e token",
  "main": "server.js",
  "scripts": {
    "start": "nodemon server.js"
  },
  "keywords": ["api", "auth", "token", "jwt", "jsonwebtoken", "username password"],
  "author": "Leonardo A. MINORA",
  "license": "ISC",
  "dependencies": {
    "@hapi/basic": "^5.1.1",
    "@hapi/hapi": "^18.3.2",
    "@hapi/joi": "^15.1.1",
    "bcrypt": "^3.0.6",
    "hapi-auth-jwt2": "^8.6.1",
    "jsonwebtoken": "^8.5.1",
    "nodemon": "^1.19.1"
  }
}

```

```bash
pnpm run start
```

## Repositório de usuários (users.js)

```js
const bcrypt = require("bcrypt");

const users = {
  data: new Map(),
  json: function() {
    let users = [];
    for (let [key, value] of this.data) users.push(value);
    return users;
  },
  findByUsername: function(username) {
    for (let [key, value] of this.data) {
      if (value.nickname == username) return value;
    }
    return undefined;
  },
  findById: function(id) {
    for (let [key, value] of this.data) {
      if (key == id) return value;
    }
    return undefined;
  },
  add: function(user) {
    if (user.id == undefined) {
      user.id = this.data.size + 1;
    }
    const salt = bcrypt.genSaltSync();
    user.password = bcrypt.hashSync(user.password, salt);
    this.data.set(user.id, user);
  }
};

users.data.set(1, {
  id: 1,
  name: "Jen Jones",
  nickname: "jones",
  password: "$2b$10$C.FoziHbuzDOSISdncrrW.jQkEFeCNocxlnxBfAH/xyAWLrptuhrG",
  email: "jones@jen.com"
});

module.exports = users;
```

## Servidor (server.js) com login e senha

```js
const Hapi = require("@hapi/hapi");

const JWT = require("jsonwebtoken");
const bcrypt = require("bcrypt");

const secret = "NeverShareYourSecret";

const people = require("./users");

const basic_validation = async (request, username, password) => {
  user = people.findByUsername(username);

  if (user == undefined) return { credentials: null, isValid: false };

  const isValid = bcrypt.compareSync(password, user.password);
  const credentials = { id: user.id, name: user.name };
  return { isValid, credentials };
};

const init = async () => {
  const server = new Hapi.Server({ port: 8000 });

  await server.register(require("@hapi/basic"));

  server.auth.strategy("simple", "basic", { validate: basic_validation });

  server.route([
    {
      method: "GET",
      path: "/",
      config: { auth: false },
      handler: (request, reply) =>
        reply.response({ text: "Token not required" })
    },
    {
      method: "POST",
      path: "/auth",
      config: { auth: "simple" },
      handler: (request, reply) => reply.response(request.auth.credentials)
    }
  ]);
  await server.start();
  return server;
};

init()
  .then(server => {
    console.log("Server running at:", server.info.uri);
  })
  .catch(error => {
    console.log(error);
  });
```


### testando servidor

```console
leo:src$ curl http://localhost:8000/ -v -X GET
Note: Unnecessary use of -X or --request, GET is already inferred.
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
< content-length: 29
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 20:14:36 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"text":"Token not required"}

leo:src$

```

### testando login sem senha

```console
leo:src$ curl http://localhost:8000/auth -v -X POST
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> POST /auth HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 401 Unauthorized
< WWW-Authenticate: Basic
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 76
< Date: Mon, 02 Sep 2019 20:16:53 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"statusCode":401,"error":"Unauthorized","message":"Missing authentication"}

leo:src$

```

### testando login com senha ERRADA

```console
leo:src$ curl http://localhost:8000/auth -v -X POST --user minora:t3st3
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'minora'
> POST /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic bWlub3JhOnQzc3Qz
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 401 Unauthorized
* Authentication problem. Ignoring this.
< WWW-Authenticate: Basic error="Bad username or password"
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 128
< Date: Mon, 02 Sep 2019 20:17:49 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"statusCode":401,"error":"Unauthorized","message":"Bad username or password","attributes":{"error":"Bad username or password"}}

leo:src$

```

### testando login com senha CORRETA

```console
leo:src$ curl http://localhost:8000/auth -v -X POST --user jones:secret
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'jones'
> POST /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic am9uZXM6c2VjcmV0
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 27
< Date: Mon, 02 Sep 2019 20:18:52 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"id":1,"name":"Jen Jones"}

leo:src$ 

```

## Servidor (server.js) listando e adicionando usuários

```js
const Hapi = require("@hapi/hapi");
const Joi = require("@hapi/joi");

const bcrypt = require("bcrypt");

const secret = "NeverShareYourSecret";

const people = require("./users");

const basic_validation = async (request, username, password) => {
  user = people.findByUsername(username);

  if (user == undefined) return { credentials: null, isValid: false };

  const isValid = bcrypt.compareSync(password, user.password);
  const credentials = { id: user.id, name: user.name };
  return { isValid, credentials };
};

const init = async () => {
  const server = new Hapi.Server({ port: 8000 });

  await server.register( require("@hapi/basic") );

  server.auth.strategy("simple", "basic", { validate: basic_validation });

  server.route([
    {
      method: "GET",
      path: "/",
      config: { auth: false },
      handler: (request, reply) =>
        reply.response({ text: "Token not required" })
    },
    {
      method: "GET",
      path: "/users",
      config: { auth: false },
      handler: (request, reply) => reply.response(people.json())
    },
    {
      method: "POST",
      path: "/users",
      options: {
        auth: false,
        validate: {
          payload: Joi.object({
            name: Joi.string().required(),
            nickname: Joi.string().required(),
            password: Joi.string().required(),
            email: Joi.string().required()
          })
        }
      },
      handler: (request, reply) => {
        const user = {
          name: request.payload.name,
          nickname: request.payload.nickname,
          password: request.payload.password,
          email: request.payload.email
        };
        people.add(user);
        return reply.response({ text: "user created!" }).code(201);
      }
    },
    {
      method: "POST",
      path: "/auth",
      config: { auth: "simple" },
      handler: (request, reply) => reply.response(request.auth.credentials)
    }
  ]);
  await server.start();
  return server;
};

init()
  .then(server => {
    console.log("Server running at:", server.info.uri);
  })
  .catch(error => {
    console.log(error);
  });
```

### listando todos os usuários

```console
leo:src$ curl http://localhost:8000/users -v -X GET
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /users HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 146
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 20:24:30 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
[{"id":1,"name":"Jen Jones","nickname":"jones","password":"$2b$10$C.FoziHbuzDOSISdncrrW.jQkEFeCNocxlnxBfAH/xyAWLrptuhrG","email":"jones@jen.com"}]

leo:src$ 
```

### adicionando novo usuário, verificando adição e testando login

**modifique** arquivo `user.json`

```json
{
  "name": "Leonardo A. Minora",
  "nickname": "minora",
  "password": "s3cr3t",
  "email": "leonardo.minora@ifrn.edu.br"
}

```

**adicione usuário**

```console
leo:teste$ curl http://localhost:8000/users -v -X POST --header "content-type: application/json" --data "@user.json"
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> POST /users HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> content-type: application/json
> Content-Length: 119
> 
* upload completely sent off: 119 out of 119 bytes
< HTTP/1.1 201 Created
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 24
< Date: Mon, 02 Sep 2019 20:36:16 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"text":"user created!"}

leo:teste$ 

```

**liste todos os usuários**

```console
leo:teste$ curl http://localhost:8000/users -v -X GET
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /users HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 315
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 20:38:09 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
[{"id":1,"name":"Jen Jones","nickname":"jones","password":"$2b$10$C.FoziHbuzDOSISdncrrW.jQkEFeCNocxlnxBfAH/xyAWLrptuhrG","email":"jones@jen.com"},{"name":"Leonardo A. Minora","nickname":"minora","password":"$2b$10$/LzLmWnDkewJhQW/5kXxQuaAh3j1W8m0EOi2vv/E5yxdRbXxqkw5m","email":"leonardo.minora@ifrn.edu.br","id":2}]

leo:teste$ 

```

**teste autenticacação**

```console
leo:teste$ curl http://localhost:8000/auth -v -X POST --user minora:s3cr3t
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'minora'
> POST /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic bWlub3JhOnMzY3IzdA==
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 36
< Date: Mon, 02 Sep 2019 20:39:04 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"id":2,"name":"Leonardo A. Minora"}

leo:teste$ 

```

## Servidor (server.js) adicinando token

```js
const Hapi = require("@hapi/hapi");
const Joi = require("@hapi/joi");

const JWT = require("jsonwebtoken");
const bcrypt = require("bcrypt");

const secret = "NeverShareYourSecret";

const people = require("./users");

const basic_validation = async (request, username, password) => {
  user = people.findByUsername(username);

  if (user == undefined) return { credentials: null, isValid: false };

  const isValid = bcrypt.compareSync(password, user.password);
  const token = JWT.sign(user, secret);
  const credentials = { id: user.id, name: user.name, token: token };
  return { isValid, credentials };
};

// bring your own validation function
const jwt_validate = async (user, request) => {
  // do your checks to see if the person is valid
  if (people.findById(user.id) == undefined) {
    return { isValid: false };
  } else {
    return { isValid: true };
  }
};

const init = async () => {
  const server = new Hapi.Server({ port: 8000 });
  // include our module here ↓↓
  await server.register([require("@hapi/basic"), require("hapi-auth-jwt2")]);

  server.auth.strategy("simple", "basic", { validate: basic_validation });
  server.auth.strategy("jwt", "jwt", {
    key: secret, // Never Share your secret key
    validate: jwt_validate, // validate function defined above
    verifyOptions: { algorithms: ["HS256"] } // pick a strong algorithm
  });

  server.auth.default("jwt");

  server.route([
    {
      method: "GET",
      path: "/",
      config: { auth: false },
      handler: (request, reply) =>
        reply.response({ text: "Token not required" })
    },
    {
      method: "GET",
      path: "/users",
      config: { auth: false },
      handler: (request, reply) => reply.response(people.json())
    },
    {
      method: "POST",
      path: "/users",
      options: {
        auth: false,
        validate: {
          payload: Joi.object({
            name: Joi.string().required(),
            nickname: Joi.string().required(),
            password: Joi.string().required(),
            email: Joi.string().required()
          })
        }
      },
      handler: (request, reply) => {
        const user = {
          name: request.payload.name,
          nickname: request.payload.nickname,
          password: request.payload.password,
          email: request.payload.email
        };
        people.add(user);
        return reply.response({ text: "user created!" }).code(201);
      }
    },
    {
      method: "POST",
      path: "/auth",
      config: { auth: "simple" },
      handler: (request, reply) => reply.response(request.auth.credentials)
    },
    {
      method: "GET",
      path: "/restricted",
      config: { auth: "jwt" },
      handler: (request, reply) =>
        reply
          .response({ text: "You used a Token!" })
          .header("Authorization", request.headers.authorization)
    }
  ]);
  await server.start();
  return server;
};

init()
  .then(server => {
    console.log("Server running at:", server.info.uri);
  })
  .catch(error => {
    console.log(error);
  });
```

### testando URL protegido por token

**token inválido**

```console
leo:teste$ curl http://localhost:8000/restricted -v -H "Authorization: um.token.aqui"
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /restricted HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: um.token.aqui
> 
< HTTP/1.1 401 Unauthorized
< WWW-Authenticate: Token error="Invalid token"
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 106
< Date: Mon, 02 Sep 2019 20:46:28 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"statusCode":401,"error":"Unauthorized","message":"Invalid token","attributes":{"error":"Invalid token"}}

leo:teste$

```

### listando os usuários

```console
leo:teste$ http://localhost:8000/users -v -X GET
Note: Unnecessary use of -X or --request, GET is already inferred.
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /users HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 146
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 20:42:50 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
[{"id":1,"name":"Jen Jones","nickname":"jones","password":"$2b$10$C.FoziHbuzDOSISdncrrW.jQkEFeCNocxlnxBfAH/xyAWLrptuhrG","email":"jones@jen.com"}]

leo:teste$

```

### autenticando para pegar token

```console
leo:teste$ curl http://localhost:8000/auth -v -X POST --user jones:secret
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'jones'
> POST /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic am9uZXM6c2VjcmV0
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 334
< Date: Mon, 02 Sep 2019 21:02:38 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"id":1,"name":"Jen Jones","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ1ODE1OH0._n9xsDF8c7MzzWfzOKZ8ABhpg1fV168OwFSxWHEWatY"}

leo:teste$

```

### usando o token para acessar URL protegida

```console
leo:teste$ curl http://localhost:8000/restricted -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ1ODE1OH0._n9xsDF8c7MzzWfzOKZ8ABhpg1fV168OwFSxWHEWatY"
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /restricted HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ1ODE1OH0._n9xsDF8c7MzzWfzOKZ8ABhpg1fV168OwFSxWHEWatY
> 
< HTTP/1.1 200 OK
< authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ1ODE1OH0._n9xsDF8c7MzzWfzOKZ8ABhpg1fV168OwFSxWHEWatY
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 28
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 21:03:26 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"text":"You used a Token!"}

leo:teste$ 

```

### problema

1. A validação por token verifica apenas se o token é convertido em um usuário com mesmo identificador;
   1. Teste o com o token gerado pelo site [jwt.io](https://jwt.io) com os objetos json abaixo
   2. Copie o token para o comando no `bash`
   3. A saída será como no console abaixo, validando o token :-/

**dados json** 

`header`

```json
{
  "alg": "HS256",
  "typ": "JWT2"
}

```

`payload`

```json
{
  "id": 1,
  "name": "Aluno do IFRN",
  "iat": 9999999999
}

```

`signature` : `NeverShareYourSecret`

**testando no** `bash`

```console
leo:teste$ curl http://localhost:8000/restricted -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVDIifQ.eyJpZCI6MSwibmFtZSI6IkFsdW5vIGRvIElGUk4iLCJpYXQiOjk5OTk5OTk5OTl9.kdaR-cBmdm7_gawTWHhw9HV9ABvw1nrzUPSW2TTg9rE"
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /restricted HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVDIifQ.eyJpZCI6MSwibmFtZSI6IkFsdW5vIGRvIElGUk4iLCJpYXQiOjk5OTk5OTk5OTl9.kdaR-cBmdm7_gawTWHhw9HV9ABvw1nrzUPSW2TTg9rE
> 
< HTTP/1.1 200 OK
< authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVDIifQ.eyJpZCI6MSwibmFtZSI6IkFsdW5vIGRvIElGUk4iLCJpYXQiOjk5OTk5OTk5OTl9.kdaR-cBmdm7_gawTWHhw9HV9ABvw1nrzUPSW2TTg9rE
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 28
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 22:48:31 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"text":"You used a Token!"}

leo:teste$

```

## Adicionando um banco de dados intermediário

### adicionando a persistência de autenticação

**adicione** `tokens.js`

```js
const JWT = require("jsonwebtoken");

const tokens = {
  data: new Map(),
  secret: "NeverShareYourSecret",
  add: function(user, expiration) {
    token = JWT.sign(user, this.secret, { expiresIn: "24h" });
    this.data.set(user.email, token);
    return token;
  },
  rm: function(user) {
    token = this.data.delete(user.email);
    return token;
  },
  remove: function(user) {
    this.rm(user);
  },
  findByUser: function(user) {
    return this.data.get(user.email);
  }
};

module.exports = tokens;

```

`server.js`

```js
const Hapi = require("@hapi/hapi");
const Joi = require("@hapi/joi");
const bcrypt = require("bcrypt");

const people = require("./users");
const tokens = require("./tokens");

const basic_validation = async (request, username, password) => {
  user = people.findByUsername(username);

  if (user == undefined) return { credentials: null, isValid: false };

  const isValid = bcrypt.compareSync(password, user.password);
  const token = tokens.add(user);
  const credentials = { id: user.id, name: user.name, token: token };
  return { isValid, credentials };
};

const jwt_validate = async (user, request) => {
  token = tokens.findByUser(user);
  if (token == undefined) {
    return { credentials: null, isValid: false };
  } else {
    const credentials = { id: user.id, name: user.name, token: token };
    return { credentials, isValid: true };
  }
};

const init = async () => {
  const server = new Hapi.Server({ port: 8000 });
  await server.register([require("@hapi/basic"), require("hapi-auth-jwt2")]);

  server.auth.strategy("simple", "basic", { validate: basic_validation });
  server.auth.strategy("jwt", "jwt", {
    key: tokens.secret, // Never Share your secret key
    validate: jwt_validate, // validate function defined above
    verifyOptions: { algorithms: ["HS256"] } // pick a strong algorithm
  });

  server.auth.default("jwt");

  server.route([
    {
      method: "GET",
      path: "/",
      config: { auth: false },
      handler: (request, reply) =>
        reply.response({ text: "Token not required" })
    },
    {
      method: "GET",
      path: "/users",
      config: { auth: false },
      handler: (request, reply) => reply.response(people.json())
    },
    {
      method: "POST",
      path: "/users",
      options: {
        auth: false,
        validate: {
          payload: Joi.object({
            name: Joi.string().required(),
            nickname: Joi.string().required(),
            password: Joi.string().required(),
            email: Joi.string().required()
          })
        }
      },
      handler: (request, reply) => {
        const user = {
          name: request.payload.name,
          nickname: request.payload.nickname,
          password: request.payload.password,
          email: request.payload.email
        };
        people.add(user);
        return reply.response({ text: "user created!" }).code(201);
      }
    },
    {
      method: "POST",
      path: "/auth",
      config: { auth: "simple" },
      handler: (request, reply) => reply.response(request.auth.credentials)
    },
    {
      method: "GET",
      path: "/restricted",
      config: { auth: "jwt" },
      handler: (request, reply) =>
        reply
          .response({ text: "You used a Token!" })
          .header("Authorization", request.headers.authorization)
    }
  ]);
  await server.start();
  return server;
};

init()
  .then(server => {
    console.log("Server running at:", server.info.uri);
  })
  .catch(error => {
    console.log(error);
  });

```


### testando um token válido mas NÃO AUTENTICADO

```console
leo:teste$ curl http://localhost:8000/restricted -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ2NDY4NH0.Ca44xavIwci_Ii5WOrY4qGvDQNz1LZ_ngeZeLGWj6rw"
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /restricted HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ2NDY4NH0.Ca44xavIwci_Ii5WOrY4qGvDQNz1LZ_ngeZeLGWj6rw
> 
< HTTP/1.1 401 Unauthorized
< WWW-Authenticate: Token error="Invalid credentials"
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 118
< Date: Mon, 02 Sep 2019 23:01:53 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"statusCode":401,"error":"Unauthorized","message":"Invalid credentials","attributes":{"error":"Invalid credentials"}}

leo:teste$

```

### Autenticando e usando o token para acessar URL protegida

```console
leo:teste$ curl http://localhost:8000/auth -v -X POST --user jones:secret
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
* Server auth using Basic with user 'jones'
> POST /auth HTTP/1.1
> Host: localhost:8000
> Authorization: Basic am9uZXM6c2VjcmV0
> User-Agent: curl/7.54.0
> Accept: */*
> 
< HTTP/1.1 200 OK
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 334
< Date: Mon, 02 Sep 2019 22:51:24 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"id":1,"name":"Jen Jones","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ2NDY4NH0.Ca44xavIwci_Ii5WOrY4qGvDQNz1LZ_ngeZeLGWj6rw"}

leo:teste$ curl http://localhost:8000/restricted -v -H "Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ2NDY4NH0.Ca44xavIwci_Ii5WOrY4qGvDQNz1LZ_ngeZeLGWj6rw"
*   Trying ::1...
* TCP_NODELAY set
* Connection failed
* connect to ::1 port 8000 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /restricted HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.54.0
> Accept: */*
> Authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ2NDY4NH0.Ca44xavIwci_Ii5WOrY4qGvDQNz1LZ_ngeZeLGWj6rw
> 
< HTTP/1.1 200 OK
< authorization: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwibmFtZSI6IkplbiBKb25lcyIsIm5pY2tuYW1lIjoiam9uZXMiLCJwYXNzd29yZCI6IiQyYiQxMCRDLkZvemlIYnV6RE9TSVNkbmNyclcualFrRUZlQ05vY3hsbnhCZkFIL3h5QVdMcnB0dWhyRyIsImVtYWlsIjoiam9uZXNAamVuLmNvbSIsImlhdCI6MTU2NzQ2NDY4NH0.Ca44xavIwci_Ii5WOrY4qGvDQNz1LZ_ngeZeLGWj6rw
< content-type: application/json; charset=utf-8
< cache-control: no-cache
< content-length: 28
< accept-ranges: bytes
< Date: Mon, 02 Sep 2019 23:00:49 GMT
< Connection: keep-alive
< 
* Connection #0 to host localhost left intact
{"text":"You used a Token!"}

leo:teste$

```

## Referências

- [JsonWebToken](https://jwt.io)
  - [jsonwebtoken](https://github.com/auth0/node-jsonwebtoken)
- [Auth0](https://auth0.com)
  - [Documents](https://auth0.com/docs)
  - [Support](https://support.auth0.com)
- Hapi Plugins
  - [@hapi/basic](https://github.com/hapijs/basic)
  - [hapi-auth-jwt2](https://github.com/dwyl/hapi-auth-jwt2)