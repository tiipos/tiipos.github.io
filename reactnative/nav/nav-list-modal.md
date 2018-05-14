# [](#header-1) List modal : Navigation : ReactNative


## [](#header-2) Introdução

**Objetivos**:


### [](#header-3) Componentes

- StackNavigator


## [](#header-2) Sumário

1. Acessar diretório da aplicação e instalar pacotes
2. Criar diretório de configuração
3. Criar arquivo de configurações de rotas
4. Adicionar rotas no app/index
5. Configurar header da tela Home
6. Programando navegação na tela Home
7. Configurando o tipo de transição
8. Configurando o título das telas
9. Fechando a tela CurrencyList quando escolher uma moeda
10. Ajustando o Header para o Android


### [](#header-3) Passo 1. Acessar diretório da aplicação e instalar pacotes

```sh
cd CurrencyConverter
yarn add react-navigation react-native-dropdownalert hoist-non-react-statics
yarn start
```


### [](#header-3) Passo 2. Criar diretório de configuração

```sh
mkdir app/config
```


### [](#header-3) Passo 3. Criar arquivo de configurações de rotas

```sh
touch app/config/router.js
```


**app/config/router.js**
```jsx
import  { StackNavigator } from 'react-navigation';

import Home from '../screens/Home';
import CurrencyList from '../screens/CurrencyList';

export default StackNavigator({
  Home: {
    screen: Home,
  },
  CurrencyList: {
    screen: CurrencyList
  },
});
```


### [](#header-3) Passo 4. Adicionar rotas no app/index.js

**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Navigator from './config/routes';

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

export default () => <Navigator />;
```


### [](#header-3) Passo 5. Configurar header da tela Home

**app/config/router.js**
```jsx
import  { StackNavigator } from 'react-navigation';

import Home from '../screens/Home';
import CurrencyList from '../screens/CurrencyList';

export default StackNavigator({
  Home: {
    screen: Home,
    navigationOptions: {
      header: () => null,
    },
  },
  CurrencyList: {
    screen: CurrencyList
  },
});
```


### [](#header-3) Passo 6. Programando navegação na tela Home

**app/screens/Home.js**
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

  handleChangeText = () => {
    console.log('change text');
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList');
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList');
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


### [](#header-3) Passo 7. Configurando o tipo de transição

**app/config/router.js**
```jsx
import  { StackNavigator } from 'react-navigation';

import Home from '../screens/Home';
import CurrencyList from '../screens/CurrencyList';

export default StackNavigator(
  {
    Home: {
      screen: Home,
      navigationOptions: {
        header: () => null,
      },
    },
    CurrencyList: {
      screen: CurrencyList
    }
  },
  {
      mode: 'modal',
  }
);
```


### [](#header-3) Passo 8. Configurando o título das telas

**app/screens/Home.js**
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

  handleChangeText = () => {
    console.log('change text');
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', {title: 'Base currency'});
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', {title: 'Quote currency'});
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

**app/config/router.js**
```jsx
import  { StackNavigator } from 'react-navigation';

import Home from '../screens/Home';
import CurrencyList from '../screens/CurrencyList';

export default StackNavigator(
  {
    Home: {
      screen: Home,
      navigationOptions: {
        header: () => null,
      },
    },
    CurrencyList: {
      screen: CurrencyList,
      navigationOptions: ({navigation}) => ({
        headerTitle: navigation.state.params.title,
      }),
    },
  },
  {
    mode: 'modal',
  }
);
```


### [](#header-3) Passo 9. Fechando a tela CurrencyList quando escolher uma moeda

**app/screens/CurrencyList.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { FlatList, StatusBar, View } from 'react-native';

import currencies from '../data/currencies';
import { ListItem } from '../components/List';

const TEMP_CURRENT_CURRENCY = 'CAD';

class CurrencyList extends Component {
  static propTypes = {
    navigation: PropTypes.object,
  };

  handlePress = () => {
    this.props.navigation.goBack(null);
  };

  render() {
    return (
      <View>
        <StatusBar translucent={false} barStyle="default" />
        <FlatList
          data={currencies}
          renderItem={({ item }) => (
            <ListItem
              text={item}
              selected={item === TEMP_CURRENT_CURRENCY}
              onPress={this.handlePress}
            />
          )}
          keyExtractor={item => item}
        />
      </View>
    );
  }
}

export default CurrencyList;
```


