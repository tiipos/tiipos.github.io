# [](#header-1) React Native Introdução



## [](#header-2) Ferramentas

**desktop**
- vscode
- Node, npm, yarn, React Native funcionam em qualquer ambiente
- Expo funciona com simulador e com o dispositivo (iOS e Android)

**online**
- [snack.expo](https://snack.expo.io/)



## [](#header-2) Sumário

1. Instale as ferramentas nos laboratórios da DIATINF
2. Crie o aplicativo CurrencyConverter
3. Execute no dispositivo o aplicativo CurrencyConverter
4. Inicialize o repositório local
5. Conecte e publicar em um repositório remoto
6. Configure o editor de código
7. Organize os arquivos e diretórios do projeto
8. Monitore o código do projeto

**códigos-fonte**
- github
- [snack.expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-01-app)



## [](#header-2) Passo a passo

### [](#header-3) Instale as ferramentas nos laboratórios da DIATINF

```sh
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
nvm install lts/carbon
npm i -g create-react-native-app yarn
```

### [](#header-3) Crie o aplicativo CurrencyConverter

1. crie projeto inicial ```create-react-native-app CurrencyConverter```
2. acesse diretório do projeto inicial ```cd CurrencyConverter```
3. inicie a aplicação ```yarn start```


### [](#header-3) Execute no dispositivo o aplicativo CurrencyConverter

1. conecte o dispositivo na mesma rede do computador
2. execute o expo.io no dispositivo
3. acesse **Scan QR code**


### [](#header-3) Inicializar o repositório local

1. Inicialize o versionamento ```git init```
2. Modifique o arquivo ```.gitignore``` (ver arquivo no final da página)
3. Marque os arquivos para versionar ```git add '*'```
4. Registre o versionamento ```git commit -m "Primeira versão do app CurrencyConverter"```

### [](#header-2) Conecte e publique em um repositório remoto

1. Conecte o diretório da aplicação ao repositório
   - ```git remote add origin https://github.com/tiipos/CurrencyConverter.git```
2. Copie o versionamento do repositório remoto (github) para o respositório local (diretório local)
   - ```git pull origin master```
3. Publique o versionamento do repositório local (diretório local) para o repositório remoto (github)
   - ```git push origin master```

**observação**: 
- é possível que ao executar ```git pull```, seja sugerido pelo _git_ realizar a mesclagem (_merge_).
- quando isso ocorre, o _git_ não conseguiu fazer a mesclagem automática e o desenvolvedor deverá fazer manualmente.
- após fazer a mesclagem manual, o desenvolvedor deverá marcar os arquivos para versionamento (```git add```) e depois registrar o versionamento (```git commit```).

### [](#header-3) Configure o editor de código

**vscode** configurando
1. instale os segintes plugins no **vscode**
   - React Native Tools
   - React-Native/React/Redux snippets for es6/es7
   - Prettier - JavaScript formatter
   - ESLint
   - vscode-icons
   - Rainbow Brackets
   - Color Highlight
   - Auto Close Tag
   - Auto Complete Tag
   - Auto Rename Tag
   - Settings Sync
2. configure o vscode para o projeto (ver arquivo _.vscode/settings.json_ no final da página)
3. reinicie o vscode

**git** registrando de fato a mudança
1. adicione o arquivo de cofiguração ```git add .vscode/settings.json```
2. efetue o commit ```git commit -m "vscode configurado para projetos react native"```
3. publique o branch no github ```git push origin master```


### [](#header-3) Organize os arquivos e diretórios do projeto

**a organizção final será essa**
- App.js
- app
  - index.js

**passos**
1. crie os diretórios _app_ ```mkdir app/```
2. copie o arquivo _App.js_ para _app/index.js_  ```cp App.js app/index.js```
3. edite o arquivo _App.js_ (ver código no final da página)
4. reinicie o aplicativo, digitando _r_ no terminal
5. modifique o arquivo _app/index.js_ (ver a v1 do arquivo no final da página)

**git** registre o versionamento
1. adicione os arquivos ao repositório ```git add App.js app/index.js```
2. efetue o commit ```git commit -m "estruturando os diretórios do projeto"```
3. publique o branch no github ```git push origin master```


### [](#header-3) Monitore o código do projeto

**eslint** configurando
1. adicione o aplicativo _eslint_ ```npm i -g eslint```
2. execute ```eslint --init```, respondendo as perguntas da seguinte forma:
   - __Use a popular style guide__
   - __Airbnb__
   - Do you use React? __y__
   - What format do you want your config file to be in? __JSON__
   - _Observação_ o aplicativo irá quebrar :-/ mas não se preocupe ;-)
