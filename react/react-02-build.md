# [](#header-1) React App - Build and Deploy


## [](#header-2) Sumário

1. Introdução


## [](#header-2) 1. Introdução

- **build** construir um instalável
- **deploy** executar uma instalação
- **publicar** publicar código-fonte ou app instalável


## [](#header-2) 2. Fazendo build

```sh
cd behappywith.me/front-end
pnpm run build
```
![Resultado após comando build](img/react-build-result.png)

![Antes de executar o build](img/react-build-before.png)

![Depois de executar o build](img/react-build-after.png)



## [](#header-2) 3. Realizando deploy

**O que é instalar um app web?**

**configurar o app** : package.json

adicionar
```json
  "engines": {
    "npm": "6.2.0",
    "node": "10.8.0"
  },
```

**Herokuu**

1. Acessar site http://heroku.com/
2. Criar conta no Heroku
3. No [dashboard](https://dashboard.heroku.com/apps) do Heroku, criar um app
4. No _dashboard_ do seu app, clicar na aba _Deploy_
5. Em _Deployment method_, selecionar GiHub
6. Definir qual o repositório esta o app
7. Em _Manual deploy_, clicar em _Deploy Branch_
8. Aguardar um tempo, olhando o log
9. No topo da página, clicar em _Open app_


![behappiest.herokuapp.com](img/react-deploy-heroku.png)