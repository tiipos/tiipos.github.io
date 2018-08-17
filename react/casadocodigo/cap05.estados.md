# [](#header-1) Capítulo 5 Componentes com estado e fluxo de eventos

https://github.com/lgapontes/behappywith.me/

## [](#header-2) Sumário

1. Estados dos componentes
2. Criando o componente Input
3. Criando o componente de selesção de gênero
4. Conclusão



## [](#header-2) 5. Introdução

- Criar os componentes: Input, GenderButton, GenderImage, GenderSelector
- Modificar os componentes: App, NewUser



## [](#header-2) 5.1. Estados dos componentes

- Componentes React têm _propriedades_ (props) e _estado_ (state)
- Estado, valores do:
  - Expressos como objeto javascript ```{attr1: value, attr2: value}```
  - Inicialização no método ```constructor```
  - Modificação no método ```setState```


```jsx
render() {
  return (
    <h1>Eu sou um código JSX!</h1>
  );
}
```

```jsx
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      cor: white,
      contador: 0
    };
  }

  trocarCor(e) {
    let contador = this.state.contador;
    let cor = (++contador % 2) == 0 ? 'red': 'blue';
    this.setState({
      cor: cor,
      contador: contador
    });
  }

  render() {
    const estilo = {
      padding: 10,
      background: this.state.cor,
    }
    return (
      <h1 style={estilo} onClick={this.trocarCor.bind(this)}>
        {this.props.texto}
      </h1>
    );
  }
}
```

```
ReactDOM.render(
  <App texto="Olá mundo!" />,
  document.querySelector("#exemplo");
)
```


### JavaScript

**IO não bloqueante** (programação assíncrona e callback)

```js
function funcaoNaoBloqueante(par, callback) {
  // API assíncrona de consulta ao servidor
  // Cria uma variável resultado
  callback(resultado);
}

let texto1 = funcaoBloqueante();
let texto2 = funcaoNaoBloqueante(texto1, function(variavel_resultado) { codigo });
funcaoBloqueante(texto2);
```

**Arrow functions**

```js
function a(par) {
  // código
}

const b = function(par) {
  // código
}

// ARROW Function
const c = (par) => {
  // código
}
```

**Spread Operator** ```...```
```jsx
export default function Input(props) {
  return (
    <input
      type="text"
      id={props.id}
      placeholder={props.placeholder}
      maxLength={props.maxLength}
    />
  ); 
}

export default function Input(props) {
  return (
    <input
      type="text"
      {...props}
    />
  ); 
}
```

**Object.assign()**
```jsx
let aluno = {
  nome: 'Leonardo Minora',
  email: 'leonardo.minora@ifrn.edu.br'
};
console.log(aluno);
//{ nome: 'Leonardo Minora',
//  email: 'leonardo.minora@ifrn.edu.br' }

Object.assign({matricula: '22958'}, aluno);
// { matricula: '22958',
//   nome: 'Leonardo Minora',
//   email: 'leonardo.minora@ifrn.edu.br' }
```

**template string**
```js
'auto' + 2 * 3 * (-1) + 'px'

`auto ${2 * 3 * (-1)}px`
```


## [](#header-2) 5.2. Criando o componente Input

```sh
git config --global --replace-all user.name "[nome]"
git config --global --replace-all user.email "[email]"

cd [diretório da aplicação web]
pnpm install
pnpm start

mkdir src/components/Input
touch src/components/Input/index.js
touch src/components/Input/Input.js

mkdir src/components/Image
touch src/components/Image/index.js
touch src/components/Image/Image.js

mkdir public/img
curl [url] --output public/img/avatars.png

mkdir src/components/GenderImage
touch src/components/GenderImage/index.js
touch src/components/GenderImage/GenderImage.js

mkdir src/components/GenderButton
touch src/components/GenderButton/index.js
touch src/components/GenderButton/index.css
touch src/components/GenderButton/GenderButton.js

mkdir src/components/GenderSelector
touch src/components/GenderSelector/index.js
touch src/components/GenderSelector/GenderSelector.js

```

