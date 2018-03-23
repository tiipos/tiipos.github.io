# [](#header-1) Trabalhando nos DIATINF labs :: git Version Control System 


## [](#header-2) Clonando o repositório

**lista de comandos**
```sh
## Remova o diretório existente
rm CurrencyConverter

## clone o repositório remoto
## LEMBRE DE MUDAR o nome do usuário
git clone https://github.com/tiipos/CurrencyConverter.git

## configure seu usuário e seu email
git config --global user.name "Leonardo MINORA"
git config --global user.email "leonardo.minora@gmail.com"

## Abra o diretório com o VS Code
```

## [](#header-2) Publicando mudanças

**lista de comandos**
```sh
## após modificar os arquivos
## defina quais arquivos precisam ser versionados
git add *

## registre o versionamento com uma mensagem
git commit -m "uma mensagem significativa"

## se desejar copiar a mudança para o repositório remoto
git push origin master

## Abra o diretório com o VS Code
```

## [](#header-2) Lista de comandos

- ```cd```: acessar diretórios
- ```git```: controlar o versionamento de arquivos de um diretório
   - ```git clone```: copia o repositório remoto para o diretório local
   - ```git add```: marca os arquivos para versionar
   - ```git commit -m```: versiona os arquivos marcados com uma mensagem
   - ```git push```: copia o diretório/repositório local para o repositório remoto
   - ```git config --global user.name```: configura nas varáveis globais do git o nome do usuário que aparecerá nos commits
   - ```git config --global user.email```: configura nas varáveis globais do git o email do usuário que aparecerá nos commits