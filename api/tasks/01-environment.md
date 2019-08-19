# [](#header) API : CRUD de gentilezas : Configurando o ambiente <!-- omit in toc -->

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
