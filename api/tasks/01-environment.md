# API : CRUD de gentilezas : Configurando o ambiente <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [1 Configurando o ambiente](#1-configurando-o-ambiente)
  - [1.1 O projeto já esta na máquina, atualizando do repositório remoto](#11-o-projeto-j%c3%a1-esta-na-m%c3%a1quina-atualizando-do-reposit%c3%b3rio-remoto)
  - [1.2 O projeto NÃO esta na máquina, pegando o projeto do repositório remoto](#12-o-projeto-n%c3%83o-esta-na-m%c3%a1quina-pegando-o-projeto-do-reposit%c3%b3rio-remoto)
- [2. Instalando bibliotecas e executando a API](#2-instalando-bibliotecas-e-executando-a-api)

## 1 Configurando o ambiente

[ver instalação do node](../node/install)

### 1.1 O projeto já esta na máquina, atualizando do repositório remoto

```sh
cd [nickname]/2019-behappy-api

git pull

## zera a configuração global de usuário
git config --global --unset user.name
git config --global --unset user.email

## configura localmente no projeto as informações do usuário
git config user.name "Leonardo MINORA"
git config user.email "leonardo.minora@gmail.com"

### instalando as bibliotecas do package.json
pnpm i

```

### 1.2 O projeto NÃO esta na máquina, pegando o projeto do repositório remoto

```sh
mkdir [nickname] && cd $_

git clone https://github.com/leonardo-minora/2019-behappy-api.git

cd 2019-behappy-api

## configura localmente no projeto as informações do usuário
git config --global --unset user.name
git config --global --unset user.email

## configura localmente no projeto as informações do usuário
git config user.name "Leonardo MINORA"
git config user.email "leonardo.minora@gmail.com"

```

## 2. Instalando bibliotecas e executando a API

```bash
### adicionando novas bibliotecas
pnpm install @hapi/joi hapi-router

### executando o servidor em modo de supervisão de código
pnpm run dev-start

```
