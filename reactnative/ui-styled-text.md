

# [](#header-1) Logo : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar um novo componente TextInput;

### [](#header-3) componentes react native

1. [TouchableHighlight](https://facebook.github.io/react-native/docs/touchablehighlight.html): é um wrapper para que as visualizações respondam adequadamente aos toques.
2. [TextInput](https://facebook.github.io/react-native/docs/textinput.html): é um componente para entrada de texto.
3. [Text](https://facebook.github.io/react-native/docs/text.html): é um componente para mostrar textos.
4. [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.

**observação**
[TouchableHighlight vs TouchableOpacity](https://facebook.github.io/react-native/docs/handling-touches.html#touchables)


## [](#header-2) Códigos-fonte

snack expo

**app/components/Text/index.js**
```javascript
import LastConverted from './LastConverted';
import styles from './styles';

export { LastConverted, styles };
```


**app/components/Text/LastConverted.js**
```javascript
import PropTypes from 'prop-types';
import React from 'react';
import { Text } from 'react-native';
import moment from 'moment';

import styles from './styles';

const LastConverted = ({ date, base, quote, conversionRate }) => (
  <Text style={styles.smallText}>
    1 {base} = {conversionRate} {quote} as of {moment(date).format('MMMM D, YYYY')}
  </Text>
);

LastConverted.propTypes = {
  date: PropTypes.object,
  base: PropTypes.string,
  quote: PropTypes.string,
  conversionRate: PropTypes.number,
};

export default LastConverted;
```

   
**app/components/Text/styles.js**
```javascript
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({
  smallText: {
    color: '$white',
    textAlign: 'center',
    fontSize: 12,
  },
});
```


**app/screens/Home.js**
```javascript
import React, { Component } from 'react';
import { StatusBar, KeyboardAvoidingView } from 'react-native';

import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
import { InputWithButton } from '../components/TextInput';
import { ClearButton } from '../components/Button';
import { LastConverted } from '../components/Text';
 
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
        <LastConverted
          date={TEMP_LAST_CONVERTED}
          base={TEMP_BASE_CURRENCY}
          quote={TEMP_QUOTE_CURRENCY}
          conversionRate={TEMP_CONVERSION_RATE}
        />
        <ClearButton onPress={this.handleSwapCurrency} text="Reverse Currencies" />
      </Container>
    );
  }
}
```