# API : Autenticação : Login e senha <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Preparando o ambiente](#preparando-o-ambiente)
- [Preparando o repositório de dados](#preparando-o-reposit%c3%b3rio-de-dados)
- [Criando a entidade Usuário](#criando-a-entidade-usu%c3%a1rio)
- [Adicinando a rota do usuário](#adicinando-a-rota-do-usu%c3%a1rio)
- [Modificando o servidor](#modificando-o-servidor)


## Preparando o ambiente

```sh
npm install --global knex

pnpm add bcrypt @hapi/basic

pnpm run db-migrate-make users
pnpm run db-seed-make users

pnpm run dev-start
```

## Preparando o repositório de dados

Adicione `migrations/*_users.js`

```js
const table_name = "users";

exports.up = function(knex) {
  return knex.schema.createTable(table_name, table => {
    table.increments("oid").primary();
    table.string("login").notNullable();
    table.string("email").nullable();
    table.string("name").nullable();
    table.string("password").nullable();
    table.timestamps(false, true);
  });
};

exports.down = function(knex) {
    return knex.schema.dropTable(table_name);
};
```

Adicione `seeds/user.js`

```js
bcrypt = require("bcrypt");

const table_name = "users";
const salt = bcrypt.genSaltSync();

exports.seed = function(knex) {
  return knex(table_name).del()
    .then(function () {
      return knex(table_name).insert([
        {
          login: "testador", 
          email: "testador@gmail.com", 
          name: "Testador fake", 
          password: bcrypt.hashSync("secret", salt)
        },
        {
          login: "professor", 
          email: "professor@gmail.com", 
          name: "Professor fake", 
          password: bcrypt.hashSync("s3cr3t", salt)
        },
        {
          login: "aluno", 
          email: "aluno@gmail.com", 
          name: "Aluno fake", 
          password: bcrypt.hashSync("53cr3t", salt)
        }
      ]);
    });
};
```

Execute

```bash
pnpm run db-migrate
pnpm run db-populate
```

## Criando a entidade Usuário

Adicione `./src/models/users.js`

```js
import bcrypt from "bcrypt";

import knex from "../config/knex";

const table_name = "users";

class User {
    static login(username, password) {
        return knex(table_name).select().where('login', username).then(users => {
            if (users.length == undefined || users.length == 0) return false;
            return bcrypt.compareSync(password, users[0].password);
        });
    }
}

export default User;
```

## Adicinando a rota do usuário

Adicione `src/routes/users/auth.js`

```js
import { User } from "../../models";

export default {
  method: ["GET", "POST"],
  path: "/auth",
  options: {
    auth: "simple"
  },
  handler: (request, reply) => ({msg: "logged"})
};
```

## Modificando o servidor

Modificado `src/server.js`

```js
import Hapi from "@hapi/hapi";
import { User } from "../../models";

const server = new Hapi.Server({
  port: process.env.PORT || 8000,
  debug: { request: ["*"] }
});

const validate = async (request, username, password) => {
  if (!User.login(username, password)) return { credentials: null, isValid: false };
  return { credentials: {}, isValid: true };
};

const init = async () => {
  await server.register([{
    plugin: require("hapi-router"),
    options: {
      routes: "src/routes/**/*.js"
    }
  },
  {
    plugin: require("@hapi/basic"),
  }]);
  server.auth.strategy('simple', 'basic', { validate });

  await server.start();
  console.log("Server is running");
  console.log(server.info);
};

init();
```

