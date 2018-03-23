

# [](#header-1) Logo : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar um componente Logo;
- aprenseder mostrar uma imagem.

## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/handlebar-labs---ui-logo)

**app/components/Logo/Logo.js**
```javascript
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
```javascript
import Logo from './Logo';
import styles from './styles';

export { Logo, styles };
```


**app/components/Logo/styles.js**
```javascript
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
```javascript
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
```javascript
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
