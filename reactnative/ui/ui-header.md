

# [](#header-1) Header : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar um componente Header/Header;
- consultar proriedades do dispositivo para montar estilo.

### [](#header-3) componentes react native

- ```@media ios``` e ```@media ios``` [@media](https://github.com/vitalets/react-native-extended-stylesheet#media-queries): consulta propriedades do dispositivo.
- [Image](https://facebook.github.io/react-native/docs/image.html): é um componente para mostrar imagens.
- [StatusBar](https://facebook.github.io/react-native/docs/statusbar.html): é um componente que controla a barra de status do aplicativo.
- [TouchableOpacity](https://facebook.github.io/react-native/docs/touchableopacity.html): é um wrapper para que as visualizações respondam adequadamente aos toques.
- [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.
- func in [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html): checagem de tipos 



## [](#header-2) Sumário

1. Crie diretório ```app/components/Header```;
2. No diretório ```app/components/Header```, crie os arquivos ```index.js``` ```styles.js``` e ```Header.js```;
3. Inicie o app e abra no dispositivo (ou emulador);
4. Edite o arquivo ```app/components/Header/styles.js```;
5. Edite o arquivo ```app/components/Header/Header.js```;
6. Edite o arquivo ```app/components/Header/index.js```;
7. Edite o arquivo ```app/screens/Home.js``` e veja o resultado;
8. Crie o diretório ```app/components/Header/images```;
9. Copie os arquivos de imagens da [internet](https://www.filepicker.io/api/file/aoA8J6S6RKyTsJfdonzF) para ```app/components/Header/images```;
10. Edite o arquivo ```app/components/Header/Header.js```;
11. Edite o arquivo ```app/components/Header/styles.js```;

### [](#header-3) Passo 1. Crie diretório ```app/components/Header```

```sh
cd CurrencyConverter
mkdir app/components/Header
```

### [](#header-3) Passo 2. Crie os arquivos ```index.js``` ```styles.js``` e ```Header.js```

```sh
touch app/components/Header/index.js
touch app/components/Header/styles.js
touch app/components/Header/Header.js
```

### [](#header-3) Passo 3. Inicie o app e abra no dispositivo

```sh
yarn start
```


### [](#header-3) Passo 4. Edite o arquivo ```app/components/Header/styles.js```

```jsx
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({});
```


### [](#header-3) Passo 5. Edite o arquivo ```app/components/Header/Header.js```

```jsx
import React from 'react';
import { View } from 'react-native';

import styles from './styles';

const Header = () => <View style={styles.container} />;

export default Header;
```


### [](#header-3) Passo 6. Edite o arquivo ```app/components/Header/index.js```

```jsx
import Header from './Header';
import styles from './styles';

export { Header, styles };
```


### [](#header-3) Passo 7. Edite o arquivo ```app/screens/Home.js``` e veja o resultado

```jsx
import React, { Component } from 'react';
import { StatusBar } from 'react-native';

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
      </Container>
    );
  }
}

export default Home;
```

### [](#header-3) Passo 8. Crie o diretório ```app/components/Header/images```

```sh
mkdir app/components/Header/images
```


### [](#header-3) Passo  9. Copie os arquivos de imagens para ```app/components/Header/images```

1. acesse o endereço da [imagens](https://www.filepicker.io/api/file/aoA8J6S6RKyTsJfdonzF)
2. se o sistema operacional ainda não o fez, descompacte os arquivos do arquivo baixado.
3. mova os arquivos para ```app/components/Header/images```

ou na linha de comando e de dentro do diretório ```CurrencyConverter```
```sh
cd app/components/Header
curl https://www.filepicker.io/api/file/aoA8J6S6RKyTsJfdonzF --output images.zip
unzip images.zip
ls -l images
```

### [](#header-3) Passo 10. Edite o arquivo ```app/components/Header/Header.js```

```jsx
import PropTypes from 'prop-types';
import React from 'react';
import { View, Image, TouchableOpacity } from 'react-native';

import styles from './styles';

const Header = ({ onPress }) => (
  <View style={styles.container}>
    <TouchableOpacity onPress={onPress} style={styles.button}>
      <Image resizeMode="contain" source={require('./images/gear.png')} style={styles.icon} />
    </TouchableOpacity>
  </View>
);

Header.propTypes = {
  onPress: PropTypes.func,
};

export default Header;
```


### [](#header-3) Passo 11. Edite o arquivo ```app/components/Header/styles.js```

```jsx
import EStyleSheet from 'react-native-extended-stylesheet';
import { StatusBar } from 'react-native';

export default EStyleSheet.create({
  container: {
    position: 'absolute',
    left: 0,
    top: 0,
    right: 0,
    '@media ios': {
      paddingTop: 20,
    },
    '@media android': {
      paddingTop: StatusBar.currentHeight,
    },
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





## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-08-app)

[imagens](https://www.filepicker.io/api/file/aoA8J6S6RKyTsJfdonzF)


**app/components/Header/index.js**
```jsx
import Header from './Header';
import styles from './styles';

export { Header, styles };
```


**app/components/Header/Header.js**
```jsx
import PropTypes from 'prop-types';
import React from 'react';
import { View, Image, TouchableOpacity } from 'react-native';

import styles from './styles';

const Header = ({ onPress }) => (
  <View style={styles.container}>
    <TouchableOpacity onPress={onPress} style={styles.button}>
      <Image resizeMode="contain" source={require('./images/gear.png')} style={styles.icon} />
    </TouchableOpacity>
  </View>
);

Header.propTypes = {
  onPress: PropTypes.func,
};

export default Header;
```

**app/components/Header/styles.js**
```jsx
import EStyleSheet from 'react-native-extended-stylesheet';
import { StatusBar } from 'react-native';

export default EStyleSheet.create({
  container: {
    position: 'absolute',
    left: 0,
    top: 0,
    right: 0,
    '@media ios': {
      paddingTop: 20,
    },
    '@media android': {
      paddingTop: StatusBar.currentHeight,
    },
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
import React, { Component } from 'react';
import { StatusBar } from 'react-native';

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
      </Container>
    );
  }
}

export default Home;
```