**Características**
1. Um mecanismo para que os outros componentes obtenham o valor digitado;
2. Um atributo identificar;
3. Um valor de placeholder;
4. Um tamanho máximo de caracteres;
5. Um indicador para mostrar se é _read-only_ ou não;
6. Um indicador de que está ou não corretamente preenchido
7. Um valor padrão.

**O valor do estado de Input** será guardado por **NewUser**

### 1a versão
**Input.js**
```jsx
import React, {Component} from 'react';

export default function Input(props) {
  const style = {
    borderColor: props.invalidValue ? '#d50000' : '#cccccc',
    backgroundColor: props.invalidValue ? '#ffcdd2' : '#ffffff'
  }
  let properties = Object.assign({}, props);
  delete properties.invalidValue;

  return (
    <input
      type="text"
      style={style}
      {...properties}
    />
  ); 
}
```

**NewUser.js**
```jsx
import React, {Component} from 'react';

import Label from '../Label';
import Input from '../Input';

class NewUser extends Component {
  render() {
    return (
      <div className="center">
        <form className="pure-form pure-form-stacked">
          <Label htmlFor="name" text="Quem é você?" />
          <Input 
              id="name" 
              placeholder="Digite seu nome" 
              maxLength="40" readOnly invalidValue 
              defaultValue="Minora" />
        </form>
      </div>
    );
  }
}

export default NewUser;
```

2a versão **NewUser.js**
```jsx
import React, {Component} from 'react';

import Label from '../Label';
import Input from '../Input';

class NewUser extends Component {
  constructor(props) {
    super(props);
    this.state = {
      invalidName: true
    };
  }

  render() {
    return (
      <div className="center">
        <form className="pure-form pure-form-stacked">
          <Label 
              htmlFor="name" 
              text="Quem é você?" 
              invalidValue={this.state.invalidName} />
          <Input 
              id="name" 
              placeholder="Digite seu nome" 
              maxLength="40" 
              readOnly={false}
              invalidValue={this.state.invalidName}
              defaultValue="Minora" />
        </form>
      </div>
    );
  }
}

export default NewUser;
```

3a versão **NewUser.js**
```jsx
import React, {Component} from 'react';

import Label from '../Label';
import Input from '../Input';

class NewUser extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: {
        name: 'Minora'
      },
      validation: {
        invalidName: false
      }
    };
  }

  updateUserName = (event) => {
    let user = this.state.user;
    user.name = event.target.value;
    this.setState({
      user: user
    });
  }

  render() {
    return (
      <div className="center">
        <form className="pure-form pure-form-stacked">
          <Label 
              htmlFor="name" 
              text="Quem é você?" 
              invalidValue={this.state.validation.invalidName} />
          <Input 
              id="name" 
              placeholder="Digite seu nome" 
              maxLength="40" 
              readOnly={false}
              invalidValue={this.state.validation.invalidName}
              defaultValue={this.state.user.name}
              onChange={this.updateUserName} />
        </form>
      </div>
    );
  }
}

export default NewUser;
```

## [](#header-2) 5.3. Criando o componente de selesção de gênero

**lógica**
1. Um mecanismo para que os outros componentes obtenham o valor digitado;
2. Um gênero selionado;
3. Um indicador de que está ou não corretamente preenchido;
4. Um indicador de seleção de gênero selecionado.

**Sprites**

### Image.js
**Image.js**
```jsx
import React, {Component} from 'react';

class Image extends Component {
  // constructor(props) {
  //   super(props);
  // }

  calcPositionX = () => {
    return this.props.x * this.props.width * (-1) + 'px';
  }

  calcPositionY = () => {
    return `${this.props.y * this.props.height * (-1)}px`;
  }

  calcSize = () => `auto ${this.props.backgroundHeight}px`

  calcStyle = () => {
    return {
      backgroundImage: `url(${this.props.file})`,
      backgroundPositionX: this.calcPositionX(),
      backgroundPositionY: this.calcPositionY(),
      width: `${this.props.width}px`,
      height: `${this.props.height}px`,
      display: 'table',
      margin: '0 auto'
    };
  }

  render() {
    return (
      <div style={this.calcStyle()}></div>
    );
  }
}

export default Image;
```

**GenderImage.js**
```jsx
```

