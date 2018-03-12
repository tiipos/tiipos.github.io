

# [](#header-1) Interface do usuário ReactNative

## [](#header-2) Parte 1

### [](#header-3) Introdução

- [React Native Extended StyleSheet](https://github.com/vitalets/react-native-extended-stylesheet): estender a forma (estilo) padrão do React Native
- [color](https://github.com/Qix-/color): manipulação de cores
- [moment](https://momentjs.com): locale, parser, validar, manipular e mostrar datas
- [expo icons](https://github.com/expo/vector-icons): icons vetoriais

```sh
yarn add react-native-extended-stylesheet color moment @expo/vector-icons
```

### [](#header-3) Container

**app/components/Container/Container.js**
```javascript
import PropTypes from 'prop-types';
import React from 'react';
import { View } from 'react-native';

import styles from './styles';

const Container = ({ children }) => (
  <View style={styles.container}>
    {children}
  </View>
);

Container.propTypes = {
  children: PropTypes.any,
};

export default Container;
```


**app/components/Container/index.js**
```javascript
import Container from './Container';
import styles from './styles';

export { Container, styles };
```

**app/components/Container/styles.js**
```javascript
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
    backgroundColor: '$primaryBlue',
  },
});
```


**app/screens/Home.js**
```javascript
import React from 'react';
import { StatusBar } from 'react-native';

import { Container } from '../components/Container';

export default () => (
  <Container>
    <StatusBar translucent={false} barStyle="light-content" />
  </Container>
);
```

**app/index.js**
```javascript
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Home from './screens/Home';

EStyleSheet.build({
  $primaryBlue: '#4F6D7A',
});

export default () => <Home />;
```

**App.js**
```javascript
import App from './app/index';
 
export default App;
```

### [](#header-3) Logo


### [](#header-3) Text input


### [](#header-3) Teclado


### [](#header-3) Botão


### [](#header-3) Estilo