### [](#header-3) Passo 10. Ajustando o Header para o Android

**app/config/router.js**
```jsx
import { StatusBar } from 'react-native';
import { StackNavigator } from 'react-navigation';

import Home from '../screens/Home';
import CurrencyList from '../screens/CurrencyList';

export default StackNavigator(
  {
    Home: {
      screen: Home,
      navigationOptions: {
        header: () => null,
      },
    },
    CurrencyList: {
      screen: CurrencyList,
      navigationOptions: ({navigation}) => ({
        headerTitle: navigation.state.params.title,
      }),
    }
  },
  {
    mode: 'modal',
    cardStyle: {
      paddingTop: StatusBar.currentHeight,
    },
  },
);
```

**app/components/Header/styles.js**
```jsx
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({
  container: {
    position: 'absolute',
    left: 0,
    top: 0,
    right: 0,
    '@media ios': {
      paddingTop: 20,
    }
  },
  button: {
    alignSelf: 'flex-end',
    paddingVertical: 5,
    paddingHorizontal: 20,
  },
  icon: {
    width: 18,
  },
});
```


## [](#header-2) Arquivos finais

**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Navigator from './config/routes';

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

export default () => <Navigator />;
```


**app/config/router.js**
```jsx
import { StatusBar } from 'react-native';
import { StackNavigator } from 'react-navigation';

import Home from '../screens/Home';
import CurrencyList from '../screens/CurrencyList';

export default StackNavigator({
  Home: {
    screen: Home,
    navigationOptions: {
      header: () => null,
    },
  },
  CurrencyList: {
    screen: CurrencyList,
    navigationOptions: ({navigation}) => ({
      headerTitle: navigation.state.params.title,
    }),
  },
  {
    mode: 'modal',
    cardStyle: {
      paddingTop: StatusBar.currentHeight,
    },
  },
});
```


**app/components/Header/styles.js**
```jsx
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({
  container: {
    position: 'absolute',
    left: 0,
    top: 0,
    right: 0,
    '@media ios': {
      paddingTop: 20,
    }
  },
  button: {
    alignSelf: 'flex-end',
    paddingVertical: 5,
    paddingHorizontal: 20,
  },
  icon: {
    width: 18,
  },
});
```

**app/screens/Home.js**
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

  handleChangeText = () => {
    console.log('change text');
  };

  handlePressBaseCurrency = () => {
    this.props.navigation.navigate('CurrencyList', {title: 'Base currency'});
  };

  handlePressQuoteCurrency = () => {
    this.props.navigation.navigate('CurrencyList', {title: 'Quote currency'});
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


**app/screens/CurrencyList.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { FlatList, StatusBar, View } from 'react-native';

import currencies from '../data/currencies';
import { ListItem } from '../components/List';

const TEMP_CURRENT_CURRENCY = 'CAD';

class CurrencyList extends Component {
  static propTypes = {
    navigation: PropTypes.object,
  };

  handlePress = () => {
    this.props.navigation.goBack(null);
  };

  render() {
    return (
      <View>
        <StatusBar translucent={false} barStyle="default" />
        <FlatList
          data={currencies}
          renderItem={({ item }) => (
            <ListItem
              text={item}
              selected={item === TEMP_CURRENT_CURRENCY}
              onPress={this.handlePress}
            />
          )}
          keyExtractor={item => item}
        />
      </View>
    );
  }
}

export default CurrencyList;
```
