# React Native : Criando a aplicação <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Instalando e configurando as ferramentas](#instalando-e-configurando-as-ferramentas)
  - [Sugestão para o Microsoft Visual Studio Code](#sugest%c3%a3o-para-o-microsoft-visual-studio-code)
- [Criando e configurando o aplicativo Be Happy With Me Mobile](#criando-e-configurando-o-aplicativo-be-happy-with-me-mobile)
  - [Crie o aplicativo mobile](#crie-o-aplicativo-mobile)
  - [Conecte ao repositório remoto e realize o primeiro commit](#conecte-ao-reposit%c3%b3rio-remoto-e-realize-o-primeiro-commit)
  - [Configure o vscode para o projeto](#configure-o-vscode-para-o-projeto)
- [Executando o app no dispositivo](#executando-o-app-no-dispositivo)
  - [Após iniciar, fica assim a tela do terminal](#ap%c3%b3s-iniciar-fica-assim-a-tela-do-terminal)
  - [Após iniciar, o navegador padrão abre](#ap%c3%b3s-iniciar-o-navegador-padr%c3%a3o-abre)
  - [Modifique App.js](#modifique-appjs)
  - [Commit e push da modificação](#commit-e-push-da-modifica%c3%a7%c3%a3o)

## Instalando e configurando as ferramentas

**no smartphone**

- Instale o expo app, ver detalhes em https://expo.io/tools#client

**no computador do laboratório no linux**

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

nvm install node

npm install --global expo-cli eslint

```

### Sugestão para o Microsoft Visual Studio Code

Instalar os seguintes extensões:

- React Native Tools
- React-Native/React/Redux snippets for es6/es7
- Prettier
- JavaScript formatter
- ESLint
- vscode-icons
- Rainbow Brackets
- Color Highlight
- Auto Close Tag
- Auto Complete Tag
- Auto Rename Tag
- Settings Sync

## Criando e configurando o aplicativo Be Happy With Me Mobile

### Crie o aplicativo mobile

**primeira pergunta** pressione `enter` para selecionar `blank`

```console
machine user:dir$ expo-cli init behappywithme
? Choose a template: (Use arrow keys)
  ----- Managed workflow -----
❯ blank                 a minimal app as clean as an empty canvas
  blank (TypeScript)    same as blank but with TypeScript configuration
  tabs                  several example screens and tabs using react-navigation
  ----- Bare workflow -----
  minimal               bare and minimal, just the essentials to get you started
  minimal (TypeScript)  same as minimal but with TypeScript configuration

```

**segunda pergunta** digite `BeHappyWithMe` e pressione `enter` para definir nome do app

```console
machine user:dir$ expo-cli init behappywithme
? Choose a template: expo-template-blank
? Please enter a few initial configuration values.
  Read more: https://docs.expo.io/versions/latest/workflow/configuration/ › 50% completed
 {
   "expo": {
     "name": "BeHappyWithMe",
     "slug": "behappywithme"
   }
 }


```

**terceira pergunta** pressione `enter` para selecionar `Y` e confirmar o uso do Yarn para instalar dependências

```console
leo:2019-src$ expo-cli init behappywithme
? Choose a template: expo-template-blank
✔ Please enter a few initial configuration values.
  Read more: https://docs.expo.io/versions/latest/workflow/configuration/ · 100% completed
? Yarn v1.13.0 found. Use Yarn to install dependencies? (Y/n)

```

**resultado final**

```console
machine user:dir$ expo-cli init behappywithme
? Choose a template: expo-template-blank
✔ Please enter a few initial configuration values.
  Read more: https://docs.expo.io/versions/latest/workflow/configuration/ · 100% completed
? Yarn v1.13.0 found. Use Yarn to install dependencies? Yes
Extracting project files...
Customizing project...
Initialized a git repository.
Installing dependencies...
yarn install v1.13.0
info No lockfile found.
[1/4] 🔍  Resolving packages...
warning expo > fbemitter > fbjs > core-js@1.2.7: core-js@<2.6.8 is no longer maintained. Please, upgrade to core-js@3 or at least to actual version of core-js@2.
warning react-native-web > deep-assign@3.0.0: Check out `lodash.merge` or `merge-options` instead.
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "react-native > metro-react-native-babel-transformer@0.51.0" has unmet peer dependency "@babel/core@*".
warning "react-native > @react-native-community/cli > metro-react-native-babel-transformer@0.51.1" has unmet peer dependency "@babel/core@*".
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
warning Your current version of Yarn is out of date. The latest version is "1.19.1", while you're on "1.13.0".
info To upgrade, run the following command:
$ curl --compressed -o- -L https://yarnpkg.com/install.sh | bash
✨  Done in 70.24s.

Your project is ready at /Users/leo/src/disciplinas/TII-POS/2019-src/behappywithme

To get started, you can type:

  cd behappywithme
  yarn start
```

**modifique o nome do diretório**

```
machine user:dir$ mv -v behappywithme 2019-behappy-mobile && cd $_
behappywithme -> 2019-behappy-mobile

machine user:2019-behappy-mobile$
```

### Conecte ao repositório remoto e realize o primeiro commit

**fork do repositório remoto**

[mobile](https://github.com/tiipos/2019-behappy-mobile/)

**adicione o repositório remoto**

```console
machine user:2019-behappy-mobile$ git remote add origin https://github.com/leonardo-minora/2019-behappy-mobile.git

machine user:2019-behappy-mobile$ git pull origin master
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 9 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (9/9), done.
From https://github.com/leonardo-minora/2019-behappy-mobile
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master

machine user:2019-behappy-mobile$ git branch --set-upstream-to=origin/master master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

**edite o arquivo** `.gitignore`

```gitignore
# expo
.expo/

# node
/node_modules
package-lock.json

# env files
.env.local
.env.development.local
.env.test.local
.env.production.local

# npm and yarn files
npm-debug.log*
yarn-debug.log*
yarn-error.log*
yarn.lock

# OS X
.DS_Store

# Watchman
.watchmanconfig

# misc
*.jks
*.p8
*.p12
*.key
*.mobileprovision
*.orig.*
web-build/
web-report/
```

**configure o usuário**

```console
machine user:2019-behappy-mobile$ git config user.name "Leonardo Minora"

machine user:2019-behappy-mobile$ git config user.email "leonardo.minora@gmail.com"
```

**faça o commit e push**

```console
machine user:2019-behappy-mobile$ git add .

machine user:2019-behappy-mobile$ git commit -m "app created"
[master b1a85cf] app created
 8 files changed, 114 insertions(+)
 create mode 100644 .expo-shared/assets.json
 create mode 100644 .gitignore
 create mode 100644 App.js
 create mode 100644 app.json
 create mode 100644 assets/icon.png
 create mode 100644 assets/splash.png
 create mode 100644 babel.config.js
 create mode 100644 package.json

machine user:2019-behappy-mobile$ git push
Enumerating objects: 13, done.
Counting objects: 100% (13/13), done.
Delta compression using up to 4 threads
Compressing objects: 100% (11/11), done.
Writing objects: 100% (12/12), 6.71 KiB | 3.36 MiB/s, done.
Total 12 (delta 0), reused 0 (delta 0)
To https://github.com/leonardo-minora/2019-behappy-mobile.git
   85e26cd..b1a85cf  master -> master

machine user:2019-behappy-mobile$

```

### Configure o vscode para o projeto

```bash
mkdir .vscode
touch .vscode/settings.json
```

**edite o arquivo** `[dir do projeto]/.vscode/settings.json`

```json
{
  "prettier.eslintIntegration": true,
  "editor.formatOnSave": true
}
```

## Executando o app no dispositivo

```console
machine user:2019-behappy-mobile$  yarn start
yarn run v1.13.0
$ expo start
Starting project at .../2019-behappy-mobile
Expo DevTools is running at http://localhost:19002
Opening DevTools in the browser... (press shift-d to disable)
Starting Metro Bundler on port 19001.

  exp://10.24.22.127:19000

  To run the app with live reloading, choose one of:
  • Scan the QR code above with the Expo app (Android) or the Camera app (iOS).
  • Press a for Android emulator, or i for iOS simulator.
  • Press e to send a link to your phone with email.
  • Press s to sign in and enable more options.

 Expo  Press ? to show a list of all available commands.
Logs for your project will appear below. Press Ctrl+C to exit.
Tunnel ready.


```

### Após iniciar, fica assim a tela do terminal

![Expo iniciado no terminal](./terminal.png)

### Após iniciar, o navegador padrão abre

![Expo iniciado no browser](./browser.png)

### Modifique App.js

crie o diretório e arquivo `./src/index.js`

```jsx
import React from "react";
import { StyleSheet, Text, View } from "react-native";

export default function App() {
  return (
    <View style={styles.container}>
      <Text>Meu primeiro app!</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#fff",
    alignItems: "center",
    justifyContent: "center"
  }
});
```

modifique o arquivo `./App.js`

```js
import App from "./src/index";

export default App;
```

**o app atualiza automaticamente**

```console
  To run the app with live reloading, choose one of:
  • Scan the QR code above with the Expo app (Android) or the Camera app (iOS).
  • Press a for Android emulator, or i for iOS simulator.
  • Press e to send a link to your phone with email.
  • Press s to sign in and enable more options.

 Expo  Press ? to show a list of all available commands.
Logs for your project will appear below. Press Ctrl+C to exit.
Tunnel ready.
Finished building JavaScript bundle in 3277ms.
Running application on iPhone de Leonardo.
Finished building JavaScript bundle in 967ms.
Running application on iPhone de Leonardo.
Finished building JavaScript bundle in 178ms.
Running application on iPhone de Leonardo.
Finished building JavaScript bundle in 56ms.
Running application on iPhone de Leonardo.
Finished building JavaScript bundle in 1179ms.
Running application on iPhone de Leonardo.

```

### Commit e push da modificação

```console
machine user:2019-behappy-mobile$ git add -v .
add 'App.js'
add 'src/index.js'

machine user:2019-behappy-mobile$ git commit -vm "First app update"
[master d9abd32] First app update
 2 files changed, 5 insertions(+), 20 deletions(-)
 rewrite App.js (99%)
 copy App.js => src/index.js (84%)

machine user:2019-behappy-mobile$ git push
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (5/5), 654 bytes | 654.00 KiB/s, done.
Total 5 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/leonardo-minora/2019-behappy-mobile.git
   b1a85cf..d9abd32  master -> master

machine user:2019-behappy-mobile$
```
