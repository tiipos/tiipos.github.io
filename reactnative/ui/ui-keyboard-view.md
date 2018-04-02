

# [](#header-1) Keyboard View : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- melhorar a apresentação do app quando o teclado aparece.

### [](#header-3) componentes react native

- [KeyboardAvoidingView](https://facebook.github.io/react-native/docs/keyboardavoidingview.html): ajustar o posicionamento dos componentes quando o teclado virtual aparece.



## [](#header-2) Sumário

1. Inicie o app e abra no dispositivo
2. Edite o arquivo ```app/screens/Home.js``` e teste os resultados;


### [](#header-3) Passo 1. Inicie o app e abra no dispositivo

```sh
cd CurrencyConverter
yarn start
```

### [](#header-3) Passo 2. Edite o arquivo ```app/screens/Home.js``` e veja o resultado

**observação** teste o componente ```<KeyboardAvoidingView>``` com:
1. ```behavior="height"```
2. ```behavior="position"```
3. ```behavior="padding"```


```javascript
import React, { Component } from 'react';
import { StatusBar, KeyboardAvoidingView } from 'react-native';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

const TEMP_BASE_CURRENCY = 'USD';
const TEMP_QUOTE_CURRENCY = 'GBP';
const TEMP_BASE_PRICE = '100';
const TEMP_QUOTE_PRICE = '79.74';
const TEMP_LAST_CONVERTED = new Date();
const TEMP_CONVERSION_RATE = 0.79739;

class Home extends Component {
  handleChangeText = () => {
    console.log('change text');
  };

  handlePressBaseCurrency = () => {
    console.log('press base currency');
  };

  handlePressQuoteCurrency = () => {
    console.log('press quote currency');
  };

  handle = () => {
    console.log('clear button pressed');
  };

  handleOptionsPress = () => {
    console.log('options press');
  };

  render() {
    return (
      <Container>
        <StatusBar backgroundColor="blue" barStyle="light-content" />
        <Header onPress={this.handleOptionsPress} />
        <KeyboardAvoidingView behavior="padding">
          <Logo />
          <InputWithButton
            buttonText={TEMP_BASE_CURRENCY}
            onPress={this.handlePressBaseCurrency}
            defaultValue={TEMP_BASE_PRICE}
            keyboardType="numeric"
            onChangeText={this.handleChangeText}
          />
          <InputWithButton
            editable={false}
            buttonText={TEMP_QUOTE_CURRENCY}
            onPress={this.handlePressQuoteCurrency}
            value={TEMP_QUOTE_PRICE}
          />
          <LastConverted
            date={TEMP_LAST_CONVERTED}
            base={TEMP_BASE_CURRENCY}
            quote={TEMP_QUOTE_CURRENCY}
            conversionRate={TEMP_CONVERSION_RATE}
          />
          <ClearButton text="clear" onPress={this.handle} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

export default Home;
```





## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-09-app)


**app/screens/Home.js**
```javascript
import React, { Component } from 'react';
import { StatusBar, KeyboardAvoidingView } from 'react-native';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

const TEMP_BASE_CURRENCY = 'USD';
const TEMP_QUOTE_CURRENCY = 'GBP';
const TEMP_BASE_PRICE = '100';
const TEMP_QUOTE_PRICE = '79.74';
const TEMP_LAST_CONVERTED = new Date();
const TEMP_CONVERSION_RATE = 0.79739;

class Home extends Component {
  handleChangeText = () => {
    console.log('change text');
  };

  handlePressBaseCurrency = () => {
    console.log('press base currency');
  };

  handlePressQuoteCurrency = () => {
    console.log('press quote currency');
  };

  handle = () => {
    console.log('clear button pressed');
  };

  handleOptionsPress = () => {
    console.log('options press');
  };

  render() {
    return (
      <Container>
        <StatusBar backgroundColor="blue" barStyle="light-content" />
        <Header onPress={this.handleOptionsPress} />
        <KeyboardAvoidingView behavior="padding">
          <Logo />
          <InputWithButton
            buttonText={TEMP_BASE_CURRENCY}
            onPress={this.handlePressBaseCurrency}
            defaultValue={TEMP_BASE_PRICE}
            keyboardType="numeric"
            onChangeText={this.handleChangeText}
          />
          <InputWithButton
            editable={false}
            buttonText={TEMP_QUOTE_CURRENCY}
            onPress={this.handlePressQuoteCurrency}
            value={TEMP_QUOTE_PRICE}
          />
          <LastConverted
            date={TEMP_LAST_CONVERTED}
            base={TEMP_BASE_CURRENCY}
            quote={TEMP_QUOTE_CURRENCY}
            conversionRate={TEMP_CONVERSION_RATE}
          />
          <ClearButton text="clear" onPress={this.handle} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

export default Home;
```
