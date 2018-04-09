

# [](#header-1) Themes screen : UI : ReactNative


## [](#header-2) Introdução

**Objetivos**:
- criar uma nova tela com opções do app.

### [](#header-3) componentes react native

- [Image](https://facebook.github.io/react-native/docs/image.html): é um componente para mostrar imagens.
- [Ionicons](https://docs.expo.io/versions/latest/guides/icons#expovector-icons): é o componente para selecionar qual ícon de @expo/vector-icon renderizar.
- [ScrollView](https://facebook.github.io/react-native/docs/scrollview.html)
- [StatusBar](https://facebook.github.io/react-native/docs/statusbar.html): é um componente que controla a barra de status do aplicativo.
- [TouchableHighlight](https://facebook.github.io/react-native/docs/touchablehighlight.html): é um wrapper para que as visualizações respondam adequadamente aos toques.
- [View](https://facebook.github.io/react-native/docs/view.html): é um container para outros componentes.


## [](#header-2) Sumário

1. Inicie o app e abra no dispositivo;
2. Edite o arquivo de tela ```app/components/List/Icon.js```
3. Edite o arquivo de tela ```app/components/List/ListItem.js```
4. Crie e edite o arquivo de tela ```app/screens/Themes.js```
5. Edite o arquivo ```app/index.js```


### [](#header-3) Passo 1. Inicie o app e abra no dispositivo 

```sh
cd CurrencyConverter
yarn start
```


### [](#header-3) Passo 2. Edite o arquivo ```app/components/List/Icon.js```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 3. Edite o arquivo ```app/components/List/ListItem.js```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 4. Crie e edite o arquivo de tela ```app/screens/Themes.js```

```sh
touch app/screens/Themes.js
```

com editor copie o conteúdo do arquivo do final desta página.


### [](#header-3) Passo 2. Edite o arquivo ```app/index.js```

Com editor copie o conteúdo do arquivo do final desta página.
Será substituído a apresentação da tela ```Options``` pela tela ```Themes```












## [](#header-2) Códigos-fonte

[snack expo](https://snack.expo.io/@leonardo-minora/tiipos-2018-rn-14-app)

**app/components/List/Icon.js**
```javascript
import PropTypes from 'prop-types';
import React from 'react';
import { View, Image } from 'react-native';

import styles from './styles';

const Icon = ({ visible, checkmark, iconBackground }) => {
  if (visible) {
    const iconStyles = [styles.icon];
    if (visible) {
      iconStyles.push(styles.iconVisible);
    }
    if (iconBackground) {
      iconStyles.push({ backgroundColor: iconBackground });
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
  iconBackground: PropTypes.string,
 };
 
 export default Icon;
 ```


**app/components/List/ListItem.js**
```javascript
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
  iconBackground,
}) => (
   <TouchableHighlight onPress={onPress} underlayColor={styles.$underlayColor}>
     <View style={styles.row}>
      <Text style={styles.text}>{text}</Text>
      {selected
        ? <Icon visible={visible} checkmark={checkmark} iconBackground={iconBackground} />
        : <Icon />}
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
  iconBackground: PropTypes.string,
 };
 
 export default ListItem;
 ```



**app/screens/Themes.js**
```javascript
import React, { Component } from 'react';
import { ScrollView, StatusBar } from 'react-native';
import EStyleSheet from 'react-native-extended-stylesheet';

import { ListItem, Separator } from '../components/List';

const styles = EStyleSheet.create({
  $blue: '$primaryBlue',
  $orange: '$primaryOrange',
  $green: '$primaryGreen',
  $purple: '$primaryPurple',
});

class Themes extends Component {
  handlePressTheme = (color) => {
    console.log('press theme');
  };

  render() {
    return (
      <ScrollView>
        <StatusBar translucent={false} barStyle="default" />
        <ListItem
          text="Blue"
          onPress={() => this.handlePressTheme(styles.$blue)}
          selected
          checkmark={false}
          iconBackground={styles.$blue}
        />
        <Separator />
        <ListItem
          text="Orange"
          onPress={() => this.handlePressTheme(styles.$orange)}
          selected
          checkmark={false}
          iconBackground={styles.$orange}
        />
        <Separator />
        <ListItem
          text="Green"
          onPress={() => this.handlePressTheme(styles.$green)}
          selected
          checkmark={false}
          iconBackground={styles.$green}
        />
        <Separator />
        <ListItem
          text="Purple"
          onPress={() => this.handlePressTheme(styles.$purple)}
          selected
          checkmark={false}
          iconBackground={styles.$purple}
        />
        <Separator />
      </ScrollView>
    );
  }
}
export default Themes;
```



**app/index.js**
```javascript
import React from 'react';
import EStyleSheet from 'react-native-extended-stylesheet';

import Themes from './screens/Themes';

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

export default () => <Themes />;
```
