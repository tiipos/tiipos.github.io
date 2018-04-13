

# [](#header-1) Option screen : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar uma nova tela com opções do app.

### [](#header-3) componentes react native

- [Ionicons](https://docs.expo.io/versions/latest/guides/icons#expovector-icons): é o componente para selecionar qual ícon de @expo/vector-icon renderizar.
- [ScrollView](https://facebook.github.io/react-native/docs/scrollview.html)
- [StatusBar](https://facebook.github.io/react-native/docs/statusbar.html): é um componente que controla a barra de status do aplicativo.
- [Platform](https://facebook.github.io/react-native/docs/platform-specific-code.html): é uma biblioteca para detectar a plataforma na qual o app esta sendo executado.
- [FlatList](https://facebook.github.io/react-native/docs/flatlist.html): compomente performático e multiplataforma para apresentar dados em lista.


## [](#header-2) Sumário

1. Inicie o app e abra no dispositivo;
2. Edite o arquivo de tela ```app/components/List/ListItem.js```
3. Crie e edite o arquivo de tela ```app/screens/Options.js```
4. Edite o arquivo ```app/index.js```


### [](#header-3) Passo 1. Inicie o app e abra no dispositivo

```sh
cd CurrencyConverter
yarn start
```


### [](#header-3) Passo 2. Edite o arquivo de tela ```app/components/List/ListItem.js```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 3. Crie e edite o arquivo de tela ```app/screens/Options.js```

```sh
touch app/screens/Options.js
```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 4. Edite o arquivo ```app/index.js```

Com editor copie o conteúdo do arquivo do final desta página.
Será substituído a apresentação da tela ```CurrencyList``` pela tela ```Options```












## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-13-app)


**app/components/List/ListItem.js**
```jsx
import PropTypes from 'prop-types';
import React from 'react';
import { View, Text, TouchableHighlight } from 'react-native';

import styles from './styles';
import Icon from './Icon';
 
const ListItem = ({
  text,
  onPress,
  checkmark = true,
  selected = false,
  visible = true,
  customIcon = null,
}) => (
   <TouchableHighlight onPress={onPress} underlayColor={styles.$underlayColor}>
     <View style={styles.row}>
      <Text style={styles.text}>{text}</Text>
      {selected ? <Icon visible={visible} checkmark={checkmark} /> : <Icon />}
      {customIcon}
     </View>
   </TouchableHighlight>
 );

ListItem.propTypes = {
  text: PropTypes.string,
  onPress: PropTypes.func,
  checkmark: PropTypes.bool,
  selected: PropTypes.bool,
  visible: PropTypes.bool,
  customIcon: PropTypes.element,
 };
 
 export default ListItem;
 ```



**app/screens/Options.js**
```jsx
import React, { Component } from 'react';
import { ScrollView, StatusBar, Platform } from 'react-native';
import { Ionicons } from '@expo/vector-icons';
import { ListItem, Separator } from '../components/List';

const ICON_PREFIX = Platform.OS === 'ios' ? 'ios' : 'md';
const ICON_COLOR = '#868686';
const ICON_SIZE = 23;

class Options extends Component {
  handlePressThemes = () => {
    console.log('press themes');
  };

  handlePressSite = () => {
    console.log('press site');
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
export default Options;
```



**app/index.js**
```jsx
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Options from './screens/Options';

EStyleSheet.build({
  $primaryBlue: '#4F6D7A',
  $white: '#FFFFFF',
  $lightGray: '#F0F0F0',
  $border: '#979797',
  $inputText: '#797979',
  $darkText: '#343434',
});

export default () => <Options />;
```
