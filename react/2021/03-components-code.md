# React App - Components com Arrow functions

## Objetivos

-  JavaScript - Arrow Functions e templates strings
-  Mostrar sintaxe de componente React com Arrow functions

## Sumário

1. Arrow functions e templates strings
2. Relembrando JSX
3. React components
4. Uso de _props_ em componentes
5. Exportando componentes para arquivos js
6. Publicando componentes no github

### 1. Arrow functions e templates strings

**template strings**

```js
console.log("aqui é uma string");
console.log("aqui tmabém é uma string");

let nome = "Minora";
console.log(`aqui é uma template strings ${nome}`);
```

**Arrow functions**

```js
function soma_1(operando_1, operando_2) {
   console.log(`${operando_1} + ${operando_2}`);
   return operando_1 + operando_2;
}

let soma_2 = function (operando_1, operando_2) {
   console.log(`${operando_1} + ${operando_2}`);
   return operando_1 + operando_2;
};

let soma_3 = (operando_1, operando_2) => {
   console.log(`${operando_1} + ${operando_2}`);
   return operando_1 + operando_2;
};
```

### 2. JSX (relembrando)

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

### 3. React components

**3.1. Limpar página inicial**

1. Retirar as referências de _css_ `import styles from '../styles/Home.module.css';`

**3.2. Organizar os diretórios**

1. criar diretório de código-fonte `/src`
2. mover diretório de páginas para dentro de código-fonte : de `/pages` para `src/pages`
3. mover diretório de estilos para dentro de código-fonte : de `/styles` para `src/styles`

**3.3. Iniciar aplicativo**

1. iniciar aplicativo : `yarn run dev`

**3.4. Construir o componente AppNavBar** a edição sempre no arquivo `/src/pages/index.js`

1. criar componente de barra de aplicativo (`const AppNavBar = () => <div>barra de navegação e menu do webapp</div>` dentro de `src/pages/index.js`
2. modificar página inicial `Home`, incluindo o componente `AppNavBar`

```jsx
export default function Home () {
   let title = 'Meu Bullet Journal';
   return (
    <div>
      <Head>
        <title>{title}</title>
        <meta name="description" content="Generated by create next app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <AppNavBar />
    <div>
   );
```

**3.4. Construir o componente Bulllet List** a edição sempre no arquivo `/src/pages/index.js`

1. criar o componente de _bullets_ (`const AppBulletList = () => <div>bullets</div>` dentro de `src/pages/index.js`
2. modificar página inicial `Home`, incluindo o componente `AppBulletList`

```jsx
export default function Home () {
  let title = 'Meu Bullet Journal';
  return (
    <div>
      <Head>
        <title>{title}</title>
        <meta name="description" content="Generated by create next app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <AppNavBar />
      <AppBulletList />
    <div>
  );
```

### 4. Uso de _props_ em componentes

**4.1. Passando um título para Bulllet List** a edição sempre no arquivo `/src/pages/index.js`

1. Modificar página inicial `Home`, incluindo propriedade `title` no componente `AppBulletList`

```jsx
export default function Home () {
  let title = 'Meu Bullet Journal';
  return (
    <div>
      <Head>
        <title>{title}</title>
        <meta name="description" content="Generated by create next app" />
        <link rel="icon" href="/favicon.ico" />
      </Head>
      <AppNavBar />
      <AppBulletList title='29/09/2021' />
      <AppBulletList title='30/09/2021' />
      <AppBulletList title='01/10/2021' />
    <div>
  );
```

**4.2. Usando o título passado para Bulllet List** a edição sempre no arquivo `/src/pages/index.js`

1. modificar o componente `AppBulletList`

```jsx
const AppBulletList = (props) => (
   <div>
      <h4>{props.title}</h4>
      <p>lista de bullets</p>
   </div>
);
```

### 5. Exportando componentes para arquivos js

`AppNavBar`

1. Criar arquivo `src/components/AppNavBar.js`
2. Mover o componente `AppNavBar` de `src/pages/index.js` para `src/components/AppNavBar.js`
3. Importar o componente `AppNavBar` em `src/pages/index.js`

```jsx
// src/components/AppNavBar.js
import React from "react";

const AppNavBar = (props) => <div>{props.title}</div>;

export default AppNavBar;
```

```jsx
// `src/pages/index.js`
import Head from "next/head";

import AppNavBar from "../components/AppNavBar";

const AppBulletList = (props) => (
   <div>
      <h4>{props.title}</h4>
      <p>lista de bullets</p>
   </div>
);

export default function Home() {
   let title = "Meu Bullet Journal";

   return (
      <div>
         <Head>
            <title>{title}</title>
            <meta name="description" content="Generated by create next app" />
            <link rel="icon" href="/favicon.ico" />
         </Head>

         <AppNavBar title={title} />
         <AppBulletList title="29/09/2021" />
         <AppBulletList title="30/09/2021" />
         <AppBulletList title="01/10/2021" />
      </div>
   );
}
```

`AppBulletList`

1. Criar arquivo `src/components/AppBulletList.js`
2. Mover o componente `AppBulletList` de `src/pages/index.js` para `src/components/AppBulletList.js`
3. Importar o componente `AppBulletList` em `src/pages/index.js`

```jsx
// src/components/AppBulletList.js
import React from "react";

const AppBulletList = (props) => (
   <div>
      <h4>{props.title}</h4>
      <p>lista de bullets</p>
   </div>
);

export default AppBulletList;
```

```jsx
// `src/pages/index.js`
import Head from "next/head";

import AppNavBar from "../components/AppNavBar";
import AppBulletList from "../components/AppBulletList";

export default function Home() {
   let title = "Meu Bullet Journal";

   return (
      <div>
         <Head>
            <title>{title}</title>
            <meta name="description" content="Generated by create next app" />
            <link rel="icon" href="/favicon.ico" />
         </Head>

         <AppNavBar title={title} />
         <AppBulletList title="29/09/2021" />
         <AppBulletList title="30/09/2021" />
         <AppBulletList title="01/10/2021" />
      </div>
   );
}
```

### 6. Publicando componentes no github

```sh
git add .
git commit -m "criando primeira versão de componentes"
git push
```

## Vídeo aula

-  [Momento Síncrono 1h20m](https://youtu.be/bsFC4qMkQOo)
-  [Momento Assíncrono m]()
