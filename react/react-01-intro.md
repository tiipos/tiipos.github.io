# [](#header-1) React App - Introdução

## [](#header-2) Sumário

1. Instalando e configurando as ferramentas
2. Testando do ambiente
3. Conectando ao GitHub
4. Entendento um App React
5. Tarefa

## [](#header-2) 1. Instalando e configurando as ferramentas

### [](#header-3) javascript: nvm, Node, npm, pnpm

By [CADES-IFRN](https://cades.natal.br) [Curso introdutório de Rect Native](https://github.com/cades-ifrn/minicurso-react-native-wtads/blob/master/install.md)

```sh
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash # install nvm
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
nvm install node # install node lastest version
npm i -g pnpm # instal pnpm
```

[pnpm](https://pnpm.js.org)

## [](#header-2) 2. Testando do ambiente

```sh
mkdir [nickname] # make your directory
cd [nickname]    # change the working directory
pnpx create-react-app behappywith.me # create a new react app named behappywith.me
cd behappywith.me # change the working directory
pnpm start        # start the new react app named behappywith.me
```

## [](#header-2) 3. Conectando ao GitHub

- **Fork** no repositório `https://github.com/tiipos/behappy-frontend.git` para sua conta!
- **Substituir** nos comandos abaixo o endereço `https://github.com/leonardo-minora/behappy-frontend.git` pelo seu repositório

```sh
git init
git add README.md package.json pnpm-debug.log public src .gitignore
git commit -m "2019 Aplicativo React inicial"

git remote add origin https://github.com/leonardo-minora/behappy-frontend.git
 ## Adiciona repositório remoto do GitHub
 ## Lembrar de mudar leonardo-minora pelo seu login
git pull --allow-unrelated-histories
git branch --set-upstream-to=origin/master master
git pull --allow-unrelated-histories
 ## sincroniza
 ##   do repositório remoto (GitHub)
 ##   para repositório local (computador)
git push ## publica no repositório remoto o código do App React
```

## [](#header-2) 4. Entendento um App React

### [](#header-3) Navegador Web: HTML, CSS e js

![Navegador Web](img/browser.jpeg)

### [](#header-3) App React

![App React](img/react-app.jpeg)

![Estrutura de diretórios](img/react-dir.png)

### [](#header-3) Funcionamento de um App React

![Funcionamento de um App React](img/react-dom.png)

1. No primeiro acesso à página,o React cria os componentes;
2. Os componentes são renderizados no VirtualDOM (antigo);
3. Como se trata da primeira renderização, o DOM real é
   atualizado na íntegra;
4. Os dados ( this.state ) dos componentes são alterados;
5. Os componentes são renderizados no VirtualDOM (novo);
6. O React calcula a diferença entre o Virtual DOM antigo e o
   novo;
7. Por fim, o React atualiza o DOM real apenas com as
   diferenças.

![virtual dom](img/react-virtualdom.png)

### [](#header-3) Anatomia do package.json

- name, version, author
- `dependencies` vs `devDependencies`
  - major version, minor version, patch releases
    - Major releases: \* or x
    - Minor releases: 1 or 1.x or ^1.0.4
    - Patch releases: 1.0 or 1.0.x or ~1.0.4
- scripts

```json
{
  "name": "front-end",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.4.2",
    "react-dom": "^16.4.2",
    "react-scripts": "1.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test --env=jsdom",
    "eject": "react-scripts eject"
  }
}
```

### [](#header-3) Anatomia JSX

```jsx
import React, { Component } from "react";
import logo from "./logo.svg";
import "./App.css";

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
      </div>
    );
  }
}

export default App;
```

### [](#header-3) Como chega no Componente App?

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <meta name="theme-color" content="#000000" />
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <title>React App</title>
  </head>
  <body>
    <noscript>
      You need to enable JavaScript to run this app.
    </noscript>
    <div id="root"></div>
  </body>
</html>
```

**index.js**

```jsx
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import registerServiceWorker from "./registerServiceWorker";

ReactDOM.render(<App />, document.getElementById("root"));
registerServiceWorker();
```

## [](#header-2) 5. Tarefa

1. Fazer o fork do repositório [behappy]()
