# [](#header-1) Handling errors : Navigation : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- Mostrar mensagens (erro e info) no topo do aplicativo

### [](#header-3) Componentes

- [StackNavigator](https://facebook.github.io/react-native/docs/navigation.html)
- [DropdownAlert](https://github.com/testshallpass/react-native-dropdownalert)
- [hoistNonReactStatic](https://github.com/mridgway/hoist-non-react-statics)


## [](#header-2) Sumário

1. Acessar diretório da aplicação e executar
2. Criar o componente Alert
3. Habilitar as mensagens via componente Alert
4. Programar o alerta de erro no acesso ao Fixer.io

### [](#header-3) Passo 1. Acessar diretório da aplicação e executar

```sh
cd CurrencyConverter
yarn start
```


### [](#header-3) Passo 2. Criar o componente Alert

**1. criar diretório e arquivos**
```sh
mkdir app/components/Alert
touch app/components/Alert/AlertProvider.js
touch app/components/Alert/connectAlert.js
touch app/components/Alert/index.js
```


**2. editar os códigos-fonte com os conteúdos abaixo**

**app/components/Alert/AlertProvider.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { View, StyleSheet } from 'react-native';
import DropdownAlert from 'react-native-dropdownalert';

const styles = StyleSheet.create({
  view: { 
    flex: 1,
  },
});

class AlertProvider extends Component {
  static get childContextTypes() {
    return {
      alertWithType: PropTypes.func,
      alert: PropTypes.func,
    };
  }

  static get propTypes() {
    return {
      children: PropTypes.any,
    };
  }

  getChildContext() {
    return {
      alert: (...args) => this.dropdown.alert(...args),
      alertWithType: (...args) => this.dropdown.alertWithType(...args),
    };
  }

  render() {
    return (
      <View style={styles.view}>
        {React.Children.only(this.props.children)}
        <DropdownAlert
          ref={(ref) => {
            this.dropdown = ref;
          }}
        />
      </View>
    );
  }
}

export default AlertProvider;
```

**app/components/Alert/connectAlert.js**
```jsx
/* eslint-disable react/prefer-stateless-function */
import PropTypes from 'prop-types';

import React, { Component } from 'react';
import hoistNonReactStatic from 'hoist-non-react-statics';

const connectAlert = (WrappedComponent) => {
  class ConnectedAlert extends Component {
    render() {
      return (
        <WrappedComponent
          {...this.props}
          alertWithType={this.context.alertWithType}
          alert={this.context.alert}
        />
      );
    }
  }

  ConnectedAlert.contextTypes = {
    alertWithType: PropTypes.func,
    alert: PropTypes.func,
  };

  return hoistNonReactStatic(ConnectedAlert, WrappedComponent);
};

export default connectAlert;
```

**app/components/Alert/index.js**
```jsx
import AlertProvider from './AlertProvider';
import connectAlert from './connectAlert';

export { AlertProvider, connectAlert };
```


### [](#header-3) Passo 3. Criar arquivo de configurações de rotas

**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Navigator from './config/routes';
import { AlertProvider } from './components/Alert';

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

**app/screens/Options.js**
```jsx
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { ScrollView, StatusBar, Platform, Linking } from 'react-native';
import { Ionicons } from '@expo/vector-icons';

import { ListItem, Separator } from '../components/List';
import { connectAlert } from '../components/Alert';

const ICON_PREFIX = Platform.OS === 'ios' ? 'ios' : 'md';
const ICON_COLOR = '#868686';
const ICON_SIZE = 23;

class Options extends Component {
  static propTypes = {
    navigation: PropTypes.object,
    alertWithType: PropTypes.func,
  };

  handlePressThemes = () => {
    this.props.navigation.navigate('Themes');
  };

  handlePressSite = () => {
    Linking.openURL('telnet://fixer.io/').catch(() =>
      this.props.alertWithType('error', 'Sorry!', "Fixer.io can't be opened right now."));
    // info, warn, error, success, custom
  };

  render() {
    return (
      <ScrollView>
        <StatusBar translucent={false} barStyle="default" />
        <ListItem
          text="Themes"
          onPress={this.handlePressThemes}
          customIcon={
            <Ionicons name={`${ICON_PREFIX}-arrow-forward`} size={ICON_SIZE} color={ICON_COLOR} />
          }
        />
        <Separator />
        <ListItem
          text="Fixer.io"
          onPress={this.handlePressSite}
          customIcon={<Ionicons name={`${ICON_PREFIX}-link`} size={ICON_SIZE} color={ICON_COLOR} />}
        />
        <Separator />
      </ScrollView>
    );
  }
}
export default connectAlert(Options);
```
