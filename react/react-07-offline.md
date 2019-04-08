# [](#header-1) React App - Funcionando o aplicativo offline no browser <!-- omit in toc -->

## [](#header-2) Objetivos <!-- omit in toc -->

- PWA
  - Discutir alternativas de implementação do recurso _offline_
- BeHappy.Me
  - Implementar o arquivo `public/behappy.appcache`
  - Entender o arquivo `src/registerServiceWorker.js`

## [](#header-2) Sumário <!-- omit in toc -->

- [1. Preparando o ambiente](#1-preparando-o-ambiente)
- [2. PWA Requisito funcionar o app offline](#2-pwa-requisito-funcionar-o-app-offline)
- [3. Implementar o arquivo appcache](#3-implementar-o-arquivo-appcache)
- [4. Entender o arquivo registerServiceWorker.js](#4-entender-o-arquivo-registerserviceworkerjs)
- [5. Registrando as mudanças no GitHub](#5-registrando-as-mudan%C3%A7as-no-github)
- [6. Publicando no Heroku](#6-publicando-no-heroku)
- [7. Homework](#7-homework)

### 1. Preparando o ambiente

**para quem não tem o pnpm, npm, node**

```sh
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash # install nvm
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
nvm install node # install node lastest version
npm i -g pnpm # instal pnpm
```

**para quem tem o código no computador**

```sh
### acessa diretório da aplicação
cd [nickname]/behappywith.me
git pull ### baixa o código-fonte

### Modifica informações do desenvolvedor
git config --global --unset user.name
git config --global --unset user.email
git config user.name "$NOME"
git config user.email "$EMAIL"

atom . ### abre o editor de código-fonte com o diretório atual

### Instala as bibliotecas do app
pnpm install
pnpm start
```

**para quem NÃO tem o código no computador**

```sh
### acessa diretório da aplicação
mkdir [nickname] && cd $_
### Mudar o [github-username] pelo seu nickname do GitHub
git clone https://github.com/[github-username]/behappy-frontend.git
mv behappy-frontend behappywith.me && cd $_

### Modifica informações do desenvolvedor
git config --global --unset user.name
git config --global --unset user.email
git config user.name "$NOME"
git config user.email "$EMAIL"

atom . ### abre o editor de código-fonte com o diretório atual

### Instala as bibliotecas do app
pnpm install
pnpm start
```

### 2. PWA Requisito funcionar o app offline

**Requisito** : uma aplicação progressiva precisa fornecer a capacidade de funcionar _offline_

- **Técnicas**
  - Application cache ou _AppCache_
    - [portabilidade de ~94% dos navegadores](https://caniuse.com/#feat=offline-apps): Opera mini
    - API declarativa
    - relacionar quais arquivos ele precisa armazenar no arquivo _manifest_
  - Service worker
    - Cache storage do browser
    - "_Recurso mais poderoso e exigido em aplicações progressivas_"
    - [portabilidade](https://caniuse.com/#feat=serviceworkers): problemas com IE e Opera mini

### 3. Implementar o arquivo appcache

**arquivo**: `public/behappy.appcache`

```sh
touch public/behappy.appcache
```

`public/behappy.appcache` : edite e cole o conteúdo abaixo

```manifaest
CACHE MANIFEST
# v0.0.1

CACHE:
bundle.js
style.css
img/avatars.png
img/botoes.png
img/favicon.ico
img/logo.png
img/icon-72x72.png
img/icon-96x96.png
img/icon-128x128.png
img/icon-144x144.png
img/icon-152x152.png
img/icon-192x192.png
img/icon-384x384.png
img/icon-512x512.png

NETWORK:
*
```

`public/index.html` : editar o arquivo e modificar a linha 2

```html
<!DOCTYPE html>
<html lang="en" manifest="%PUBLIC_URL%/behappy.appcache">
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <meta name="theme-color" content="#000000" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <title>BeHappy.Me</title>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
  </body>
</html>
```

### 4. Entender o arquivo registerServiceWorker.js

`src/registerServiceWorker.js`

```js
// In production, we register a service worker to serve assets from local cache.

// This lets the app load faster on subsequent visits in production, and gives
// it offline capabilities. However, it also means that developers (and users)
// will only see deployed updates on the "N+1" visit to a page, since previously
// cached resources are updated in the background.

// To learn more about the benefits of this model, read https://goo.gl/KwvDNy.
// This link also includes instructions on opting out of this behavior.

const isLocalhost = Boolean(
  window.location.hostname === "localhost" ||
    // [::1] is the IPv6 localhost address.
    window.location.hostname === "[::1]" ||
    // 127.0.0.1/8 is considered localhost for IPv4.
    window.location.hostname.match(
      /^127(?:\.(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)){3}$/
    )
);

export default function register() {
  if (process.env.NODE_ENV === "production" && "serviceWorker" in navigator) {
    // The URL constructor is available in all browsers that support SW.
    const publicUrl = new URL(process.env.PUBLIC_URL, window.location);
    if (publicUrl.origin !== window.location.origin) {
      // Our service worker won't work if PUBLIC_URL is on a different origin
      // from what our page is served on. This might happen if a CDN is used to
      // serve assets; see https://github.com/facebookincubator/create-react-app/issues/2374
      return;
    }

    window.addEventListener("load", () => {
      const swUrl = `${process.env.PUBLIC_URL}/service-worker.js`;

      if (isLocalhost) {
        // This is running on localhost. Lets check if a service worker still exists or not.
        checkValidServiceWorker(swUrl);

        // Add some additional logging to localhost, pointing developers to the
        // service worker/PWA documentation.
        navigator.serviceWorker.ready.then(() => {
          console.log(
            "This web app is being served cache-first by a service " +
              "worker. To learn more, visit https://goo.gl/SC7cgQ"
          );
        });
      } else {
        // Is not local host. Just register service worker
        registerValidSW(swUrl);
      }
    });
  }
}

function registerValidSW(swUrl) {
  navigator.serviceWorker
    .register(swUrl)
    .then(registration => {
      registration.onupdatefound = () => {
        const installingWorker = registration.installing;
        installingWorker.onstatechange = () => {
          if (installingWorker.state === "installed") {
            if (navigator.serviceWorker.controller) {
              // At this point, the old content will have been purged and
              // the fresh content will have been added to the cache.
              // It's the perfect time to display a "New content is
              // available; please refresh." message in your web app.
              console.log("New content is available; please refresh.");
            } else {
              // At this point, everything has been precached.
              // It's the perfect time to display a
              // "Content is cached for offline use." message.
              console.log("Content is cached for offline use.");
            }
          }
        };
      };
    })
    .catch(error => {
      console.error("Error during service worker registration:", error);
    });
}

function checkValidServiceWorker(swUrl) {
  // Check if the service worker can be found. If it can't reload the page.
  fetch(swUrl)
    .then(response => {
      // Ensure service worker exists, and that we really are getting a JS file.
      if (
        response.status === 404 ||
        response.headers.get("content-type").indexOf("javascript") === -1
      ) {
        // No service worker found. Probably a different app. Reload the page.
        navigator.serviceWorker.ready.then(registration => {
          registration.unregister().then(() => {
            window.location.reload();
          });
        });
      } else {
        // Service worker found. Proceed as normal.
        registerValidSW(swUrl);
      }
    })
    .catch(() => {
      console.log(
        "No internet connection found. App is running in offline mode."
      );
    });
}

export function unregister() {
  if ("serviceWorker" in navigator) {
    navigator.serviceWorker.ready.then(registration => {
      registration.unregister();
    });
  }
}
```

### 5. Registrando as mudanças no GitHub

1. Fechar servidor web `CTRL + C`
2. Adicioanar novos e modificados arquivos (`git add`) no repositório local
3. Registrar mudanças (`git commit`) no repositório local
4. Publicar mudanças no repositório remoto (`git pull` e `git push`)

```sh
# Fechar o servidor web
### 1. Pressionar CTRL+C
###

### 2. Adicionar arquivos novos e modificados no repositório local
git add public/behappy.appcache public/index.html

### 3. Registrar mudanças no repositório local
git commit -m "$MSG"

### Publicar as mudanças no repositório remoto
git pull
git push
```

### 6. Publicando no Heroku

1. Acessar o [Heroku](https://www.heroku.com) e se autenticar
2. Acessar o link do seu _App_ no Heroku
3. No _dashboard_ do seu _App_, clicar na aba _Deploy_
4. Em _Deployment method_, selecionar GiHub
5. Definir qual o repositório esta o _App_
6. Em _Manual deploy_, clicar em _Deploy Branch_
7. Aguardar um tempo, olhando o log
8. No topo da página, clicar em _Open App_

### 7. Homework

**Just do it!!!**
