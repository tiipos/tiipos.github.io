# [](#header-1) Introdução a API

## Sumário

1. Histórico
2. Propriedades
3. Princípios
4. Níveis de REST API

## 1. Histórico

- Sistemas operacionais [API](https://pt.wikipedia.org/wiki/Interface_de_programação_de_aplicações)
- Computação distribuída
  - RPC, CORBA, EJB, COM+
- [Web service](https://en.wikipedia.org/wiki/Web_service)
  - [SOAP](https://pt.wikipedia.org/wiki/SOAP)
  - [REST API](https://pt.wikipedia.org/wiki/REST)

## 2. Propriedades

1. Performance
2. Escalabilidade
3. Simplicidade com uma interface uniforme
4. _Modificabilidade_
5. Visibilidade entre as partes
6. Portabilidade do código e dados
7. _Reliability_

## 2. Princípios

1. Arquitetura cliente/servidor
2. Sem estado
   - cada mensagem HTTP contém toda a informação necessária para compreender o pedido. Como resultado, nem o cliente e nem o servidor necessitam gravar nenhum estado das comunicações entre mensagens. Na prática, muitas aplicações baseadas em HTTP utilizam cookies e outros mecanismos para manter o estado da sessão (algumas destas práticas, como a reescrita de URLs, não são permitidas pela regra do REST).
3. Passível de fazer cache
4. Sistemas em camadas
5. Código sobre demanda (opcional)
6. Interface uniforme

## Níveis de REST API

1. Nível 0 : The Swamp of POX
   - Usa o HTTP para interações remotas
   - NÃO usa nenhum dos mecanismos/recursos da internet
2. Nível 1 : Resources
   - Introdução a recursos
3. Nível 2 : HTTP Verbs
   - Início do uso de verbos HTTP
4. Nível 3 : Hypermedia controls
   - HATEOAS : _Hypertext As The Engine Of Application State_

## Node e Frameworks
