# React Native : Meu primeiro aplicativo

## Sobre a aula

-  **Objetivo**:

   1. Ter um primeiro contato com a tecnologia
   2. Criar o primeiro aplicativo web
   3. Publicar o aplicativo na internet
   4. Entender a dinâmica de trabalho

-  **Pré-requisitos**:

   -  [GitHub](https://github.com/) - Repositório de códigos-fonte
      1. Criar uma conta - _use seu melhor email_
   -  Baixar, instalar e configurar no computador o [NodeJs](https://nodejs.org/), versão corrente
   -  [Expo](https://expo.dev/)
      1. Criar uma conta _use seu melhor email_
      2. Instalar no celular o app Expo Go [Android](https://play.google.com/store/apps/details?id=host.exp.exponent&referrer=www) ou [iOS](https://apps.apple.com/app/apple-store/id982107779)
      3. Instalar o Expo Cli no computador `npm install --global expo-cli`
   -  Baixar e instalar no computador um editor de código-fonte, recomendo o [Microsoft Visual Studio Code](https://code.visualstudio.com)
   -  Baixar e instalar no computador um [cliente git](https://git-scm.com)

## Passo a passo

1. Fazer o login no [GitHub](https://github.com/)
2. Realize _fork_ do respositório [Tarefas Mobile App](https://github.com/tiipos/2021-TarefasMobile)
3. Abrir o terminal de comandos
4. Acessar o diretório para os projetos da disciplina, o nome recomendado foi _tarefas_
5. Clonar do repositório (git clone `url` , ex: `git clone https://github.com/leonardo-minora/2021-TarefasMobile`)
6. Criar uma aplicação React Native `expo-cli init mobile`
7. Executar a aplicação `cd mobile && yarn start`
8. Abrir o aplicativo do celular Expo Go
9. Abrir a câmera do celular e apontar para o QR Code do terminal ou do navegador
10.   Mover arquivos e diretórios de `mobile` para `2021-TarefasMobile`
      -  assets
      -  .gitignore
      -  App.js
      -  app.json
      -  babel.config.js
      -  package.json
11.   Comitar os arquivos `2021-TarefasMobile` (`git add . && git commit -m "my first react mobile app"`)
12.   Publicar no github (`git push origin master`)

## Saídas dos comandos

### expo-cli init tarefas

```console
$ expo-cli init mobile
? Choose a template: › - Use arrow-keys. Return to submit.
    ----- Managed workflow -----
❯   blank               a minimal app as clean as an empty canvas
    blank (TypeScript)  same as blank but with TypeScript configuration
    tabs (TypeScript)   several example screens and tabs using react-navigation and TypeScript
    ----- Bare workflow -----
    minimal             bare and minimal, just the essentials to get you started

```

```console
$ expo-cli init mobile
✔ Choose a template: › blank               a minimal app as clean as an empty canvas
  Downloading and extracting project files.
🧶 Using Yarn to install packages. Pass --npm to use npm instead.
  Installing JavaScript dependencies.

✅ Your project is ready!

To run your project, navigate to the directory and run one of the following yarn commands.

- cd mobile
- yarn start # you can open iOS, Android, or web from here, or run them directly with the commands below.
- yarn android
- yarn ios
- yarn web

```

### npm start

```console
$ cd mobile && npm start

> start
> expo start

Starting project at /Users/minora/Documents/src/react-native/tarefas
Developer tools running on http://localhost:19002
Opening developer tools in the browser...
Starting Metro Bundler





























 › Waiting on exp://192.168.0.98:19000
 › Scan the QR code above with Expo Go (Android) or the Camera app (iOS)

 › Press a │ open Android
 › Press i │ open iOS simulator
 › Press w │ open web

 › Press r │ reload app
 › Press m │ toggle menu
 › Press d │ show developer tools
 › shift+d │ toggle auto opening developer tools on startup (enabled)

 › Press ? │ show all commands

Logs for your project will appear below. Press Ctrl+C to exit.

```

## Video aula

-  [youtube]()
