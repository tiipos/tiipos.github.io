

# [](#header-1) API Introdução


## [](#header-2) Sumário

1. Introdução
2. Hands-on
3. Algumas outras APIs



### [](#header-3) Introdução

- Acrônimos
  - **API** Application Programming Interface
  - Restful (**Rest**) Representational State Transfer
  - HATEOAS Hypermedia As The Engine Of Application State


- Referência
  - Wikipedia
    - Wikipedia API [pt-br](https://pt.wikipedia.org/wiki/Interface_de_programação_de_aplicações) [en](https://en.wikipedia.org/wiki/Application_programming_interface)
    - Wikipedia Restful API [pt-br](https://pt.wikipedia.org/wiki/REST) [en](https://en.wikipedia.org/wiki/Representational_state_transfer)
  - [Uma rápida Introdução ao REST](https://www.infoq.com/br/articles/rest-introduction)
  - [Architectural Styles and
the Design of Network-based Software Architectures](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)
  - [REST: Princípios e boas práticas](http://blog.caelum.com.br/rest-principios-e-boas-praticas/)

- **O que é API Restful?**

_API_: de forma simploria e resumida, 
abstração de um (sub)sistema por suas funcionalidades
para facilitar o uso (programação) deste (sub)sistema.

_Web service_: brevemente, _funções_ de (sub)sistemas publicados na internet, normalmente usando o protocolo HTTP.

_Rest_: de forma restrita e resumida (baseado na [tese](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm) de autoria de [Roy Thomas Fielding](http://www.ics.uci.edu/~fielding/)), 
um estilo arquitetural para systemas de hipermidia distribuída.

Palavras-chaves: URI, Recurso, HTTP (verbos, código de resposta, mensagens [corpo e cabeçalho], URL amigáveis, Stateless, HATEOAS)

_Restful API ou Web service_: resumidamente, APIs web services que aderem ao estilo arquitetural _Rest_.

<!-- 
- **Princípios**
  1. Desenvolvimento orientado a recurso
  2. Os recursos suporta diversas representações (json, xml, csv, html)
  3. Cada recurso tem sua identificação (URI)
  4. Uma sintaxe universal para identificar os recursos (padronizar as URI)
  5. Comunicação por um protocolo cliente/servidor sem estado (stateless)
  6. Os recursos são manipulados por ações (verbos HTTP)
  7. O uso de hipermídia, tanto para a informação da aplicação como para as transições de estado da aplicação (hiperlinks e HATEOAS)
  8. Utilização correta dos códigos HTTP

Propriedades
- Performance in component interactions, which can be the dominant factor in user-perceived performance and network efficiency;
- Scalability allowing to support large numbers of components and interactions among components;
- simplicity of a uniform interface;
modifiability of components to meet changing needs (even while the application is running);
- visibility of communication between components by service agents;
- portability of components by moving program code with the data;
- reliability in the resistance to failure at the system level in the presence of failures within components, connectors, or data.

Restrições
- Client–server architecture
- Statelessness
- Cacheability
- Layered system
- Code on demand (optional)
- Uniform interface
- Resource identification in requests
- Resource manipulation through representations
- Self-descriptive messages
- Hypermedia as the engine of application state (HATEOAS)


outros links
https://stackoverflow.com/questions/671118/what-exactly-is-restful-programming
https://searchmicroservices.techtarget.com/definition/RESTful-API
https://www.codecademy.com/articles/what-is-rest
https://medium.com/@edgar/whats-the-best-strategy-for-versioning-a-restful-api-29b90dbbcf5f
http://www.rodrigocalado.com.br/o-que-e-rest-um-resumo-do-assunto-caracteristicas-conceitos-vantagens-e-desvantagens-prefiro-dizer-que-e-uma-rapida-introducao-ao-assunto/
http://blog.caelum.com.br/morte-a-sessao-entenda-esse-tal-de-stateless-session-com-tokens/
-->

### [](#header-3) Hands-on

1. Cadastrar conta em [Fixer.io](https://fixer.io)
2. Instalar plugin no VS Code
3. Acessar o serviço básico da API de Fixer.io
4. Navegar na documentação da API de Fixer.io

**Passo 1**. Cadastrar conta em [Fixer.io](https://fixer.io)


**Passo 2**. Instalar plugin no VS Code  "Rest client"


**Passo 3**. Acessar o serviço básico da API de Fixer.io

_ERRO: Sem  o Token de autenticação_
```http
GET http://data.fixer.io/api/latest HTTP/1.1
content-type: application/json
```

Pegando última taxas de conversão_
```http
GET http://data.fixer.io/api/latest?access_key=... HTTP/1.1
content-type: application/json
```

_Pegando as moedas disponíveis_
```http
GET http://data.fixer.io/api/symbols?access_key=... HTTP/1.1
content-type: application/json
```


**Passo 4**. Navegar na documentação da API de Fixer.io

[Documentação da API](https://fixer.io/documentation)



### [](#header-3) Algumas outras APIs

- [Concurso busca desenvolver soluções tecnológicas para o Diário Oficial da União](http://portal.imprensanacional.gov.br/noticias-da-imprensa-nacional/-/asset_publisher/54vcxf9ztQ7x/content/id/14960089)
- [Via CEP](https://viacep.com.br)
- Governo brasileiro - Dados abertos
  - [Portal de dados abertos](http://dados.gov.br) [github](https://github.com/dadosgovbr)
  - [Senado](https://www12.senado.leg.br/dados-abertos)
  - [Câmara dos Deputados Federais](https://dadosabertos.camara.leg.br/swagger/api.html)
  - [Educação brasileira](http://educacao.dadosabertosbr.com/api)



# [](#header-2) Links

- API
  - [Fixer](https://fixer.io) : Foreign exchange rates and currency conversion JSON API
    - [API Documentation](https://fixer.io/documentation) : documentação explicando o uso da API
  - [Frankfurter](https://frankfurter.app) : Frankfurter is a lightweight API for querying current and historical forex rates published by the European Central Bank.
- Ferramentas
  - Rest client Visual Code Extension [github](https://github.com/Huachao/vscode-restclient/) [VSC Marketplace](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
  - [Postman](https://www.getpostman.com) : Postman’s API Development Environment (ADE) -
Collections, Workspaces & Tools.