3. edite o arquivo _.eslintrc.json_ (ver arquivo no final da página)
4. instale as bibliotecas
   - ```yarn --dev add babel-eslint eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-react prettier prettier-eslint```
5. reinicie o aplicativo, digitando _r_ no terminal
6. adicione os scripts _lint_ e _lint:fix_ no arquivo ```package.json``` (ver scripts no final desta página)

**git** registrando de fato a mudança
1. adicione os arquivos ao repositório ```git add .eslintrc.json package.json```
2. efetue o commit ```git commit -m "adicionando o eslint ao projeto"```
3. publique o branch no github ```git push origin master```

**modificando app/index.js** o eslint irá avisar que tem alguns erros e warnings
1. modifique o arquivo _app/index.js_ (ver v2 do arquivo no final da página)
2. adicione os arquivos ao repositório ```git add app/index.js```
3. efetue o commit ```git commit -m "corrigindo app/index com os erros encontrados por eslint"```
4. publique o branch no github ```git push origin master```

## [](#header-2) Como debug um projeto

- Usando o Expo no dispositivo
  - Shake device
  - Pressione "Debug Remote JS"
- Simulador
  - iOS
    - Pressione ```cmd + opt + i```
    - Pressione ```Debug Remote JS```
  - Android
    - Pressione ```cmd + m``` ou ```ctrl + m```
    - Pressione ```Debug Remote JS```


## [](#header-2) arquivos fonte

__.eslintrc.json__
```json
{
  "extends": "airbnb", // EXISTING
  "parser": "babel-eslint",
  "env": {
    "browser": true
  },
  "plugins": [
    "react"
  ],
  "rules": {
    "react/jsx-filename-extension": [
      2,
      {
        "extensions": [
          ".js",
          ".jsx"
        ]
      }
    ],
    "react/forbid-prop-types": [
      0
    ],
    "react/require-default-props": [
      0
    ],
    "global-require": [
      0
    ]
  }
}
```


__.gitignore__
```
# expo
.expo/

# node
/node_modules
package-lock.json

# misc
.env.local
.env.development.local
.env.test.local
.env.production.local

npm-debug.log*
yarn-debug.log*
yarn-error.log*
yarn.lock

# OS X
.DS_Store

# Watchman
.watchmanconfig
```


__.vscode/settings.json__
```json
{
  "prettier.eslintIntegration": true,
  "editor.formatOnSave": true
}
```

__app/index.js__ v1
```js
import React, { Component } from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Meu primeiro App Mobile.</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```


__app/index.js__ v2
```js
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

export default () => (
  <View style={styles.container}>
    <Text>Meu primeiro App Mobile.</Text>
  </View>
);
```


__App.js__
```js
import App from './app/index';

export default App;
```


__package.json__
```json
{
  "name": "CurrencyConverter",
  "version": "0.0.1",
  "private": true,
  "devDependencies": {
    "babel-eslint": "^8.2.2",
    "eslint": "^4.19.1",
    "eslint-config-airbnb": "^16.1.0",
    "eslint-plugin-import": "^2.9.0",
    "eslint-plugin-jsx-a11y": "^6.0.3",
    "eslint-plugin-react": "^7.7.0",
    "jest-expo": "25.0.0",
    "prettier": "^1.11.1",
    "prettier-eslint": "^8.8.1",
    "react-native-scripts": "1.11.1",
    "react-test-renderer": "16.2.0"
  },
  "main": "./node_modules/react-native-scripts/build/bin/crna-entry.js",
  "scripts": {
    "start": "react-native-scripts start",
    "eject": "react-native-scripts eject",
    "android": "react-native-scripts android",
    "ios": "react-native-scripts ios",
    "test": "node node_modules/jest/bin/jest.js",
    "lint": "eslint app/",
    "lint:fix": "eslint app/ --fix"
  },
  "jest": {
    "preset": "jest-expo"
  },
  "dependencies": {
    "expo": "^25.0.0",
    "react": "16.2.0",
    "react-native": "0.52.0"
  }
}
```
