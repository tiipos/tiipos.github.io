

# [](#header-1) List item : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- Melhorar a visualização da tela CurrencyList;
- Criar um componente novo ```List/ListItem```;
- Usar o componente ```List/ListItem``` para renderizar em ```FlatList```.

### [](#header-3) componentes react native

- [FlatList](https://facebook.github.io/react-native/docs/flatlist.html): compomente performático e multiplataforma para apresentar dados em lista.
- [Image](https://facebook.github.io/react-native/docs/image.html): é um componente para mostrar imagens.
- [PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html): é uma biblioteca para checagem de tipos.
- [StatusBar](https://facebook.github.io/react-native/docs/statusbar.html): é um componente que controla a barra de status do aplicativo.
- [Text](https://facebook.github.io/react-native/docs/text.html): é um componente para mostrar textos.
- [TouchableHighlight](https://facebook.github.io/react-native/docs/touchablehighlight.html): é um wrapper para que as visualizações respondam adequadamente aos toques.
- [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.


## [](#header-2) Sumário

1. Inicie o app e abra no dispositivo;
2. Crie os diretórios ```app/components/List``` e ```app/components/List/images```
3. Crie no diretório ```app/components/List``` os arquivos ```index.js```, ```styles.js```, ```ListItem.js```, ```Icon.js```, ```Separator.js```
4. Edite o arquivo ```app/components/List/styles.js```
5. Edite o arquivo ```app/components/List/Icon.js```
6. Edite o arquivo ```app/components/List/Separator.js```
7. Edite o arquivo ```app/components/List/ListItem.js```
8. Edite o arquivo ```app/components/List/index.js```
9. Edite o arquivo ```app/index.js```
10. Edite o arquivo ```app/screens/CurrencyList.js```
11. Veja o resultado final


### [](#header-3) Passo 1. Inicie o app e abra no dispositivo ```app/components/Header```

```sh
cd CurrencyConverter
yarn start
```


### [](#header-3) Passo 2. Crie os diretórios ```app/components/List``` e ```app/components/List/images```

```sh
mkdir app/components/List
mkdir app/components/List/images
```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 3. Crie no diretório ```app/components/List``` os arquivos

```sh
touch app/components/List/index.js
touch app/components/List/styles.js
touch app/components/List/ListItem.js
touch app/components/List/Icon.js
touch app/components/List/Separator.js
```


### [](#header-3) Passo 4. Edite o arquivo ```app/components/List/styles.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 5. Edite o arquivo ```app/components/List/Icon.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 6. Edite o arquivo ```app/components/List/Separator.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 7. Edite o arquivo ```app/components/List/ListItem.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 8. Edite o arquivo ```app/components/List/index.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 9. 9. Edite o arquivo ```app/index.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 10. Edite o arquivo ```app/screens/CurrencyList.js```

Com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 11. Veja o resultado final










## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-12-app)

[imagens](https://www.filepicker.io/api/file/aA2ThTDNSSGoZ3JjMTtks)

**app/components/List/index.js**
```jsx
import ListItem from './ListItem';
import styles from './styles';
import Separator from './Separator';
import Icon from './Icon';

export { ListItem, styles, Separator, Icon };
```



**app/components/List/Icon.js**
```jsx
import PropTypes from 'prop-types';
import React from 'react';
import { View, Image } from 'react-native';

import styles from './styles';

const Icon = ({ visible, checkmark }) => {
  if (visible) {
    const iconStyles = [styles.icon];
    if (visible) {
      iconStyles.push(styles.iconVisible);
    }

    return (
      <View style={iconStyles}>
        {checkmark
          ? <Image
            source={require('./images/check.png')}
            style={styles.checkIcon}
            resizeMode="contain"
          />
          : null}
      </View>
    );
  }

  return <View style={styles.icon} />;
};

Icon.propTypes = {
  visible: PropTypes.bool,
  checkmark: PropTypes.bool,
};

export default Icon;
```



**app/components/List/ListItem.js**
```jsx
import PropTypes from 'prop-types';
import React from 'react';
import { View, Text, TouchableHighlight } from 'react-native';

import styles from './styles';
import Icon from './Icon';

const ListItem = ({ text, onPress, checkmark = true, selected = false, visible = true }) => (
  <TouchableHighlight onPress={onPress} underlayColor={styles.$underlayColor}>
    <View style={styles.row}>
      <Text style={styles.text}>{text}</Text>
      {selected ? <Icon visible={visible} checkmark={checkmark} /> : <Icon />}
    </View>
  </TouchableHighlight>
);

ListItem.propTypes = {
  text: PropTypes.string,
  onPress: PropTypes.func,
  checkmark: PropTypes.bool,
  selected: PropTypes.bool,
  visible: PropTypes.bool,
};

export default ListItem;
```



**app/components/List/Separator.js**
```jsx
import React from 'react';
import { View } from 'react-native';

import styles from './styles';

const Separator = () => <View style={styles.separator} />;

export default Separator;
```



**app/components/List/styles.js**
```jsx
import { StyleSheet } from 'react-native';
import EStyleSheet from 'react-native-extended-stylesheet';

export default EStyleSheet.create({
  $underlayColor: '$border',
  row: {
    paddingHorizontal: 20,
    paddingVertical: 16,
    justifyContent: 'space-between',
    alignItems: 'center',
    flexDirection: 'row',
    backgroundColor: '$white',
  },
  text: {
    color: '$darkText',
    fontSize: 16,
  },
  separator: {
    backgroundColor: '$border',
    height: StyleSheet.hairlineWidth,
    flex: 1,
    marginLeft: 20,
  },
  icon: {
    backgroundColor: 'transparent',
    borderRadius: 15,
    width: 30,
    height: 30,
    alignItems: 'center',
    justifyContent: 'center',
  },
  iconVisible: {
    backgroundColor: '$primaryBlue',
  },
  checkIcon: {
    width: 18,
  },
});
```



**app/screens/CurrencyList.js**
```jsx
import React, { Component } from 'react';
import { FlatList, StatusBar, View } from 'react-native';

import { ListItem, Separator } from '../components/List';
import currencies from '../data/currencies';

const TEMP_CURRENT_CURRENCY = 'CAD';

class CurrencyList extends Component {
  handlePress = () => {
    console.log('row press');
  };

  render() {
    return (
      <View style={{ flex: 1 }}>
        <StatusBar translucent={false} barStyle="light-content" />
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
          ItemSeparatorComponent={Separator}
        />
      </View>
    );
  }
}

export default CurrencyList;
```



**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import CurrencyList from './screens/CurrencyList';

EStyleSheet.build({
  $primaryBlue: '#4F6D7A',
  $white: '#FFFFFF',
  $lightGray: '#F0F0F0',
  $border: '#E2E2E2',
  $inputText: '#797979',
  $darkText: '#343434',
});

export default () => <CurrencyList />;
```