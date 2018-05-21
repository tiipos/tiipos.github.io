# [](#header-1) Introdução : Redux : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- Instalar componentes
- Projetar dados (data design) da tela Home (app/sreens/Home.js)
- Para a tela Home (app/sreens/Home.js), usando Redux
  1. Especificar estrutura dos dados
  2. Programar o armazenamento e atualização dos dados na tela
    1. Ação
    2. Redutor (capturar dados de forma assíncrona e converter dados)
    3. Configurar armazenamento do dado
    4. Mapear dado para a tela

### [](#header-3) Componentes

- [react-redux](https://github.com/reduxjs/react-redux) 
- [react-logger](https://github.com/evgenyrodionov/redux-logger)



## [](#header-2) Sumário

1. Acessar diretório do aplicativo, instalar componentes e iniciar aplicativo
2. Projetar dados da tela de Home (app/sreens/Home.js)
3. Especificando ações da tela de Home (app/sreens/Home.js)
4. Programando redutores (Redux Reducer)
5. Configurando o armazenamento
6. Atualizando o estado da tela Home (app/sreens/Home.js)
7. Limando o código-fonte


### [](#header-3) Passo 1. Acessar diretório do aplicativo, instalar componentes e iniciar aplicativo

```sh
cd CurrencyConverter
yarn add react-redux redux redux-logger
yarn start
```


### [](#header-3) Passo 2. Projetar dados da tela de Home (app/sreens/Home.js)

**projetando a estrutura dos dados**
```
{
  currencies: {
    baseCurrency: <String>,
    quoteCurrency: <String>,
    amount: <Number>,
    conversions: {
      [CURRENCY_STRING]: {
        isFetching: <Boolean>,
        date: <String>,
        rates: {
          [CURRENCY_STRING]: <Number>,
          ...
        }
      }
    }
  }
}
```

**exemplo de dados**
```json
{
  "currencies": {
    "baseCurrency": "USD",
    "quoteCurrency": "GBP",
    "amount": 100,
    "conversions": {
      "USD": {
        "isFetching": false,
        "date": "2017-05-31",
        "rates": {
          "BRL": 3.2515,
          "GBP": 0.77858,
          "EUR": 0.89119,
        },
      "GBP": {
        "isFetching": false,
        "date": "2017-05-31",
        "rates": {
          "BRL": 3.2515,
          "EUR": 0.89119,
          "USD": 0.77858,
        },
      }
    }
  }
}
```

### [](#header-3) Passo 3. Especificando ações da tela de Home (app/sreens/Home.js)

__Ações da tela__:
- **troca de moeda**: trocar as moedas de _base_ para _quote_ e vice-versa
- **valor a ser convertido**: _amount_ digitar o valor em _base_ e ele calcular o valor de _quote_


Etapa 1. **criar diretório e arquivo de ações**
```sh
mkdir app/actions
touch app/actions/currencies.js
```

Etapa 2. **editar o arquivo app/actions/currencies.js**
```jsx
export const CHANGE_CURRENCY_AMOUNT = 'CHANGE_CURRENCY_AMOUNT';
export const SWAP_CURRENCY = 'SWAP_CURRENCY';

export const changeCurrencyAmount = amount => ({
  type: CHANGE_CURRENCY_AMOUNT,
  amount: parseFloat(amount),
});

export const swapCurrency = () => ({
  type: SWAP_CURRENCY,
});
```

Etapa 3. **editar o arquivo app/screens/Home.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { KeyboardAvoidingView, StatusBar } from 'react-native';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

import { changeCurrencyAmount, swapCurrency } from '../actions/currencies';

const TEMP_BASE_CURRENCY = 'USD';
const TEMP_QUOTE_CURRENCY = 'GBP';
const TEMP_BASE_PRICE = '100';
const TEMP_QUOTE_PRICE = '79.74';
const TEMP_LAST_CONVERTED = new Date();
const TEMP_CONVERSION_RATE = 0.79739;

class Home extends Component {
  static propTypes = {
    navigation: PropTypes.object,
  };

  handleChangeText = (text) => {
    console.log(changeCurrencyAmount(text));
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Base currency' });
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Quote currency' });
  };

  handleSwapCurrency = () => {
    console.log(swapCurrency());
  };

  handleOptionsPress = () => {
    this.props.navigation.navigate('Options');
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
          <ClearButton text="Reverse currencies" onPress={this.handleSwapCurrency} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

export default Home;
```


### [](#header-3) Passo 4. Programando redutores (Redux Reducer)

Etapa 1. **criar o diretório** (app/reducers/) **e os arquivos** (index.js e currencies.js)
```sh
mkdir app/reducers
touch app/reducers/index.js
touch app/reducers/currencies.js
```

Etapa 2. editar o arquivo **app/reducers/index.js**
```jsx
import { combineReducers } from 'redux';

import currencies from './currencies';

export default combineReducers({
  currencies,
});
```

Etapa 3. editar o **app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Navigator from './config/routes';
import { AlertProvider } from './components/Alert';

import './reducers';

EStyleSheet.build({
  $primaryBlue: '#4F6D7A',
  $primaryOrange: '#D57A66',
  $primaryGreen: '#00BD9D',
  $primaryPurple: '#9E768F',
  $white: '#FFFFFF',
  $lightGray: '#F0F0F0',
  $border: '#979797',
  $inputText: '#797979',
  $darkText: '#343434',
});

export default () => (
  <AlertProvider>
    <Navigator />
  </AlertProvider>
);
```

Etapa 4. Habilitar o _Remote Debugging_ no smartphone

Etapa 5. editar o arquivo **app/reducers/currencies.js**
```jsx
// import { CHANGE_CURRENCY_AMOUNT, SWAP_CURRENCY } from '../actions/currencies';
import {
  CHANGE_CURRENCY_AMOUNT,
  SWAP_CURRENCY,
  changeCurrencyAmount,
  swapCurrency,
} from '../actions/currencies';

const initialState = {
  baseCurrency: 'USD',
  quoteCurrency: 'GBP',
  amount: 100,
  conversions: {},
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case CHANGE_CURRENCY_AMOUNT:
      return {
        ...state,
        amount: action.amount || 0,
      };
    case SWAP_CURRENCY:
      return {
        ...state,
        baseCurrency: state.quoteCurrency,
        quoteCurrency: state.baseCurrency,
      };
    default:
      return state;
  }
};

console.log('InitialState', initialState);
console.log('swapCurrency', reducer(initialState, swapCurrency()));
console.log('changeCurrencyAmount', reducer(initialState, changeCurrencyAmount(222)));

export default reducer;
```


### [](#header-3) Passo 5. Configurando o armazenamento

Etapa 1. criar o arquivo **app/config/store.js**
```sh
touch app/config/store.js
```

Etapa 2. editar o arquito **app/config/store.js**
```jsx
import { createStore, applyMiddleware } from 'redux';
import logger from 'redux-logger';

import reducer from '../reducers';

const middleware = [];
if (process.env.NODE_ENV === 'development') {
  middleware.push(logger);
}

const store = createStore(reducer, applyMiddleware(...middleware));

export default store;
```

Etapa 3. editar o arquivo **app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';
import { Provider } from 'react-redux';

import Navigator from './config/routes';
import store from './config/store';
import { AlertProvider } from './components/Alert';

import './reducers';

EStyleSheet.build({
  $primaryBlue: '#4F6D7A',
  $primaryOrange: '#D57A66',
  $primaryGreen: '#00BD9D',
  $primaryPurple: '#9E768F',
  $white: '#FFFFFF',
  $lightGray: '#F0F0F0',
  $border: '#979797',
  $inputText: '#797979',
  $darkText: '#343434',
});

export default () => (
  <Provider store={store}>
    <AlertProvider>
      <Navigator onNavigationStateChange={null} />
    </AlertProvider>
  </Provider>
);
```

Etapa 4. editar o arquivo **app/screens/Home.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { KeyboardAvoidingView, StatusBar } from 'react-native';
import { connect } from 'react-redux';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

import { changeCurrencyAmount, swapCurrency } from '../actions/currencies';

const TEMP_BASE_CURRENCY = 'USD';
const TEMP_QUOTE_CURRENCY = 'GBP';
const TEMP_BASE_PRICE = '100';
const TEMP_QUOTE_PRICE = '79.74';
const TEMP_LAST_CONVERTED = new Date();
const TEMP_CONVERSION_RATE = 0.79739;

class Home extends Component {
  static propTypes = {
    navigation: PropTypes.object,
    dispatch: PropTypes.func,
  };

  handleChangeText = (text) => {
    this.props.dispatch(changeCurrencyAmount(text));
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Base currency' });
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Quote currency' });
  };

  handleSwapCurrency = () => {
    this.props.dispatch(swapCurrency());
  };

  handleOptionsPress = () => {
    this.props.navigation.navigate('Options');
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
          <ClearButton text="Reverse currencies" onPress={this.handleSwapCurrency} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

export default connect()(Home);
```



### [](#header-3) Passo 6. Atualizando o estado da tela Home (app/sreens/Home.js)

Etapa 1. **Criar a função de mapeamento** e modificar no botão de moedas o texto, arquivo **app/screens/Home.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { KeyboardAvoidingView, StatusBar } from 'react-native';
import { connect } from 'react-redux';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

import { changeCurrencyAmount, swapCurrency } from '../actions/currencies';

const TEMP_BASE_PRICE = '100';
const TEMP_QUOTE_PRICE = '79.74';
const TEMP_LAST_CONVERTED = new Date();
const TEMP_CONVERSION_RATE = 0.79739;

class Home extends Component {
  static propTypes = {
    navigation: PropTypes.object,
    dispatch: PropTypes.func,
    baseCurrency: PropTypes.string,
    quoteCurrency: PropTypes.string,
  };

  handleChangeText = (text) => {
    this.props.dispatch(changeCurrencyAmount(text));
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Base currency' });
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Quote currency' });
  };

  handleSwapCurrency = () => {
    this.props.dispatch(swapCurrency());
  };

  handleOptionsPress = () => {
    this.props.navigation.navigate('Options');
  };

  render() {
    return (
      <Container>
        <StatusBar backgroundColor="blue" barStyle="light-content" />
        <Header onPress={this.handleOptionsPress} />
        <KeyboardAvoidingView behavior="padding">
          <Logo />
          <InputWithButton
            buttonText={this.props.baseCurrency}
            onPress={this.handlePressBaseCurrency}
            defaultValue={TEMP_BASE_PRICE}
            keyboardType="numeric"
            onChangeText={this.handleChangeText}
          />
          <InputWithButton
            editable={false}
            buttonText={this.props.quoteCurrency}
            onPress={this.handlePressQuoteCurrency}
            value={TEMP_QUOTE_PRICE}
          />
          <LastConverted
            date={TEMP_LAST_CONVERTED}
            base={this.props.baseCurrency}
            quote={this.props.quoteCurrency}
            conversionRate={TEMP_CONVERSION_RATE}
          />
          <ClearButton text="Reverse currencies" onPress={this.handleSwapCurrency} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

const mapStateToProps = (state) => {
  const baseCurrency = state.currencies.baseCurrency;
  const quoteCurrency = state.currencies.quoteCurrency;

  return {
    baseCurrency,
    quoteCurrency,
  };
};

export default connect(mapStateToProps)(Home);
```

Etapa 2. Atualizar os dados iniciais **app/reducers/currencies.js**
```jsx
import {
  CHANGE_CURRENCY_AMOUNT,
  SWAP_CURRENCY,
  changeCurrencyAmount,
  swapCurrency,
} from '../actions/currencies';

const initialState = {
  baseCurrency: 'USD',
  quoteCurrency: 'GBP',
  amount: 100,
  conversions: {
    USD: {
      isFetching: false,
      base: 'USD',
      date: '2017-05-31',
      rates: {
        AUD: 1.3416,
        BGN: 1.743,
        BRL: 3.2515,
        CAD: 1.3464,
        CHF: 0.97104,
        CNY: 6.813,
        CZK: 23.547,
        DKK: 6.6302,
        GBP: 0.77858,
        HKD: 7.7908,
        HRK: 6.6068,
        HUF: 273.77,
        IDR: 13308,
        ILS: 3.5431,
        INR: 64.463,
        JPY: 110.86,
        KRW: 1118.4,
        MXN: 18.765,
        MYR: 4.281,
        NOK: 8.4117,
        NZD: 1.4071,
        PHP: 49.77,
        PLN: 3.7173,
        RON: 4.0687,
        RUB: 56.774,
        SEK: 8.6942,
        SGD: 1.3829,
        THB: 34.07,
        TRY: 3.5366,
        ZAR: 13.133,
        EUR: 0.89119,
      },
    },
  },
};

const reducer = (state = initialState, action) => {
  switch (action.type) {
    case CHANGE_CURRENCY_AMOUNT:
      return {
        ...state,
        amount: action.amount || 0,
      };
    case SWAP_CURRENCY:
      return {
        ...state,
        baseCurrency: state.quoteCurrency,
        quoteCurrency: state.baseCurrency,
      };
    default:
      return state;
  }
};

console.log('InitialState', initialState);
console.log('swapCurrency', reducer(initialState, swapCurrency()));
console.log('changeCurrencyAmount', reducer(initialState, changeCurrencyAmount(222)));

export default reducer;
```

Etapa 3. Modificar valores das moedas no arquivo **app/screens/Home.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { KeyboardAvoidingView, StatusBar } from 'react-native';
import { connect } from 'react-redux';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

import { changeCurrencyAmount, swapCurrency } from '../actions/currencies';

const TEMP_LAST_CONVERTED = new Date();

class Home extends Component {
  static propTypes = {
    navigation: PropTypes.object,
    dispatch: PropTypes.func,
    baseCurrency: PropTypes.string,
    quoteCurrency: PropTypes.string,
    amount: PropTypes.number,
    conversionRate: PropTypes.number,
  };

  handleChangeText = (text) => {
    this.props.dispatch(changeCurrencyAmount(text));
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Base currency' });
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Quote currency' });
  };

  handleSwapCurrency = () => {
    this.props.dispatch(swapCurrency());
  };

  handleOptionsPress = () => {
    this.props.navigation.navigate('Options');
  };

  render() {
    const quotePrice = (this.props.amount * this.props.conversionRate).toFixed(2);

    return (
      <Container>
        <StatusBar backgroundColor="blue" barStyle="light-content" />
        <Header onPress={this.handleOptionsPress} />
        <KeyboardAvoidingView behavior="padding">
          <Logo />
          <InputWithButton
            buttonText={this.props.baseCurrency}
            onPress={this.handlePressBaseCurrency}
            defaultValue={this.props.amount.toString()}
            keyboardType="numeric"
            onChangeText={this.handleChangeText}
          />
          <InputWithButton
            editable={false}
            buttonText={this.props.quoteCurrency}
            onPress={this.handlePressQuoteCurrency}
            value={quotePrice}
          />
          <LastConverted
            date={TEMP_LAST_CONVERTED}
            base={this.props.baseCurrency}
            quote={this.props.quoteCurrency}
            conversionRate={this.props.conversionRate}
          />
          <ClearButton text="Reverse currencies" onPress={this.handleSwapCurrency} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

const mapStateToProps = (state) => {
  const baseCurrency = state.currencies.baseCurrency;
  const quoteCurrency = state.currencies.quoteCurrency;
  const conversionSelector = state.currencies.conversions[baseCurrency] || {};
  const rates = conversionSelector.rates || {};

  return {
    baseCurrency,
    quoteCurrency,
    amount: state.currencies.amount,
    conversionRate: rates[quoteCurrency] || 0,
  };
};

export default connect(mapStateToProps)(Home);
```

Etapa 4. Trabalhando com datas na tela **app/screens/Home.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { KeyboardAvoidingView, StatusBar } from 'react-native';
import { connect } from 'react-redux';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
import { Header } from '../components/Header';

import { changeCurrencyAmount, swapCurrency } from '../actions/currencies';

class Home extends Component {
  static propTypes = {
    navigation: PropTypes.object,
    dispatch: PropTypes.func,
    baseCurrency: PropTypes.string,
    quoteCurrency: PropTypes.string,
    amount: PropTypes.number,
    conversionRate: PropTypes.number,
    lastConvertedDate: PropTypes.object,
  };

  handleChangeText = (text) => {
    this.props.dispatch(changeCurrencyAmount(text));
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Base currency' });
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', { title: 'Quote currency' });
  };

  handleSwapCurrency = () => {
    this.props.dispatch(swapCurrency());
  };

  handleOptionsPress = () => {
    this.props.navigation.navigate('Options');
  };

  render() {
    const quotePrice = (this.props.amount * this.props.conversionRate).toFixed(2);

    return (
      <Container>
        <StatusBar backgroundColor="blue" barStyle="light-content" />
        <Header onPress={this.handleOptionsPress} />
        <KeyboardAvoidingView behavior="padding">
          <Logo />
          <InputWithButton
            buttonText={this.props.baseCurrency}
            onPress={this.handlePressBaseCurrency}
            defaultValue={this.props.amount.toString()}
            keyboardType="numeric"
            onChangeText={this.handleChangeText}
          />
          <InputWithButton
            editable={false}
            buttonText={this.props.quoteCurrency}
            onPress={this.handlePressQuoteCurrency}
            value={quotePrice}
          />
          <LastConverted
            date={this.props.lastConvertedDate}
            base={this.props.baseCurrency}
            quote={this.props.quoteCurrency}
            conversionRate={this.props.conversionRate}
          />
          <ClearButton text="Reverse currencies" onPress={this.handleSwapCurrency} />
        </KeyboardAvoidingView>
      </Container>
    );
  }
}

const mapStateToProps = (state) => {
  const baseCurrency = state.currencies.baseCurrency;
  const quoteCurrency = state.currencies.quoteCurrency;
  const conversionSelector = state.currencies.conversions[baseCurrency] || {};
  const rates = conversionSelector.rates || {};

  return {
    baseCurrency,
    quoteCurrency,
    amount: state.currencies.amount,
    conversionRate: rates[quoteCurrency] || 0,
    lastConvertedDate: conversionSelector.date ? new Date(conversionSelector.date) : new Date(),
  };
};

export default connect(mapStateToProps)(Home);
```


### [](#header-3) Passo 7. Limando o código-fonte
