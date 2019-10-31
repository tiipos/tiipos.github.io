# API : Realizando requisições a partir de um App React <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Configurando](#configurando)
- [Serviços](#servi%c3%a7os)
- [Páginas: Login e Gentilezas](#p%c3%a1ginas-login-e-gentilezas)
- [Refazendo página de Login](#refazendo-p%c3%a1gina-de-login)
- [Refazendo a página de Gentilezas](#refazendo-a-p%c3%a1gina-de-gentilezas)
- [Adicionando método de done na página de Gentilezas](#adicionando-m%c3%a9todo-de-done-na-p%c3%a1gina-de-gentilezas)

## Configurando

```bash
pnpx create-react-app react-axios-example
cd react-axios-example

pnpm add axios
pnpm add semantic-ui-css semantic-ui-react

pnpm start
```

arquivo `./src/index.js`

```js
import React from "react";
import ReactDOM from "react-dom";
// import './index.css';
import "semantic-ui-css/semantic.min.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";

ReactDOM.render(<App />, document.getElementById("root"));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```

arquivo `./public/index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta name="description" content="BeHappyWithMe example API Request" />
    <link rel="apple-touch-icon" href="logo192.png" />
    <!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.

      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <title>BeHappyWith.me</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>
```

arquivo `./package.json`

```json
{
  "name": "be-happy-with-me-example",
  "author": {
    "email": "leonardo.minora@gmail.com",
    "name": "Leonardo MINORA"
  },
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "axios": "^0.19.0",
    "react": "^16.10.2",
    "react-dom": "^16.10.2",
    "react-scripts": "3.2.0",
    "semantic-ui-css": "^2.4.1",
    "semantic-ui-react": "^0.88.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": {
    "production": [">0.2%", "not dead", "not op_mini all"],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}
```

## Serviços

## Páginas: Login e Gentilezas

arquivo `./src/components/Login/index.js`

```jsx
import React, { Component } from "react";
import { Container, Header } from "semantic-ui-react";

class LoginPage extends Component {
  render() {
    return (
      <Container fluid>
        <Header>Gentilezas</Header>
      </Container>
    );
  }
}

export default LoginPage;
```

arquivo `./src/components/Task/index.js`

```jsx
import React, { Component } from "react";
import { Container, Header } from "semantic-ui-react";

class TaskPage extends Component {
  render() {
    return (
      <Container fluid>
        <Header>Gentilezas</Header>
      </Container>
    );
  }
}

export default TaskPage;
```

arquivo `./src/components/index.js`

```js
import LoginPage from "./Login";
import TaskPage from "./Task";

export { LoginPage, TaskPage };
```

arquivo `./src/App.js`

```js
import React, { Component } from "react";
import { Container } from "semantic-ui-react";

import { isAuthenticated } from "./infrastructure/auth";

import { LoginPage, TaskPage } from "./components";

class App extends Component {
  render = () => {
    return (
      <Container style={{ margin: 20 }}>
        {!isAuthenticated() && <LoginPage />}
        {isAuthenticated() && <TaskPage />}
      </Container>
    );
  };
}

export default App;
```

## Refazendo página de Login

arquivo `./src/App.js`

```js
import React, { Component } from "react";
import { Container } from "semantic-ui-react";

import { isAuthenticated } from "./infrastructure/auth";

import { LoginPage, TaskPage } from "./components";

class App extends Component {
  state = { user: undefined };

  update = user => {
    this.setState({ user: user });
  };

  render = () => {
    return (
      <Container style={{ margin: 20 }}>
        {!isAuthenticated() && <LoginPage onLogin={this.update} />}
        {isAuthenticated() && <TaskPage />}
      </Container>
    );
  };
}

export default App;
```

arquivo `./src/components/Login/index.js`

```jsx
import React, { Component } from "react";
import {
  Button,
  Checkbox,
  Container,
  Form,
  Header,
  Input
} from "semantic-ui-react";

import api from "../../infrastructure/api";
import { login } from "../../infrastructure/auth";

class LoginPage extends Component {
  state = { nickname: "", password: "" };

  handleChange = (event, { name, value }) => {
    this.setState({ [name]: value });
  };

  handleLogin = async event => {
    event.preventDefault();
    const { nickname, password } = this.state;
    if (!nickname || !password) {
      this.setState({ error: "por favor, preencha email e senha!!!" });
    } else {
      try {
        const response = await api.get("/auth", {
          auth: {
            username: nickname,
            password: password
          }
        });
        login(response.data.token);
        if (this.props.onLogin !== undefined) {
          this.props.onLogin(response.data);
          console.log("====================================");
          console.log(response.data);
          console.log("====================================");
        }
      } catch (error) {
        this.setState({ error: "por favor, verifique seu email e senha!!!" });
      }
    }
  };

  render() {
    const { nickname, password } = this.state;

    return (
      <Container fluid>
        <Header>Login</Header>
        <Form onSubmit={this.handleLogin}>
          <Form.Field>
            <Input
              label="Apelido"
              icon="user"
              iconPosition="right"
              placeholder="Apelido"
              name="nickname"
              value={nickname}
              onChange={this.handleChange}
            />
          </Form.Field>
          <Form.Field>
            <Input
              label="Senha"
              icon="lock"
              iconPosition="right"
              type="password"
              placeholder="Senha"
              name="password"
              value={password}
              onChange={this.handleChange}
            />
          </Form.Field>
          <Form.Field>
            <Checkbox label="Manter conectado" />
          </Form.Field>
          <Button type="submit">Login</Button>
        </Form>
      </Container>
    );
  }
}
export default LoginPage;
```

## Refazendo a página de Gentilezas

arquivo `./src/components/Tasks/index.js`

```jsx
import React from "react";

import {
  Button,
  Card,
  Container,
  Header,
  Icon,
  Placeholder
} from "semantic-ui-react";

import api from "../../infrastructure/api";

class TaskPage extends React.Component {
  state = {
    loading: true,
    data: undefined
  };

  list2map = list => {
    const map = new Map();
    list.forEach(item => map.set(item.oid, item));
    return map;
  };

  componentDidMount = () => {
    api
      .get("tasks")
      .then(response => response.data)
      .then(list => this.list2map(list))
      .then(tasks => this.setState({ data: tasks, loading: false }));
  };

  renderHeader = task => {
    return (
      <Card.Header>
        {task.title + " "}
        <Button
          disabled={task.delete || task.done}
          circular
          icon="trash alternate outline"
        />
        <Button disabled={task.done} circular icon="thumbs up outline" />
      </Card.Header>
    );
  };

  renderDescription = description => {
    return <Card.Meta>{description}</Card.Meta>;
  };

  renderDetails = task => (
    <Card.Description align="center">
      <Icon name="phone" circular={true} />
      <Icon name="plus" />
      <Icon name="users" circular={true} />
      <Icon name="arrow right" />
      <Icon name="dollar sign" circular={true} />
    </Card.Description>
  );

  renderCard = (task, loading) => {
    if (loading) {
      return (
        <Card>
          <Placeholder fluid>
            <Placeholder.Line length="medium" />
            <Placeholder.Line length="long" />
            <Placeholder.Line length="short" />
          </Placeholder>
        </Card>
      );
    } else {
      return (
        <Card key={task.oid}>
          <Card.Content>
            {this.renderHeader(task)}
            {this.renderDescription(task.description)}
            {this.renderDetails(task)}
          </Card.Content>
        </Card>
      );
    }
  };

  render() {
    const { data, loading } = this.state;
    const tasks = data !== undefined ? [...data].map(item => item[1]) : [];

    return (
      <Container fluid>
        <Header>Gentilezas</Header>
        <Card.Group itemsPerRow={1}>
          {tasks.map(task => this.renderCard(task, loading))}
        </Card.Group>
      </Container>
    );
  }
}

export default TaskPage;
```

## Adicionando método de done na página de Gentilezas

arquivo `./src/components/Tasks/index.js`

```jsx
import React from "react";

import {
  Button,
  Card,
  Container,
  Header,
  Icon,
  Placeholder
} from "semantic-ui-react";

import api from "../../infrastructure/api";

class TaskPage extends React.Component {
  state = {
    loading: true,
    data: undefined
  };

  list2map = list => {
    const map = new Map();
    list.forEach(item => map.set(item.oid, item));
    return map;
  };
  componentDidMount = () => {
    api
      .get("tasks")
      .then(response => response.data)
      .then(list => this.list2map(list))
      .then(tasks => this.setState({ data: tasks, loading: false }));
  };

  handleDone = task => {
    const { data } = this.state;
    api
      .post(`tasks/${task.oid}/done`)
      .then(response => response.data)
      .then(json => {
        console.log("====================================");
        console.log(task);
        console.log("====================================");
        task.done = true;
        data.set(task.oid, task);
        this.setState({ data: data });
      });
  };

  renderHeader = task => {
    return (
      <Card.Header>
        {task.title + " "}
        <Button
          disabled={task.delete || task.done}
          circular
          icon="trash alternate outline"
        />
        <Button
          disabled={task.done}
          circular
          icon="thumbs up outline"
          onClick={this.handleDone.bind(this, task)}
        />
      </Card.Header>
    );
  };

  renderDescription = description => {
    return <Card.Meta>{description}</Card.Meta>;
  };

  renderDetails = task => (
    <Card.Description align="center">
      <Icon name="phone" circular={true} />
      <Icon name="plus" />
      <Icon name="users" circular={true} />
      <Icon name="arrow right" />
      <Icon name="dollar sign" circular={true} />
    </Card.Description>
  );

  renderCard = (task, loading) => {
    if (loading) {
      return (
        <Card>
          <Placeholder fluid>
            <Placeholder.Line length="medium" />
            <Placeholder.Line length="long" />
            <Placeholder.Line length="short" />
          </Placeholder>
        </Card>
      );
    } else {
      return (
        <Card key={task.oid}>
          <Card.Content>
            {this.renderHeader(task)}
            {this.renderDescription(task.description)}
            {this.renderDetails(task)}
          </Card.Content>
        </Card>
      );
    }
  };

  render() {
    const { data, loading } = this.state;
    const tasks = data !== undefined ? [...data].map(item => item[1]) : [];

    return (
      <Container fluid>
        <Header>Gentilezas</Header>
        <Card.Group itemsPerRow={1}>
          {tasks.map(task => this.renderCard(task, loading))}
        </Card.Group>
      </Container>
    );
  }
}

export default TaskPage;
```
