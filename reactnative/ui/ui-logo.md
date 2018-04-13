

# [](#header-1) Logo : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar um componente Logo;
- aprenseder mostrar uma imagem.

### [](#header-3) componentes react native

1. [Dimensions](https://facebook.github.io/react-native/docs/dimensions.html)
2. [Image](https://facebook.github.io/react-native/docs/image.html): é um componente para mostrar imagens.
3. [ImageBackground](https://facebook.github.io/react-native/docs/images.html#background-image-via-nesting): é o mesmo que Image mas pode ser adicionado componentes filhos.
3. [Text](https://facebook.github.io/react-native/docs/text.html): é um componente para mostrar textos.
4. [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.


## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-03-app)

[imagens](https://www.filepicker.io/api/file/zl3iLVbSRR6Kn1b5Gvr2)

**app/components/Logo/Logo.js**
```jsx
import React from 'react';
import { View, Text, ImageBackground, Image } from 'react-native';

import styles from './styles';

const Logo = () => (
  <View style={styles.container}>
    <ImageBackground
      resizeMode="contain"
      style={styles.containerImage}
      source={require('./images/background.png')}
    >
      <Image resizeMode="contain" style={styles.logo} source={require('./images/logo.png')} />
    </ImageBackground>
    <Text style={styles.text}>Currency Converter</Text>
  </View>
);

export default Logo;
```

**app/components/Logo/index.js**
```jsx
import Logo from './Logo';
import styles from './styles';

export { Logo, styles };
```


**app/components/Logo/styles.js**
```jsx
import { Dimensions } from 'react-native';
import EStyleSheet from 'react-native-extended-stylesheet';

const imageWidth = Dimensions.get('window').width / 2;

export default EStyleSheet.create({
  container: {
    alignItems: 'center',
  },
  containerImage: {
    alignItems: 'center',
    justifyContent: 'center',
    width: imageWidth,
    height: imageWidth,
  },
  logo: {
    width: imageWidth / 2,
  },
  text: {
    color: '$white',
    fontSize: 28,
    letterSpacing: -0.5,
    marginTop: 15,
    fontWeight: '600',
  },
});
```

**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Home from './screens/Home';

EStyleSheet.build({
    $primaryBlue: '#4F6D7A',
    $white: '#FFFFFF'
});
 
export default () => <Home />;
```

**app/screens/Home.js**
```jsx
import { StatusBar } from 'react-native';
 
import { Container } from '../components/Container';
import { Logo } from '../components/Logo';
 
export default () => (
   <Container>
     <StatusBar translucent={false} barStyle="light-content" />
    <Logo />
   </Container>
);
```
