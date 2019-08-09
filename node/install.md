# Instalação do Node

## Linux

```sh
### Instala o gerencidador de versões de javascript
### https://github.com/nvm-sh/nvm
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash

### configura o ambiente
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

### Instala o node
### https://nodejs.org/en/
nvm install node

### Insala os gerenciadores de pacotes pnpm e yarn
### pnpm https://pnpm.js.org
### yarn https://yarnpkg.com
npm i -g pnpm yarn
```

## OS X

```sh
### Instala o homebrew
### https://brew.sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

### Instala o node
### https://nodejs.org/en/
brew update
brew install node

### Insala os gerenciadores de pacotes pnpm e yarn
### pnpm https://pnpm.js.org
### yarn https://yarnpkg.com
npm i -g pnpm yarn
```
