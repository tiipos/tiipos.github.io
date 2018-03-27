# [](#header-1) Botão (Button e Text) : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar um novo componente Button/ClearButton;

### [](#header-3) componentes react native

1. [TouchableOpacity](https://facebook.github.io/react-native/docs/touchableopacity.html): é um wrapper para que as visualizações respondam adequadamente aos toques.
2. [Image](https://facebook.github.io/react-native/docs/image.html): é um componente para mostrar imagens.
3. [Text](https://facebook.github.io/react-native/docs/text.html): é um componente para mostrar textos.
4. [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.

**observação**
[TouchableHighlight vs TouchableOpacity](https://facebook.github.io/react-native/docs/handling-touches.html#touchables)


## [](#header-2) Códigos-fonte

snack expo

[imagens](https://www.filepicker.io/api/file/S2JgcFzJSDyItFfv3sSi)

**app/components/Button/index.js**
```javascript
import ClearButton from './ClearButton';
import styles from './styles';

export { ClearButton, styles };
```

**app/components/Button/ClearButton.js**
```javascript
import PropTypes from 'prop-types';
import React from 'react';
import { Text, TouchableOpacity, Image, View } from 'react-native';

import styles from './styles';

const ClearButton = ({ text, onPress }) => (
  <TouchableOpacity style={styles.container} onPress={onPress}>
    <View style={styles.wrapper}>
      <Image resizeMode="contain" style={styles.icon} source={require('./images/icon.png')} />
      <Text style={styles.text}>{text}</Text>
    </View>
  </TouchableOpacity>
);

ClearButton.propTypes = {
  text: PropTypes.string,
  onPress: PropTypes.func,
};

export default ClearButton
```


**app/components/Button/styles.js**
```javascript
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({
  container: {
    alignItems: 'center',
  },
  wrapper: {
    flexDirection: 'row',
    alignItems: 'center',
  },
  icon: {
    width: 19,
    marginRight: 11,
  },
  text: {
    color: '$white',
    fontSize: 14,
    paddingVertical: 20,
    fontWeight: '300',
  },
});
```

**app/index.js**
```javascript
```

**app/screens/Home.js**
```javascript
import React, { Component } from 'react';
import { StatusBar } from 'react-native';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';

const TEMP_BASE_CURRENCY = 'USD';
const TEMP_QUOTE_CURRENCY = 'GBP';
const TEMP_BASE_PRICE = '100';
const TEMP_QUOTE_PRICE = '79.74';

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

  handleSwapCurrency = () => {
    console.log('handle swap currency');
  };

  render() {
    return (
      <Container>
        <StatusBar backgroundColor="blue" barStyle="light-content" />
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
        <ClearButton onPress={this.handleSwapCurrency} text="Reverse Currencies" />
      </Container>
    );
  }
}

export default Home;
```

