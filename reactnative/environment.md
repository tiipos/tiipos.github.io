# [](#header-1) React Native Introdução

## [](#header-2) Ferramentas

- vscode
- Node, npm, yarn, React Native funcionam em qualquer ambiente
- Expo funciona com simulador e com o dispositivo (iOS e Android)


## [](#header-2) Criar um app React Native

**comandos**
```sh
yarn global add create-react-native-app
create-react-native-app CurrencyConverter
cd CurrencyConverter
yarn start
```

## [](#header-2) Publicando no GitHub

1. Crie uma conta no github
2. Crie um repositório no github
3. Conecte o diretório da aplicação ao repositório
   - ```git remote add https://github.com/tiipos/CurrencyConverter.git```
4. Sincronize o diretório local com o github
   - ```git pull```
5. Crie e adicione um arquivo .gitignore
   - ```git add .gitignore```
   - ```git commit -m "configurando para ignorar arquivos locais"```
6. Adicione os arquivos locais
   - ```git add *```
   - ```git commit -m "publicando o projeto padrão inicial"```
7. Publique tudo no github
   - ```git push```


## Configurando o editor de código

Plugins do **vscode**
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


## Organização do projeto

- app
  - index.js
  - components
  - config
  - screens


## Monitorando o código do projeto

1. ```yarn global add eslint```
2. ```eslint --init```
  - __Use a popular style guide__
  - __Airbnb__
  - Do you use React? __y__
  - What format do you want your config file to be in? __JSON__
3. Editar o arquivo ```.eslintrc.json```
8. Adicionar script no arquivo ```package.json```
9. Configurar o VSCode para o projeto

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

__package.json__
```json
"scripts": {
    // ...
    "lint": "eslint app/",
    "lint:fix": "eslint app/ --fix"
}
```

__.vscode/settings.json__
```json
{
  "prettier.eslintIntegration": true,
  "editor.formatOnSave": true
}
```

## Como debug um projeto

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