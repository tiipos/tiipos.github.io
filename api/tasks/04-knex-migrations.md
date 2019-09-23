# API : CRUD de gentilezas : Knex migrations <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Entendendo o knex migration e seed](#entendendo-o-knex-migration-e-seed)
  - [Melhorando o package.json](#melhorando-o-packagejson)
  - [Adicionando Qual gentileza?](#adicionando-qual-gentileza)
  - [Adicionando Para quem?](#adicionando-para-quem)
  - [Modificando a Gentileza](#modificando-a-gentileza)

## Entendendo o knex migration e seed

### Melhorando o package.json

Adicionando scripts para executar o migrate e seed por scripts do package.json

```console
user@pc:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Batch 5 ran the following migrations:
20190810030154_task_update_with_done_delete.js
user@pc:dir $ pnpm run db-populate

> tarefas-api@0.0.1 db-populate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:run

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Ran 3 seed files
user@pc:dir $
```

```json
{
  "name": "tarefas-api",
  "version": "0.0.1",
  "description": "API do App Lista de tarefas",
  "main": "bootstrap.js",
  "engines": {
    "node": "12"
  },
  "scripts": {
    "dev-start": "nodemon bootstrap",
    "start": "node bootstrap",
    "postinstall": "knex migrate:latest && knex seed:run",
    "db-migrate": "knex migrate:up",
    "db-migrate-make": "knex migrate:make",
    "db-migrate-down": "knex migrate:down",
    "db-seed-make": "knex seed:make",
    "db-populate": "knex seed:run",
    "db-create": "knex migrate:latest && knex seed:run"
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
    "hapi-router": "^5.0.0",
    "knex": "^0.19.1",
    "sqlite3": "^4.0.2"
  },
  "devDependencies": {
    "nodemon": "^1.19.1"
  }
}
```

### Adicionando Qual gentileza?

Criando a _migration_ e _seed_:

```console
user@pc:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Batch 2 ran the following migrations:
20190810024532_what.js
user@pc:dir $ pnpm run db-seed-make what

> tarefas-api@0.0.1 db-seed-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:make "what"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Created seed file: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/seeds/what.js
user@pc:dir $

```

editando o arquivo: `migrations/*what.js`

```js
const table_name = "what";

exports.up = function(knex, Promise) {
  return knex.schema.createTable(table_name, table => {
    table.increments("oid").primary();
    table.string("title").notNullable();
    table.integer("image_index").nullable();
    table.timestamps();
  });
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTable(table_name);
};
```

editando o arquivo: `seeds/what.js`

```js
const table_name = "what";

exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex(table_name)
    .del()
    .then(function() {
      // Inserts seed entries
      return knex(table_name).insert([
        { title: "Zap", image_index: 0 },
        { title: "Bom dia", image_index: 1 },
        { title: "Ligação", image_index: 2 },
        { title: "Aperto de mão", image_index: 3 },
        { title: "Abraço", image_index: 4 },
        { title: "Doce", image_index: 5 },
        { title: "Presente", image_index: 6 }
      ]);
    });
};
```

realizando a migração e populando o banco:

```console
user@pc:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Batch 2 ran the following migrations:
20190810024532_what.js

user@pc:dir $ pnpm run db-populate

> tarefas-api@0.0.1 db-populate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:run

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Ran 2 seed files

user@pc:dir $
```

### Adicionando Para quem?

```console
user@pc:dir $ pnpm run db-migrate-make who

> tarefas-api@0.0.1 db-migrate-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:make "who"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Using environment: development
Using environment: development
Created Migration: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/migrations/20190810024822_who.js

user@pc:dir $ pnpm run db-seed-make who

> tarefas-api@0.0.1 db-seed-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:make "who"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Created seed file: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/seeds/who.js

user@pc:dir $
```

editando o arquivo: `migrations/*who.js`

```js
const table_name = "who";

exports.up = function(knex, Promise) {
  return knex.schema.createTable(table_name, table => {
    table.increments("oid").primary();
    table.string("title").notNullable();
    table.integer("image_index").nullable();
    table.timestamps(false, true);
  });
};

exports.down = function(knex, Promise) {
  return knex.schema.dropTable(table_name);
};
```

editando o arquivo: `seeds/what.js`

```js
const table_name = "who";

exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex(table_name)
    .del()
    .then(function() {
      // Inserts seed entries
      return knex(table_name).insert([
        { title: "Pais", image_index: 0 },
        { title: "Filhos", image_index: 1 },
        { title: "Cônjuges", image_index: 2 },
        { title: "Amigos", image_index: 3 },
        { title: "Pets", image_index: 4 },
        { title: "Outros", image_index: 5 }
      ]);
    });
};
```

realizando a migração e populando o banco:

```console
user@pc:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Batch 3 ran the following migrations:
20190810024822_who.js

user@pc:dir $ pnpm run db-populate

> tarefas-api@0.0.1 db-populate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:run

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Ran 3 seed files

user@pc:dir $
```

### Modificando a Gentileza

```console
user@pc:dir $ pnpm run db-migrate-make task_update_with_what_and_who

> tarefas-api@0.0.1 db-migrate-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:make "task_update_with_what_and_who"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Using environment: development
Using environment: development
Created Migration: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/migrations/20190810030133_task_update_with_what_and_who.js

user@pc:dir $ pnpm run db-migrate-make task_update_with_done_delete

> tarefas-api@0.0.1 db-migrate-make /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:make "task_update_with_done_delete"

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Using environment: development
Using environment: development
Created Migration: /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api/migrations/20190810030154_task_update_with_done_delete.js

user@pc:dir $
```

editando o arquivo: `migrations/*task_update_with_what_and_who.js`

```js
const table_name = "tasks";

exports.up = function(knex, Promise) {
  return knex.schema.alterTable(table_name, table => {
    table
      .integer("what")
      .notNullable()
      .default(0);
    table
      .integer("who")
      .notNullable()
      .default(0);
  });
};

exports.down = function(knex, Promise) {
  return knex.schema.alterTable(table_name, table => {
    table.dropColumn("what").dropColumn("who");
  });
};
```

editando o arquivo: `migrations/*task_update_with_done_delete.js`

```js
const table_name = "tasks";

exports.up = function(knex, Promise) {
  return knex.schema.alterTable(table_name, table => {
    table
      .boolean("done")
      .notNullable()
      .default(false);
    table
      .boolean("delete")
      .notNullable()
      .default(false);
  });
};

exports.down = function(knex, Promise) {
  return knex.schema.alterTable(table_name, table => {
    table.dropColumn("done").dropColumn("delete");
  });
};
```

modificando o arquivo: `seeds/tasks.js`

```js
const table_name = "tasks";

exports.seed = function(knex, Promise) {
  // Deletes ALL existing entries
  return knex(table_name)
    .del()
    .then(function() {
      // Inserts seed entries
      return knex(table_name).insert([
        {
          title: "Bom dia",
          description: "Você deu bom dia para alguém há 44 dias",
          what: 2,
          who: 6
        },
        {
          title: "Ligação",
          description: "Você ligou para seus amigos há 44 dias",
          what: 3,
          who: 4
        },
        {
          title: "Zap",
          description: "Envie um zap aos seus pais em 6 horas",
          what: 1,
          who: 1
        }
      ]);
    });
};
```

realizando a migração e populando o banco:

```console
user@pc:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Batch 4 ran the following migrations:
20190810030133_task_update_with_what_and_who.js

user@pc:dir $ pnpm run db-migrate

> tarefas-api@0.0.1 db-migrate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex migrate:up

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Batch 5 ran the following migrations:
20190810030154_task_update_with_done_delete.js

user@pc:dir $ pnpm run db-populate

> tarefas-api@0.0.1 db-populate /Users/leo/src/disciplinas/TII-POS/2019-src/2019-behappy-api
> knex seed:run

Using environment: development
sqlite does not support inserting default values. Set the `useNullAsDefault` flag to hide this warning. (see docs http://knexjs.org/#Builder-insert).
Ran 3 seed files

user@pc:dir $
```
