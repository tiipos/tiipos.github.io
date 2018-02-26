# [](#header-1) React Native Introdução

## [](#header-2) Ferramentas

- vscode
- Node, npm, yarn, React Native funcionam em qualquer ambiente
- Expo funciona com simulador e com o dispositivo (iOS e Android)

## [](#header-2) Instalação nos laboratórios do IFRN

```sh
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
nvm install lts/carbon
npm i -g create-react-native-app yarn
```

## [](#header-2) Criar um app React Native

1. instale o react native ```yarn global add create-react-native-app```
2. crie projeto inicial ```create-react-native-app CurrencyConverter```
3. acesse diretório do projeto inicial ```cd CurrencyConverter```
4. inicie a aplicação ```yarn start```


## [](#header-2) Executar o app no dispositivo

1. conecte o dispositivo na mesma rede do computador
2. execute o expo.io no dispositivo
3. acesse **Scan QR code**


## [](#header-2) Publicando no GitHub

1. Crie uma conta no github
2. Crie um repositório no github
3. Conecte o diretório da aplicação ao repositório
   - ```git remote add origin https://github.com/tiipos/CurrencyConverter.git```
4. Sincronize o diretório local com o github
   - ```git pull origin master```
5. Crie e adicione um arquivo _.gitignore_
   - edite o arquivo .gitignore (ver arquivo no final da página)
   - ```git add .gitignore```
   - ```git commit -m "configurando para ignorar arquivos locais"```
6. Adicione os arquivos locais
   - ```git add *```
   - ```git commit -m "publicando o projeto padrão inicial"```
7. Publique tudo no github
   - ```git push origin master```


## [](#header-2) Configurando o editor de código

**git** configurando o registro da mudança
1. crie um branch ```git checkout -b 01-vscode-config```

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
3. publique o branch no github ```git push origin 01-vscode-config```


## [](#header-2) Organização do projeto

**git** configurando o registro da mudança
1. crie um branch ```git checkout -b 01-project-structure```

**a organizção final será essa**
- App.js
- app
  - index.js
  - components
  - config
  - screens

**passos**
1. crie os diretórios _app_, _app/config_, _app/components_, _app/screens_
   - ```mkdir -p app/config```
   - ```mkdir app/components```
   - ```mkdir app/screens```
2. copie o arquivo _App.js_ para _app/index.js_ 
   - ```cp App.js app/index.js```
3. edite o arquivo _App.js_ (ver código no final da página)
4. reinicie o aplicativo, digitando _r_ no terminal

**git** registrando de fato a mudança
1. adicione os arquivos ao repositório
   - ```git add App.js```
   - ```git add app/index.js```
2. efetue o commit ```git commit -m "estruturando os diretórios do projeto"```
3. publique o branch no github ```git push origin 01-project-structure```


## [](#header-2) Monitorando o código do projeto

**git** configurando o registro da mudança
1. crie um branch ```git checkout -b 01-eslint```

**eslint** configurando
1. adicione o aplicativo _eslint_ ```yarn global add eslint```
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
1. adicione os arquivos ao repositório
   - ```git add .eslintrc.json package.json```
2. efetue o commit ```git commit -m "adicionando o eslint ao projeto"```
3. publique o branch no github ```git push origin 01-eslint```


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


# [](#header-2) arquivos

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
# See https://help.github.com/ignore-files/ for more about ignoring files.

# expo
.expo/

# dependencies
/node_modules

# misc
.env.local
.env.development.local
.env.test.local
.env.production.local

npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Xcode
!**/*.xcodeproj
!**/*.pbxproj
!**/*.xcworkspacedata
!**/*.xcsettings
!**/*.xcscheme
*.pbxuser
!default.pbxuser
*.mode1v3
!default.mode1v3
*.mode2v3
!default.mode2v3
*.perspectivev3
!default.perspectivev3
xcuserdata
*.xccheckout
*.moved-aside
DerivedData
*.hmap
*.ipa
*.xcuserstate
project.xcworkspace

# Gradle
/build/
/RNTester/android/app/build/
/RNTester/android/app/gradle/
/RNTester/android/app/gradlew
/RNTester/android/app/gradlew.bat
/ReactAndroid/build/

# Buck
.buckd
buck-out
/ReactAndroid/src/main/jni/prebuilt/lib/armeabi-v7a/
/ReactAndroid/src/main/jni/prebuilt/lib/x86/
/ReactAndroid/src/main/gen

# Watchman
.watchmanconfig

# Android
.idea
.gradle
local.properties
*.iml
/android/

# Node
node_modules
*.log
.nvm
/bots/node_modules/

# TODO: Check in yarn.lock in open source. Right now we need to keep it out
# from the GitHub repo as importing it might conflict with internal workspaces
*/**/yarn.lock

# OS X
.DS_Store

# Test generated files
/ReactAndroid/src/androidTest/assets/AndroidTestBundle.js
*.js.meta

/coverage
/third-party
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
import React from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Alo mundo.</Text>
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

export default class App extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text>Alo mundo.</Text>
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


__App.js__
```js
import App from './app/index';

export default App;
```


__package.json__
```json
"scripts": {
    // ...
    "lint": "eslint app/",
    "lint:fix": "eslint app/ --fix"
}
```
