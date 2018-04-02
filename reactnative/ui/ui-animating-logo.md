

# [](#header-1) Logo animado : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- Animar o logo do aplicativo;
- Entendendo State e Props;
- Entendendo o ciclo de vida de Components React.

### [](#header-3) componentes react native

- [Component React](https://reactjs.org/docs/react-component.html)
- [Ciclo de vida de componentes React](https://reactjs.org/docs/react-component.html#the-component-lifecycle): construtor, componentDidMount, componentWillUnmount, render
- [Animated](https://facebook.github.io/react-native/docs/animated.html): é uma biblioteca para fazer animações.
- [Keyboard](https://facebook.github.io/react-native/docs/keyboard.html): é uma biblioteca para controlar eventos do teclado.
- [Platform](https://facebook.github.io/react-native/docs/platform-specific-code.html): é uma biblioteca para detectar a plataforma na qual o app esta sendo executado.
- [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html): é uma biblioteca para checagem de tipos.
- [StyleSheet](https://facebook.github.io/react-native/docs/stylesheet.html): é uma abstração similar a CSS.
- [Text](https://facebook.github.io/react-native/docs/text.html): é um componente para mostrar textos.
- [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.


## [](#header-2) Sumário

1. Inicie o app e abra no dispositivo
2. Edite o arquivo ```app/components/Logo/styles.js```;
3. Adicione a animação no arquivo ```app/components/Logo/Logo.js```;
4. Faça as imagens de background e logo animarem em paralelo, mais uma edição no arquivo ```app/components/Logo/Logo.js```;
5. Faça o código ```app/components/Logo/Logo.js``` portável;


### [](#header-3) Passo 1. Crie diretório ```app/components/Header```

```sh
cd CurrencyConverter
yarn start
```

### [](#header-3) Passo 2. Edite o arquivo ```app/components/Logo/styles.js```

**app/components/Logo/styles.js** coloque dimensões (grande e pequeno) para o as imagens
```javascript
import EStyleSheet from 'react-native-extended-stylesheet';
 
const imageWidth = Dimensions.get('window').width / 2;
 
export default EStyleSheet.create({
  $smallContainerSize: imageWidth / 2,
  $smallImageSize: imageWidth / 4,
  $largeContainerSize: imageWidth,
  $largeImageSize: imageWidth / 2,
  container: {
    alignItems: 'center',
  },
  containerImage: {
    alignItems: 'center',
    justifyContent: 'center',
    width: '$largeContainerSize',
    height: '$largeContainerSize',
  },
  logo: {
    width: '$largeImageSize',
    tintColor: '$primaryBlue',
  },
  text: {
    color: '$white',
```



### [](#header-3) Passo 3. Adicione a animação

**observações**:
- O código-fonte difere das plataformas _Android_ (```keyboardDidShow``` e ```keyboardDidHide```) e _iOS_ (```keyboardWillShow``` e ```keyboardWillHide```).

**app/components/Logo/Logo.js** _Android_
```javascript
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { View, Text, Keyboard, Animated, StyleSheet } from 'react-native';
 
import styles from './styles';

const ANIMATION_DURATION = 250;

class Logo extends Component {
  static propTypes = {
    tintColor: PropTypes.string,
  };

  constructor(props) {
    super(props);
    this.state = {
      containerImageWidth: new Animated.Value(styles.$largeContainerSize),
      imageWidth: new Animated.Value(styles.$largeImageSize),
    };
  }

  componentDidMount() {
    this.keyboardDidShowListener = Keyboard.addListener(
      'keyboardDidShow',
      this.keyboardWillShow,
    );
    this.keyboardDidHideListener = Keyboard.addListener(
      'keyboardDidHide',
      this.keyboardWillHide,
    );
  }

  componentWillUnmount() {
    this.keyboardDidShowListener.remove();
    this.keyboardDidHideListener.remove();
  }

  keyboardWillShow = () => {
    Animated.timing(this.state.containerImageWidth, {
      toValue: styles.$smallContainerSize,
      duration: ANIMATION_DURATION,
    }).start();
    Animated.timing(this.state.imageWidth, {
      toValue: styles.$smallImageSize,
      duration: ANIMATION_DURATION,
    }).start();
  };

  keyboardWillHide = () => {
    Animated.timing(this.state.containerImageWidth, {
      toValue: styles.$largeContainerSize,
      duration: ANIMATION_DURATION,
    }).start();
    Animated.timing(this.state.imageWidth, {
      toValue: styles.$largeImageSize,
      duration: ANIMATION_DURATION,
    }).start();
  };

  render() {
    const containerImageStyles = [
      styles.containerImage,
      { width: this.state.containerImageWidth, height: this.state.containerImageWidth },
    ];
    const imageStyles = [
      styles.logo,
      { width: this.state.imageWidth },
      this.props.tintColor ? { tintColor: this.props.tintColor } : null,
    ];

    return (
      <View style={styles.container}>
        <Animated.View style={containerImageStyles}>
          <Animated.Image
            resizeMode="contain"
            style={[StyleSheet.absoluteFill, containerImageStyles]}
            source={require('./images/background.png')}
          />
          <Animated.Image
            resizeMode="contain"
            style={imageStyles}
            source={require('./images/logo.png')}
          />
        </Animated.View>
        <Text style={styles.text}>Currency Converter</Text>
      </View>
    );
  }
}
 
export default Logo;
```

ou

**app/components/Logo/Logo.js** _iOS_
```javascript
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { View, Text, Keyboard, Animated, StyleSheet } from 'react-native';
 
import styles from './styles';

const ANIMATION_DURATION = 250;

class Logo extends Component {
  static propTypes = {
    tintColor: PropTypes.string,
  };

  constructor(props) {
    super(props);
    this.state = {
      containerImageWidth: new Animated.Value(styles.$largeContainerSize),
      imageWidth: new Animated.Value(styles.$largeImageSize),
    };
  }

  componentDidMount() {
    this.keyboardDidShowListener = Keyboard.addListener(
      'keyboardWillShow',
      this.keyboardWillShow,
    );
    this.keyboardDidHideListener = Keyboard.addListener(
      'keyboardWillHide',
      this.keyboardWillHide,
    );
  }

  componentWillUnmount() {
    this.keyboardDidShowListener.remove();
    this.keyboardDidHideListener.remove();
  }

  keyboardWillShow = () => {
    Animated.timing(this.state.containerImageWidth, {
      toValue: styles.$smallContainerSize,
      duration: ANIMATION_DURATION,
    }).start();
    Animated.timing(this.state.imageWidth, {
      toValue: styles.$smallImageSize,
      duration: ANIMATION_DURATION,
    }).start();
  };

  keyboardWillHide = () => {
    Animated.timing(this.state.containerImageWidth, {
      toValue: styles.$largeContainerSize,
      duration: ANIMATION_DURATION,
    }).start();
    Animated.timing(this.state.imageWidth, {
      toValue: styles.$largeImageSize,
      duration: ANIMATION_DURATION,
    }).start();
  };

  render() {
    const containerImageStyles = [
      styles.containerImage,
      { width: this.state.containerImageWidth, height: this.state.containerImageWidth },
    ];
    const imageStyles = [
      styles.logo,
      { width: this.state.imageWidth },
      this.props.tintColor ? { tintColor: this.props.tintColor } : null,
    ];

    return (
      <View style={styles.container}>
        <Animated.View style={containerImageStyles}>
          <Animated.Image
            resizeMode="contain"
            style={[StyleSheet.absoluteFill, containerImageStyles]}
            source={require('./images/background.png')}
          />
          <Animated.Image
            resizeMode="contain"
            style={imageStyles}
            source={require('./images/logo.png')}
          />
        </Animated.View>
        <Text style={styles.text}>Currency Converter</Text>
      </View>
    );
  }
}
 
export default Logo;
```

### [](#header-3) Passo 4. Faça as imagens de background e logo animarem em paralelo


### [](#header-3) Passo 5. Faça o código portável;












## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-10-app)


**app/components/Logo/Logo.js**
```javascript
import PropTypes from 'prop-types';
import React, { Component } from 'react';
import { View, Text, Keyboard, Animated, Platform, StyleSheet } from 'react-native';
 
import styles from './styles';

const ANIMATION_DURATION = 250;

class Logo extends Component {
  static propTypes = {
    tintColor: PropTypes.string,
  };

  constructor(props) {
    super(props);
    this.state = {
      containerImageWidth: new Animated.Value(styles.$largeContainerSize),
      imageWidth: new Animated.Value(styles.$largeImageSize),
    };
  }

  componentDidMount() {
    const name = Platform.OS === 'ios' ? 'Will' : 'Did';
    this.keyboardDidShowListener = Keyboard.addListener(
      `keyboard${name}Show`,
      this.keyboardWillShow,
    );
    this.keyboardDidHideListener = Keyboard.addListener(
      `keyboard${name}Hide`,
      this.keyboardWillHide,
    );
  }

  componentWillUnmount() {
    this.keyboardDidShowListener.remove();
    this.keyboardDidHideListener.remove();
  }

  keyboardWillShow = () => {
    Animated.parallel([
      Animated.timing(this.state.containerImageWidth, {
        toValue: styles.$smallContainerSize,
        duration: ANIMATION_DURATION,
      }),
      Animated.timing(this.state.imageWidth, {
        toValue: styles.$smallImageSize,
        duration: ANIMATION_DURATION,
      }),
    ]).start();
  };

  keyboardWillHide = () => {
    Animated.parallel([
      Animated.timing(this.state.containerImageWidth, {
        toValue: styles.$largeContainerSize,
        duration: ANIMATION_DURATION,
      }),
      Animated.timing(this.state.imageWidth, {
        toValue: styles.$largeImageSize,
        duration: ANIMATION_DURATION,
      }),
    ]).start();
  };

  render() {
    const containerImageStyles = [
      styles.containerImage,
      { width: this.state.containerImageWidth, height: this.state.containerImageWidth },
    ];
    const imageStyles = [
      styles.logo,
      { width: this.state.imageWidth },
      this.props.tintColor ? { tintColor: this.props.tintColor } : null,
    ];

    return (
      <View style={styles.container}>
        <Animated.View style={containerImageStyles}>
          <Animated.Image
            resizeMode="contain"
            style={[StyleSheet.absoluteFill, containerImageStyles]}
            source={require('./images/background.png')}
          />
          <Animated.Image
            resizeMode="contain"
            style={imageStyles}
            source={require('./images/logo.png')}
          />
        </Animated.View>
        <Text style={styles.text}>Currency Converter</Text>
      </View>
    );
  }
}
 
export default Logo;
```


**app/components/Logo/styles.js**
```javascript
 import EStyleSheet from 'react-native-extended-stylesheet';
 
 const imageWidth = Dimensions.get('window').width / 2;
 
export default EStyleSheet.create({
  $smallContainerSize: imageWidth / 2,
  $smallImageSize: imageWidth / 4,
  $largeContainerSize: imageWidth,
  $largeImageSize: imageWidth / 2,
  container: {
    alignItems: 'center',
  },
  containerImage: {
    alignItems: 'center',
    justifyContent: 'center',
    width: '$largeContainerSize',
    height: '$largeContainerSize',
  },
  logo: {
    width: '$largeImageSize',
    tintColor: '$primaryBlue',
  },
  text: {
    color: '$white',
```