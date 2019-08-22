# [](#header) API : CRUD de gentilezas : revisitando a rota /tasks verbos POST e DELETE <!-- omit in toc -->

## Sumário <!-- omit in toc -->

## Parte 4 Modificando as rotas `/tasks` verdo POST e `/tasks/{task_id}` verbo DELETE

### Refatorando o verbo POST

## Arquivos finais

### Diretório: src/models

**src/models/index.js**

```js
import Task from "./tasks";
import What from "./what";
import Who from "./who";

export { Task, What, Who };
```

**src/models/tasks.js**

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

**src/models/What.js**

```js
import knex from "../config/knex";

const table_name = "what";

class What {
  static getAll() {
    return knex
      .from(table_name)
      .select()
      .then(results => What.deserialize(results))
      .catch(err => err);
  }

  static deserialize(json) {
    if (json.length == undefined || json.length == 0) return {};
    return json.map(data => {
      let what = new What();
      what.oid = data.oid ? data.oid : 0;
      what.title = data.title ? data.title : "";
      what.image_index = data.image_index ? data.image_index : 0;
      return what;
    });
  }
}

export default What;
```

**src/models/who.js**

```js
import knex from "../config/knex";

const table_name = "who";

class Who {
  static getAll() {
    return knex
      .from(table_name)
      .select()
      .then(results => Who.deserialize(results))
      .catch(err => err);
  }

  static deserialize(json) {
    if (json.length == undefined || json.length == 0) return {};
    return json.map(data => {
      let who = new Who();
      who.oid = data.oid ? data.oid : 0;
      who.title = data.title ? data.title : "";
      who.image_index = data.image_index ? data.image_index : 0;
      return who;
    });
  }
}

export default Who;
```

### Diretório: src/routes/tasks

**src/routes/tasks/task_delete.js**

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
  handler: (request, reply) =>
    Task.delete(request.params.task_id).then(tasks =>
      reply
        .response(response_message_builder(tasks, request.params.task_id))
        .code(delete_response_code(tasks))
    )
};
```

**src/routes/tasks/task_post.js**

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

**src/routes/tasks/task_undelete.js**

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
  handler: (request, reply) =>
    Task.undelete(request.params.task_id).then(tasks =>
      reply
        .response(response_message_builder(tasks, request.params.task_id))
        .code(delete_response_code(tasks))
    )
};
```

### Diretório: src/routes/what

**src/routes/what/what_get_all.js**

```js
import { What } from "../../models";

export default {
  method: "GET",
  path: "/what",
  handler: (request, reply) => What.getAll()
};
```

### Diretório: src/routes/who

**src/routes/who/who_get_all.js**

```js
import { Who } from "../../models";

export default {
  method: "GET",
  path: "/who",
  handler: (request, reply) => Who.getAll()
};
```
