# [](#header-1) React App - Armazenamento no browser

## [](#header-2) Objetivos

- ## React
- BeHappy.Me
  - Criar o serviço: Repository
  - Modificar o componente: App
  - Modificar o domínio: User

## [](#header-2) Sumário

1. [Preparando o ambinente](#-1-preparando-o-ambiente)
2. [Adicionar o serviço de repositório](#-2-adicionar-o-serviço-de-repositório)
3. [Modificar o domínio User](#-3-modificar-o-domínio-user)
4. [Modificar o componente App](#-4-modificar-o-componente-app)
5. [Registrar as mudanças no repositório do github](#-5-registrar-as-mudanças-no-repositório-do-github)
6. [Publicar no Heroku](#-6-publicando-no-heroku)
7. [Homework](#-7-homework)

### [](#header-2) 1. Preparando o ambiente

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

### [](#header-2) 2. Adicionar o serviço de repositório

1. Discussão inicial sobre repositórios de navegadores
2. Criar diretório para o serviço repositório
3. Criar e editar a classe do serviço de repositório

#### [](#header-4) 2.1. Discussão inicial sobre repositórios de navegadores

- Mecanismos de armazenamento de dados
  1. Web SQL database ou WebSQL
  - Ferramenta: [Sqlite](https://www.sqlite.org/) embarcado no navegador
  - Vantagem: aceitar comandos SQL (select, update, insert, etc)
  - Desvantagesn: incompatibilidade nos navegadores, [padronização W3C](https://www.w3.org/TR/webdatabase/) interrompida
  2. Indexed database ou IndexedDB
  - Características: NoSQL, armazena grandes quantidades de dados estruturados, API Assíncrona, [Recomendação W3C](https://www.w3.org/TR/IndexedDB-2/)
  - Desvantagem: API complexa, [compatibilidade de 87% com os navegadores](https://caniuse.com/#search=indexed)
  3. Web storage
  - Características: NoSQL, [Especificação W3C](https://www.w3.org/TR/webstorage/), comunidade "participativa" e "forte", [compatibilidade de 95% com os navegadores](https://caniuse.com/#search=storage), API "amigável" e "simplificada"
  - Desvantagens: comportamento síncrono ([I/O Bloqueante](https://pt.stackoverflow.com/questions/122616/diferença-entre-uma-linguagem-bloqueante-e-uma-linguagem-não-bloqueante)), espaço de armanazemento máximo de 5MB
  - 2 formas de armazenamento:
    - Sessão do navegador (`sessionStorage`): apaga as informações ao perder a sessão ou fechar o navegador
    - Permanente (`localStorage`): mantém dados após fechar navegador

#### [](#header-4) 2.2. Criar diretório para o serviço repositório

**em um outro terminal**

```sh
### Tudo que iniciar com $ deverá ser substituído
### pelo valor correspondente
cd $DIRETORIO_APP

mkdir src/infrastructure
touch src/infrastructure/Repository.js
```

#### [](#header-4) 2.3. Criar e editar a classe do serviço de repositório

**src/infrastructure/Repository.js**

```js
class Repository {
  constructor() {
    this.key = "behappy-user";
  }

  save = (json, callback) => {
    let data = JSON.stringify(json);
    window.localStorage.setItem(this.key, data);
    callback();
  };

  load = (success, fail) => {
    let data = window.localStorage.getItem(this.key);
    let json = JSON.parse(data);
    if (json) {
      success(json);
    } else {
      fail(json);
    }
  };
}

export default Repository;
```

### [](#header-2) 3. Modificar o domínio User

editar **src/models/User.js**

```js
import Avatar from "./Avatar";

import Repository from "../infrastructure/Repository";

const repository = new Repository();

class User {
  constructor() {
    this.name = "";
    this.gender = "";
    this.avatar = Avatar.getAll()[0];
  }

  validName = () => {
    return (
      typeof this.name === "string" &&
      this.name.length !== 0 &&
      this.name.length <= 40
    );
  };

  validGender = () => {
    return ["m", "f"].some(param => {
      return this.gender === param;
    });
  };

  save = callback => {
    repository.save(this, callback);
  };

  static load = (success, fail) => {
    repository.load(json => {
      let user = new User();
      user.name = json.name;
      user.gender = json.gender;
      user.avatar = new Avatar(json.avatar.index, json.avatar.description);
      success(user);
    }, fail);
  };

  toString() {
    return `${this.name}, ${this.avatar.toString()}`;
  }
}

export default User;
```

### [](#header-2) 4. Modificar o componente App

entendendo a nova versão de App.js, **sequenciando os passos**:

1. A aplicação iniciará com uma busca no localStorage.
2. Caso nada seja encontrado, o estado de App permanecerá vazio (`undefined`) e o componente NovoUsuario será exibido.
3. Ao final do cadastro, App salvará os dados no localStorage e em seu estado (`renderNewUser, user.save`).
4. Como o método `setState()` implica em uma nova renderização, NovoUsuario deixará de ser exibido, dando lugar a uma simples `<div>` com os dados do usuário cadastro.
5. No futuro, quando o navegador for reaberto, App encontrará os dados em localStorage e continuará sem exibir NovoUsuario.

**src/App.js**

```jsx
import React, { Component } from "react";

import "./App.css";

import Header from "./components/Header";
import NewUser from "./components/NewUser";
import Toast from "./components/Toast";

import User from "./models/User";

class App extends Component {
  constructor(props) {
    super(props);
    User.load(
      user => {
        this.state = { user: user };
      },
      () => {
        this.state = { user: undefined };
      }
    );
  }

  messageToNewUser(user) {
    let gender = user.gender === "m" ? "o" : "a";
    this.refs.toast.success(`Seja bem vind${gender} ${user.name}!`);
  }

  renderNewUser() {
    let user = this.state.user;
    if (user) {
      let style = {
        marginTop: "140px",
        textAlign: "center"
      };

      return (
        <div style={style}>
          <b>
            Usuário obtido do <i>localStorage</i>
          </b>
          <br />
          {user.toString()}
        </div>
      );
    } else {
      return (
        <NewUser
          onSubmit={user => {
            user.save(() => {
              this.setState(
                {
                  user: user
                },
                () => {
                  this.messageToNewUser(user);
                }
              );
            });
          }}
          error={msg => this.refs.toast.error(msg)}
        />
      );
    }
  }

  render() {
    return (
      <div>
        <Header />
        {this.renderNewUser()}
        <Toast ref="toast" />
      </div>
    );
  }
}

export default App;
```

### [](#header-2) 5. Registrar as mudanças no repositório do github

1. Fechar servidor web `CTRL + C`
2. Adicioanar novos e modificados arquivos (`git add`) no repositório local
3. Registrar mudanças (`git commit`) no repositório local
4. Publicar mudanças no repositório remoto (`git pull` e `git push`)

```sh
# Fechar o servidor web
### 1. Pressionar CTRL+C
###

### 2. Adicionar arquivos novos e modificados no repositório local
git add src

### 3. Registrar mudanças no repositório local
git commit -m "$MSG"

### Publicar as mudanças no repositório remoto
git pull
git push
```

### [](#header-2) 6. Publicando no Heroku

1. Acessar o [Heroku](https://www.heroku.com) e se autenticar
2. Acessar o link do seu _App_ no Heroku
3. No _dashboard_ do seu _App_, clicar na aba _Deploy_
4. Em _Deployment method_, selecionar GiHub
5. Definir qual o repositório esta o _App_
6. Em _Manual deploy_, clicar em _Deploy Branch_
7. Aguardar um tempo, olhando o log
8. No topo da página, clicar em _Open App_

### [](#header-2) 7. Homework

**Just do it!!!**
