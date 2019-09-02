# API : Autenticação : Introdução <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Objetivo](#objetivo)
- [Preparando o ambiente](#preparando-o-ambiente)
- [Modificando o package.json](#modificando-o-packagejson)
- [Repositório de usuários (users.js)](#reposit%c3%b3rio-de-usu%c3%a1rios-usersjs)
- [Servidor (server.js) com login e senha](#servidor-serverjs-com-login-e-senha)
- [Servidor (server.js) listando e adicionando usuários](#servidor-serverjs-listando-e-adicionando-usu%c3%a1rios)
- [Servidor (server.js) adicinando token](#servidor-serverjs-adicinando-token)


## Objetivo

**projeto exemplo de autenticação com login/senha e token**

## Preparando o ambiente

```sh
mkdir auth && cd $_

pnpm init -y
pnpm add bcrypt @hapi/hapi @hapi/joi @hapi/basic hapi-auth-jwt2 jsonwebtoken nodemon

touch server.js
touch users.js

pnpm run start
```

## Modificando o package.json

```json
{
  "name": "hapi-auth-jwt2",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "start": "nodemon server.js"
  },
  "keywords": [],
  "author": "",
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