**NewUser.js**
```jsx
import React, {Component} from 'react';

import Label from '../Label';
import Input from '../Input';
import GenderImage from '../GenderImage';

class NewUser extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: {
        name: 'Minora'
      },
      validation: {
        invalidName: false
      }
    };
  }

  updateUserName = (event) => {
    let user = this.state.user;
    user.name = event.target.value;
    this.setState({
      user: user
    });
  }

  render() {
    return (
      <div className="center">
        <form className="pure-form pure-form-stacked">
          <Label 
              htmlFor="name" 
              text="Quem é você?" 
              invalidValue={this.state.validation.invalidName} />
          <Input 
              id="name" 
              placeholder="Digite seu nome" 
              maxLength="40" 
              readOnly={false}
              invalidValue={this.state.validation.invalidName}
              defaultValue={this.state.user.name}
              onChange={this.updateUserName}
          />
          <GenderImage gender="m" />
          <GenderImage gender="f" />
        </form>
      </div>
    );
  }
}

export default NewUser;
```

**GenderButton.js** props: selected, updateGender, gender
```jsx
import React from 'react';

import './index.css';
import GenderImage from '../GenderImage';

export default function GenderButton(props) {
  return (
    <a 
      className={props.selected ? 
        "gender-button selected-gender-button" : 
        "gender-button"
      }
      href="#!"
      onClick={event => props.updateGender(event, props.gender)}
    >
      <GenderImage gender={props.gender} />
    </a>
  );
}
```

**index.css**
```css
a.gender-button {
  box-sizing: border-box;
  width: 180px;
  height: 180px;
  border-radius: 10px;
  margin: 3px;
  padding-top: 5px;
  display: inline-block;
}

a.gender-button:hover{
  background-color: #dbdbdb;
}

a.selected-gender-button {
  background-color: #00C853;
}

a.selected-gender-button:hover {
  background-color: #00C853;
}
```


**GenderSelector.js** invalidValue, gender, updateGender
```jsx
import React from 'react';

import GenderButton from '../GenderButton';

export default function GenderSelector(props) {
  const male = props.gender === "m";
  const female = props.gender === "f";
  const cor = props.invalidValue ? '#d50000' : '#cccccc';
  const style = {
    boxSizing: 'border-box',
    border: `1px solid ${cor}`,
    borderRadius: '5px',
    padding: '3px',
    paddingBottom: '0'
  };
 
  return (
    <div style={style}>
      <GenderButton
        selected={female}
        gender={'f'}
        updateGender={props.updateGender}
      />
      <GenderButton
        selected={male}
        gender={'m'}
        updateGender={props.updateGender}
      />
    </div>
  );
}
```

**NewUser.js**
```jsx
import React, {Component} from 'react';

import Label from '../Label';
import Input from '../Input';
import GenderSelector from '../GenderSelector';

class NewUser extends Component {
  constructor(props) {
    super(props);
    this.state = {
      user: {
        name: '',
        gender: ''
      },
      validation: {
        invalidName: false,
        invalidGender: false
      }
    };
  }

  updateUserName = (event) => {
    let user = this.state.user;
    user.name = event.target.value;
    this.setState({
      user: user
    });
  }

  updateUserGender = (event, gender) => {
    event.preventDefault();
    let user = this.state.user;
    user.gender = gender;
    this.setState({
      user: user
    });
  }

  render() {
    return (
      <div className="center">
        <form className="pure-form pure-form-stacked">
          <Label 
              htmlFor="name" 
              text="Quem é você?" 
              invalidValue={this.state.validation.invalidName} />
          <Input 
              id="name" 
              placeholder="Digite seu nome" 
              maxLength="40" 
              readOnly={false}
              invalidValue={this.state.validation.invalidName}
              defaultValue={this.state.user.name}
              onChange={this.updateUserName}
          />
          <Label
            text="Seu gênero:"
            invalidValue={this.state.validation.invalidGender}
          />
          <GenderSelector
            invalidValue={this.state.validation.invalidGender}
            gender={this.state.user.gender}
            updateGender={this.updateUserGender}
          />
        </form>
      </div>
    );
  }
}

export default NewUser;
```


## [](#header-2) 5.4. Conclusão

