

# [](#header-1) Interface do usuário ReactNative


## [](#header-2) Introdução


## [](#header-2) Sumário de aulas

1. [Tela inicial e componente Container](ui-container)
2. [Componente Logo](ui-logo)
3. [Text input](ui-text)
4. [Botão](ui-button)
6. [Texto estilizado](ui-styled-text)
7. [Header](ui-header)
8. [Teclado](ui-keyboard-view)
9. Logo animado
10. Lista flat
11. Lista de item
12. Tela de opções
13. Temas


**observações**:
1. Os códigos-fonte publicados no [snack.expo](https://snack.expo.io/) estão corretos.
2. [snack.expo](https://snack.expo.io/) tem um _bug_ de renderização de imagem ```TypeError: undefined is not an object (evaluating 'r.width')```. Este erro ocorre porque o objeto ```r``` deveria ter sido instanciado como ```Imagem``` e responder ao método ```width```, contudo este objeto esta vazio, ```undefined``` em _javascript_.


## [](#header-2) bibliotecas e links


**bibliotecas**
- [React Native Extended StyleSheet](https://github.com/vitalets/react-native-extended-stylesheet): estender a forma (estilo) padrão do React Native
- [color](https://github.com/Qix-/color): manipulação de cores
- [moment](https://momentjs.com): locale, parser, validar, manipular e mostrar datas
- [expo icons](https://github.com/expo/vector-icons): icons vetoriais


**comando para adicionar bibliotecas**
```sh
yarn add react-native-extended-stylesheet color moment @expo/vector-icons
```


**outros links**
[código original do curso Handlelabs no github](https://github.com/HandlebarLabs/currency-converter-starter)