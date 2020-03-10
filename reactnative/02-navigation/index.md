# React Native : Navegabilidade das telas <!-- omit in toc -->

## Sumário <!-- omit in toc -->

- [Instalando e configurando as ferramentas](#instalando-e-configurando-as-ferramentas)
  - [no smartphone](#no-smartphone)
  - [Laboratório de informática](#laborat%c3%b3rio-de-inform%c3%a1tica)
  - [Acessando código-fonte do app](#acessando-c%c3%b3digo-fonte-do-app)
  - [instalando as bibliotecas de navegação](#instalando-as-bibliotecas-de-navega%c3%a7%c3%a3o)
  - [instalando a biblioteca de armazenamento](#instalando-a-biblioteca-de-armazenamento)
  - [Inicie a aplicação mobile](#inicie-a-aplica%c3%a7%c3%a3o-mobile)
- [Projetando a nevagabilidade](#projetando-a-nevagabilidade)
- [Implementando as telas](#implementando-as-telas)
  - [Estilo temporário](#estilo-tempor%c3%a1rio)
  - [Tela de splash](#tela-de-splash)
  - [Tela principal](#tela-principal)
  - [Tela de Login](#tela-de-login)
  - [Tela de Perfil](#tela-de-perfil)
  - [índice das telas](#%c3%adndice-das-telas)
- [Implementando as Rotas](#implementando-as-rotas)
- [Agora modifique o aplicativo](#agora-modifique-o-aplicativo)

## Instalando e configurando as ferramentas

### no smartphone

Lembre de instalar o expo app, ver detalhes em https://expo.io/tools#client

### Laboratório de informática

**no computador do laboratório no linux, se ainda não tiver o nvm, node, yarn, expo-cli**

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

nvm install node

npm install --global yarn expo-cli

```

### Acessando código-fonte do app

**Clonando**

```console

```

**Atualizando**

### instalando as bibliotecas de navegação

[React Navigation](https://reactnavigation.org)

```bash
yarn add react-navigation react-navigation-stack react-native-gesture-handler
```

### instalando a biblioteca de armazenamento

[React Native Async Storage](https://github.com/react-native-community/async-storage)

```bash
yarn add @react-native-community/async-storage
```

### Inicie a aplicação mobile

```console
machine user:dir$ yarn start
yarn run v1.13.0
$ expo start
...

  To run the app with live reloading, choose one of:
  • Scan the QR code above with the Expo app (Android) or the Camera app (iOS).
  • Press a for Android emulator, or i for iOS simulator.
  • Press e to send a link to your phone with email.
  • Press s to sign in and enable more options.

 Expo  Press ? to show a list of all available commands.
Logs for your project will appear below. Press Ctrl+C to exit.
Tunnel ready.

```

## Projetando a nevagabilidade

## Implementando as telas

### Estilo temporário

edite o arquivo `./src/screens/styles.js`

```jsx
import React from "react";
import { StyleSheet } from "react-native";

export default styles = StyleSheet.create({
  container: {
    flex: 1,
    // backgroundColor: "#4F6D7A",
    alignItems: "center",
    justifyContent: "center"
  }
});
```

### Tela de splash

edite o arquivo `./src/screens/SplashScreen.js`

```jsx
import React, { Component } from "react";
import { ActivityIndicator, AsyncStorage, StatusBar, View } from "react-native";

import { APP_STACK, AUTH_STACK } from "../config/routes";

import styles from "./styles";

class SplashScreen extends React.Component {
  static navigationOptions = {
    title: "Welcome to the BeHappyWithMe!"
  };

  constructor() {
    super();
    this.initAsync();
  }

  // Fetch the token from storage then navigate to our appropriate place
  initAsync = async () => {
    const userToken = await AsyncStorage.getItem("userToken");
    console.log(userToken);

    // This will switch to the App screen or Auth screen and this loading
    // screen will be unmounted and thrown away.
    this.props.navigation.navigate(userToken ? APP_STACK : AUTH_STACK);
  };

  // Render any loading content that you like here
  render() {
    return (
      <View style={styles.container}>
        <ActivityIndicator />
        <StatusBar barStyle="default" />
      </View>
    );
  }
}

export default SplashScreen;
```

### Tela principal

edite o arquivo `./src/screens/HomeScreen.js`

```jsx
import React, { Component } from "react";
import { AsyncStorage, Button, View } from "react-native";

import { AUTH_STACK, PROFILE_SCREEN } from "../config/routes";
import styles from "./styles";

class HomeScreen extends Component {
  static navigationOptions = {
    title: "BeHappyWith.Me"
  };

  render() {
    return (
      <View style={styles.container}>
        <Button title="Perfil" onPress={this._showMoreApp} />
        <Button title="Conectar :)" onPress={this._signOutAsync} />
      </View>
    );
  }

  _showMoreApp = () => {
    this.props.navigation.navigate(PROFILE_SCREEN);
  };

  _signOutAsync = async () => {
    await AsyncStorage.clear();
    this.props.navigation.navigate(AUTH_STACK);
  };
}

export default HomeScreen;
```

### Tela de Login

edite o arquivo `./src/screens/SignInScreen.js`

```jsx
import React, { Component } from "react";
import { AsyncStorage, Button, View } from "react-native";

import { APP_STACK } from "../config/routes";
import styles from "./styles";

class SignInScreen extends Component {
  static navigationOptions = {
    title: "BeHappyWith.Me :: Login"
  };

  render() {
    return (
      <View style={styles.container}>
        <Button title="Sign in!" onPress={this._signInAsync} />
        <Button title="Sign up" />
        <Button title="Esqueci minha senha :-/" />
        <Button title="Continue disconectado" />
      </View>
    );
  }

  _signInAsync = async () => {
    await AsyncStorage.setItem("userToken", "abc");
    this.props.navigation.navigate(APP_STACK);
  };
}

export default SignInScreen;
```

### Tela de Perfil

edite o arquivo `./src/screens/ProfileScreen.js`

```jsx
import React, { Component } from "react";
import { AsyncStorage, Button, StatusBar, View } from "react-native";

import { AUTH_STACK } from "../config/routes";
import styles from "./styles";

class ProfileScreen extends React.Component {
  static navigationOptions = {
    title: "BeHappyWith.Me :: Perfil"
  };

  render() {
    return (
      <View style={styles.container}>
        <Button title="I'm done, sign me out" onPress={this._signOutAsync} />
        <StatusBar barStyle="default" />
      </View>
    );
  }

  _signOutAsync = async () => {
    await AsyncStorage.clear();
    this.props.navigation.navigate(AUTH_STACK);
  };
}

export default ProfileScreen;
```

### índice das telas

edite o arquivo `./src/screens/index.js`

```js
import HomeScreen from "./HomeScreen";
import ProfileScreen from "./ProfileScreen";
import SignInScreen from "./SignInScreen";
import SplashScreen from "./SplashScreen";

export { HomeScreen, ProfileScreen, SignInScreen, SplashScreen };
```

## Implementando as Rotas

edite o arquivo `./src/config/routes.js`

```js
import { createSwitchNavigator, createAppContainer } from "react-navigation";
import { createStackNavigator } from "react-navigation-stack";
import {
  HomeScreen,
  ProfileScreen,
  SignInScreen,
  SplashScreen
} from "../screens";

const APP_STACK = "App";
const AUTH_STACK = "Auth";

const SPLASH_SCREEN = "Splash";
const HOME_SCREEN = "Home";
const PROFILE_SCREEN = "Profile";
const SIGNIN_SCREEN = "Auth";

const AppStack = createStackNavigator({
  Home: HomeScreen,
  Profile: ProfileScreen
});
const AuthStack = createStackNavigator({ SignIn: SignInScreen });

export { APP_STACK, AUTH_STACK, HOME_SCREEN, PROFILE_SCREEN, SIGNIN_SCREEN };

export default createAppContainer(
  createSwitchNavigator(
    {
      Splash: SplashScreen,
      App: AppStack,
      Auth: AuthStack
    },
    {
      initialRouteName: SPLASH_SCREEN
    }
  )
);
```

## Agora modifique o aplicativo

edite o arquivo `./src/index.js`

```js
import AppContainer from "./config/routes";

export default AppContainer;
```
