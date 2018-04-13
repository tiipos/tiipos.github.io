

# [](#header-1) Flat list : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar uma nova tela com a lista de moedas (_currencies_).

### [](#header-3) componentes react native

- [FlatList](https://facebook.github.io/react-native/docs/flatlist.html): compomente performático e multiplataforma para apresentar dados em lista.


## [](#header-2) Sumário

1. Inicie o app e abra no dispositivo;
2. Crie e edite o arquivo de dados ```app/data/currencies.js```
3. Crie e edite o arquivo de tela ```app/screens/CurrencyList.js```
2. Edite o arquivo ```app/index.js```


### [](#header-3) Passo 1. Inicie o app e abra no dispositivo ```app/components/Header```

```sh
cd CurrencyConverter
yarn start
```


### [](#header-3) Passo 2. Crie e edite o arquivo de dados ```app/data/currencies.js```

```sh
mkdir app/data
touch app/data/currencies.js
```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 3. Crie e edite o arquivo de tela ```app/screens/CurrencyList.js```

```sh
touch app/screens/CurrencyList.js
```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 2. Edite o arquivo ```app/index.js```

Com editor copie o conteúdo do arquivo do final desta página.
Será substituído a apresentação da tela ```Home``` pela tela ```CurrencyList```












## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-11-app)


**app/data/currencies.js**
```javascript
export default [
  'AUD',
  'BGN',
  'BRL',
  'CAD',
  'CHF',
  'CNY',
  'CZK',
  'DKK',
  'EUR',
  'GBP',
  'HKD',
  'HRK',
  'HUF',
  'IDR',
  'ILS',
  'INR',
  'JPY',
  'KRW',
  'MXN',
  'MYR',
  'NOK',
  'NZD',
  'PHP',
  'PLN',
  'RON',
  'RUB',
  'SEK',
  'SGD',
  'THB',
  'TRY',
  'USD',
  'ZAR',
];
```



**app/screens/CurrencyList.js**
```jsx
import React, { Component } from 'react';
import { FlatList, Text, StatusBar, View } from 'react-native';

import currencies from '../data/currencies';

class CurrencyList extends Component {
  handlePress = () => {
    console.log('row press');
  };

  render() {
    return (
      <View style={{ flex: 1 }}>
        <StatusBar translucent={false} barStyle="default" />
        <FlatList
          data={currencies}
          renderItem={({ item }) => <Text>{item}</Text>}
          keyExtractor={item => item}
        />
      </View>
    );
  }
}

export default CurrencyList;
```



**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import CurrencyList from './screens/CurrencyList';

EStyleSheet.build({
  $primaryBlue: '#4F6D7A',
  $white: '#FFFFFF',
  $lightGray: '#F0F0F0',
  $border: '#979797',
  $inputText: '#797979',
});

export default () => <CurrencyList />;
```