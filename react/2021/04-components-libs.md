# React App - Components - Bibliotecas de componentes

## Objetivos

-  Usar uma biblioteca de componente de interface do usuário
-  Mostrar alternativas de bibliotecas de componentes

## Sumário

1. Revisar componentes JSX com Arrow functions
2. Usar bibliotecas de componentes para estilizar componentes da aplicação

### 1. Revisar componentes JSX com Arrow functions

```jsx
// src/components/AppNavBar.js
import React from "react";

const AppNavBar = (props) => <div>{props.title}</div>;

export default AppNavBar;
```

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

### 2. Usar bibliotecas de componentes para estilizar componentes da aplicação

#### 2.1. React Bootstrap

1. Para aplicação web
2. Instalar bibliotecas React-Bootstrap com `yarn add react-bootstrap@next bootstrap@5.1.1`
3. Iniciar aplicação web
4. Ajustar instalação em `src/pages/_app.js`
5. Importar biblioteca de componetnes nos componetes da aplicação

```console
$ yarn add react-bootstrap@next bootstrap@5.1.1
$ yarn run dev
```

```jsx
// src/pages/_app.js
import "bootstrap/dist/css/bootstrap.min.css";

function MyApp({ Component, pageProps }) {
   return <Component {...pageProps} />;
}

export default MyApp;
```

```jsx
// src/components/AppNavBar.js
import React from "react";
import { Container, Navbar } from "react-bootstrap";

const AppNavBar = (props) => (
   <Container>
      <Navbar expand="lg" variant="dark" bg="dark">
         <Container>
            <Navbar.Brand href="#">{props.title}</Navbar.Brand>
         </Container>
      </Navbar>
   </Container>
);

export default AppNavBar;
```

```jsx
// src/components/AppBulletList.js
import React from "react";
import { Card, Container } from "react-bootstrap";

const AppBulletList = (props) => (
   <Container>
      <Card border="primary">
         <Card.Body>
            <Card.Title>{props.title}</Card.Title>
         </Card.Body>
         <Card.Text>Lista de bullets</Card.Text>
      </Card>
   </Container>
);

export default AppBulletList;
```

#### 2.2. Material UI

1. Para aplicação web
2. Desinstalar bibliotecas React-Bootstrap `yarn remove react-bootstrap bootstrap`
3. Instalar bibliotecas com `yarn add @mui/material @mui/icons-material @fontsource/roboto @emotion/styled @emotion/react`
4. Iniciar aplicação web
5. Ajustar instalação em `src/pages/_app.js`
6. Importar biblioteca de componetnes nos componetes da aplicação

```console
$ yarn remove react-bootstrap bootstrap
$ yarn add @mui/material @mui/icons-material @fontsource/roboto @emotion/styled @emotion/react
$ yarn run dev
```

```jsx
// src/pages/_app.js
import "@fontsource/roboto/300.css";
import "@fontsource/roboto/400.css";
import "@fontsource/roboto/500.css";
import "@fontsource/roboto/700.css";

function MyApp({ Component, pageProps }) {
   return <Component {...pageProps} />;
}

export default MyApp;
```

```jsx
// src/components/AppNavBar.js
import React from "react";

import AppBar from "@mui/material/AppBar";
import Box from "@mui/material/Box";
import Toolbar from "@mui/material/Toolbar";
import Typography from "@mui/material/Typography";
import IconButton from "@mui/material/IconButton";
import MenuIcon from "@mui/icons-material/Menu";

const AppNavBar = (props) => (
   <Box sx={{ flexGrow: 1 }}>
      <AppBar position="static">
         <Toolbar variant="dense">
            <IconButton
               edge="start"
               color="inherit"
               aria-label="menu"
               sx={{ mr: 2 }}
            >
               <MenuIcon />
            </IconButton>
            <Typography variant="h6" color="inherit" component="div">
               {props.title}
            </Typography>
         </Toolbar>
      </AppBar>
   </Box>
);

export default AppNavBar;
```

```jsx
// src/components/AppBulletList.js
import React from "react";

import { Card, CardContent, Typography } from "@mui/material/";

const AppBulletList = (props) => (
   <Card>
      <CardContent>
         <Typography variant="h4">{props.title}</Typography>
         <Typography variant="body2">bullets</Typography>
      </CardContent>
   </Card>
);

export default AppBulletList;
```

#### 2.3. Bulma

1. Para aplicação web
2. Desinstalar bibliotecas Material UI `yarn remove @mui/material @mui/icons-material @fontsource/roboto @emotion/styled @emotion/react`
3. Instalar bibliotecas com `yarn add bulma`
4. Iniciar aplicação web
5. Ajustar instalação em `src/pages/_app.js`
6. Importar biblioteca de componetnes nos componetes da aplicação

```console
$ yarn remove @mui/material @mui/icons-material @fontsource/roboto @emotion/styled @emotion/react
$ yarn add bulma
$ yarn run dev
```

```jsx
// src/pages/_app.js
import "bulma/css/bulma.min.css";

function MyApp({ Component, pageProps }) {
   return <Component {...pageProps} />;
}

export default MyApp;
```

```jsx
// src/components/AppNavBar.js
import React from "react";

const AppNavBar = (props) => (
   <nav className="navbar" role="navigation" aria-label="main navigation">
      <div id="navbarBasicExample" className="navbar-menu">
         <div className="navbar-start">
            <a className="navbar-item">{props.title}</a>
            <a className="navbar-item">Documentation</a>
            <div className="navbar-item has-dropdown is-hoverable">
               <a className="navbar-link">More</a>
               <div className="navbar-dropdown">
                  <a className="navbar-item">About</a>
                  <a className="navbar-item">Jobs</a>
                  <a className="navbar-item">Contact</a>
                  <hr className="navbar-divider" />
                  <a className="navbar-item">Report an issue</a>
               </div>
            </div>
         </div>
         <div className="navbar-end">
            <div className="navbar-item">
               <div className="buttons">
                  <a className="button is-primary">
                     <strong>Sign up</strong>
                  </a>
                  <a className="button is-light">Log in</a>
               </div>
            </div>
         </div>
      </div>
   </nav>
);

export default AppNavBar;
```

```jsx
// src/components/AppBulletList.js
import React from "react";

const AppBulletList = (props) => (
   <div className="card">
      <div className="card-content">
         <p className="title">{props.title}</p>
      </div>
      <footer className="card-footer">
         <p className="card-footer-item">
            <span>
               View on
               <a href="https://twitter.com/codinghorror/status/506010907021828096">
                  Twitter
               </a>
            </span>
         </p>
         <p className="card-footer-item">
            <span>
               Share on <a href="#">Facebook</a>
            </span>
         </p>
      </footer>
   </div>
);

export default AppBulletList;
```

## Bibliotecas

-  [React Bootstrap](https://react-bootstrap.github.io/)
-  [Material UI](https://mui.com/)
-  [Bulma](https://bulma.io/)
-  [Pure CSS](https://purecss.io/)
-  [Tailwind CSS](https://tailwindcss.com/)

## Vídeos aulas

[1h24 Momento síncrono](https://youtu.be/eX_ogQMIVfA)
