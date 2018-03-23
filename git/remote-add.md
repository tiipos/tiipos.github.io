# [](#header-1) Adicionar um repositório remoto :: git Version Control System 

## [](#header-2) Adicionar repositório remoto ao local

**lista de comandos**
```sh
## acessa diretório que já tem controle de versionamento
cd projeto

## inicializa controle de versionamento no diretório 
git remote add origin https://github.com/usuario/repositorio

## pega histórico de versionamento do repositório remoto e copia para o local
git pull origin master
```

## [](#header-2) Lista de comandos

- ```cd```: acessar diretórios
- ```git```: controlar o versionamento de arquivos de um diretório
   - ```git remote add```: em um diretório que já tem controle de versionamento, adiciona um repositório remote informando um nome e uma URL
   - ```git pull origin master```: em um diretório que já tem controle de versionamento, adiciona um repositório remote informando um nome e uma URL