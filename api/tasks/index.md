# [](#header) API : CRUD de gentilezas <!-- omit in toc -->

## Sumário <!-- omit in toc -->

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

## 3. Notas de aulas

1. [Configuração do ambiente](./01-environment)
2. [Primera rota /tasks GET](./02-tasks-get)
3. [Adicionando verbos PUT e DELETE e configurando auto carregar rotass](./03-tasks-put-delete)
4. [Knex migrations](./04-knex-migrations)
5. [Adicionado as rotas /tasks/{id} , /tasks/{id}/done e /tasks/{id}/undone](./05-tasks-id)
6. [revisitando a rota /tasks verbos POST e DELETE](./06-tasks-refactor)
