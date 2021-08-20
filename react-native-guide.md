# React Native Guide

[toc]

### Installation

```bash
# install expo cli
> npm i expo-cli -g

# select 
```



### Setup project

```bash
> npx react-native init projectname
```





### Components

Docs reference for components [here](https://reactnative.dev/docs/components-and-apis#basic-components)

```jsx
import React from "react";
import {View, Text, StyleSheet, TextInput, Button} from "react-native"

export default function App() {
   return (
   	<View style={styles.container}>
         <Text>This is the text</Text>
         <View>
         	<TextInput  />
            <Button title="Click" />
         </View>
   	</View>
   )
}

const styles = StyleSheet.create({
   container: {
      flex: 1,
      backgroundColor: "#f0f",
      alignItems: "center"
   }
});
```



**Notes**

- Text component is must when you want to add text. Else errors
- View component is like div
- Flexbox reference [docs](https://reactnative.dev/docs/flexbox)



### Create Scrollable View

It renders all the items in advance. Thus if there are too many elements, it will slow down the app.

```jsx
import {ScrollView} from "react-native";

function App() {
   return (
   	<ScrollView>
      	{/*a long list maybe*/}
      </ScrollView>
   )
}
```



### FlatList

```jsx
import {FlatList} from "react-native";

function App() {
   const [arr, setArr] = useState([
      {id: 1, name: "one"},
      {id: 2, name: "two"}
   ])
   
   return (
   	<View>
			<FlatList 
            keyExtractor={(item, i) => item.id}
            data={arr}
            renderItem={itemData => (
            	<View>
               	<Text>{itemData.item.name}</Text>
               </View>
            )}
          />      	
      </View>
   )
}
```



**Notes**

- flatlist automatically adds key prop but the condition is array should have objects with key property. 
- keyextractor can be used to define custom key in item
- marginVertical property for adding top bottom margin



### Images

```jsx
import { View, Image, StyleSheet } from 'react-native';

<Image
  style={styles.img}
  source={require("./assets/logo.png")}
/>
```





### States & Events

```jsx
import React, {useState, useEffect} from "react";
import {View, Text, StyleSheet, TextInput, Button} from "react-native"

export default function App() {
   const [input, setInput] = useState("");
   
   const handleInput = (enteredText) => {
      setInput(enteredText);
   }
   
   return (
   	<View style={styles.container}>
         <Text>This is the text</Text>
         <View>
         	<TextInput onChangeText={handleInput} />
            <Button title="Click" />
         </View>
   	</View>
   )
}
```



**Notes**

- Button do not support styles. Use View as wrapper to style them.



### Touchable components

Components are not clickable by default in react. So you cannot add onPress event on all components. For this touchable component is required. 

```jsx
import {TouchableOpacity} from "react-native";

function App() {
   return (
   	<TouchableOpacity onPress={handlePress}>
      	<Text>Clickable</Text>
      </TouchableOpacity>
   )
}
```



### Inputs, keyboard, alerts

```jsx
import {
	TextInput,
	Button,
	StyleSheet,
	TouchableWithoutFeedback, // for clicking anywhere and close keyboard
	Keyboard,
	Alert
} from "react-native";

const App = () => {
   const [enteredValue, setEnteredValue] = useState("");
   
   const handleInput = (inputText) => setEnteredValue(inputText);
   
   const handleSubmit = () => {
      // make api req. and reset input val.
      Keyboard.dismiss(); // close keyboard
   }
   
   return (
      <TouchableWithoutFeedback
			onPress={() => Keyboard.dismiss()}
		>
         <View>
            <TextInput
               blurOnSubmit
               autoCapitalize="none"
               autoCorrect={false}
               keyboardType="number-pad"
               maxLength={2}
               onChangeText={handleInput}
               value={enteredValue}
            />
            <Button 
               title="Confirm" 
               onPress={handleSubmit} 
               color={Colors.primary}
            />
         </View>
      </TouchableWithoutFeedback>
   )
   
}
```





### Modals

```jsx
import {Modal, Button} from "react-native";

function App() {
   const [showModal, setShowModal] = useState(false);
   
   const openModal = () => setShowModal(true);
   const closeModal = () => setShowModal(false);
   
   return (
      <View>
      	<Button title="Open Model" onPress={openModal} />
         <Modal visible={showModal} animationType="slide">
           <View
             style={{ 
               backgroundColor: "#ccc", 
               borderWidth: 1, 
               borderColor: "black", 
               padding: 40 
             }}
           >
             <Text style={{ marginBottom: 40 }}>I am a modal</Text>
             <Button 
                style={{ marginTop: 40 }} 
                title="Close" 
                onPress={closeModal} 
             />
           </View>
         </Modal>
      </View>
   )
}
```



### Custom Fonts

```bash
# if not installed
expo i expo-font
```



Steps: 

1. create a fonts folder in assets folder
2. put all the .ttf and .otf font files in it. 
3. Rest of loading work is done in App.js file

```jsx
import * as Font from "expo-font";
import {AppLoading} from "expo"; 

const fetchFonts = () => {
   return Font.loadAsync({ // it's a promise
    // identifier: path
      'open-sans': require("./assets/fonts/open-sans-regular.ttf"),
      'open-sans-bold': require("...")
   })
}

const App = () => {
   const [dataLoaded, setDataLoaded] = useEffect(false);
   
   // load assets 
   if(!dataLoaded) {
      return <AppLoading 
                startAsync={fetchFonts} 
                onFinish={() => setDataLoaded(true)}
                onError={err => console.log(err)}
             />
   }
   
   
   return "rest of the components"
}

const styles = StyleSheet.create({
   heading: {
      fontFamily: 'open-sans-bold', // same as identifier
   }
})
```



Notes:

- in startAsync, it should be a function that returns a promise.

- you cannot use fontWeight bold. load bold font separately.



### Responsiveness 

```jsx
import {Dimention} from "react-native";
```



### Navigation v5

```bash
# installs
npm i @react-navigation/native

# dependencies
expo install react-native-gesture-handler react-native-reanimated react-native-screens react-native-safe-area-context @react-native-community/masked-view


# stack navigator
npm i --save @react-navigation/stack @react-navigation/drawer

```



**CREATING ROUTES**

```jsx
import {NavigationContainer} from "@react-navigation/native";
import {createStackNavigator} from "@react-navigation/stack"

// create a stack
const MyStack = createStackNavigator();

const AppNavigator = props => {
   
   return (
      <NavigationContainer>
      	<MyStack.Navigator 
            initialRouteName="screen2"
            screenOptions={{
               headerStyle: {
                  backgroundColor: "royalblue"
               },
               headerTintColor: "#fff",
               headerTitleStyle: {
                  fontWeight: "bold"
               }
            }}
         >
         	<MyStack.Screen name="screen1" component={Screen1} 
               options={{ 
                  title: "My Homescreen", 
                  headerStyle: {
                     backgroundColor: "#f4511e"
                  },
                  headerTintColor: "yellow", // text color
                  headerTitleStyle: {
                     fontWeight: "bold"
                  }
               }} 
             />
            <MyStack.Screen name="screen2" component={Screen2} />
         </MyStack.Navigator>
      </NavigationContainer>
   )
}

export default AppNavigator;
```



**NAVIGATING BETWEEN PAGES**

```jsx
const HomeScreen = (props) => {
	return (
		<View style={styles.screen}>
			<Text style={styles.title}>Home</Text>
			<Button
				title="Go to About"
				onPress={() =>
					props.navigation.navigate("about", {
						chandu: "chandu"
					}) // route params
				}
			/>
		</View>
	);
};

const AboutScreen = (props) => {
   const { chandu } = props.route.params;
}

```



**USEFUL PROPERTIES**

````js
// hide header in stack navigator
<Stack.Screen options={{headerShown: false}} name="route-name" component={ScreenComponent} />
````







Notes:

- Reference video: [video 1](https://www.youtube.com/watch?v=nQVCkqvU1uE&t=904s)
- [Docs](https://reactnavigation.org/docs/nesting-navigators/)



### !Working with maps





### CSS properties

```javascript
cssProps = {
   flex: 1, // makes the view take full height
   justifyContent: "center", // space-around, space-between
   alignItems: "center",
   flexDirection: "row",
   
   padding: 10,
   paddingTop: 10,
   paddingVertical: 10, // top-bottom
   paddingHorizontal: 10, // left-right
   
   width: 80,
   maxWidth: "80%",
   height: 90,
   
   color: "white",
   backgroundColor: "blue",
   
   fontSize: 20,
   fontFamily: 'open-sans',
   fontWeight: "bold",
   
   borderRadius: 10,
   borderWidth: 1,
   borderColor: "black",
   borderBottomColor: "grey",
   borderBottomWidth: 1,
   
   shadowColor: "black",
   shadowOffset: { width: 0, height: 2 },
   shadowRadius: 6,
   shadowOpacity: 0.26,
   elevation: 8, // for android
}
```



## Custom components

### Button

```js
import React from "react";
import { View, StyleSheet, TouchableOpacity, Text } from "react-native";

const styles = StyleSheet.create({
})

const Button = ({ onPress, children, textStyles, style}) => (
  <TouchableOpacity onPress={onPress} style={style}>
      <Text style={textStyles}>{children}</Text>
  </TouchableOpacity>
);

export default Button;
```



## Exporting apk

```bash
# create upload key keystore file
keytool -v -storetype PKCS12 -keystore my-upload-key.keystore -alias valet-alias -keyalg RSA -keysize 2048 -validity 10000 -keypass pspotdev -storepass pspotdev -genkey -noprompt
```



2. paste that upload key file in `android/app`
3. paste the following in android/gradle.properties

```bash
MYAPP_UPLOAD_STORE_FILE=FILENAME
MYAPP_UPLOAD_KEY_ALIAS=ALIAS-NAME
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

4. paste the following in android/app/build.gradle file inside `signingconfigs`

```bash
release {
            if (project.hasProperty('MYAPP_UPLOAD_STORE_FILE')) {
                storeFile file(MYAPP_UPLOAD_STORE_FILE)
                storePassword MYAPP_UPLOAD_STORE_PASSWORD
                keyAlias MYAPP_UPLOAD_KEY_ALIAS
                keyPassword MYAPP_UPLOAD_KEY_PASSWORD
            }
        }

// also change this inside build types
buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
```



5. becoz we ejected from expo we need the following steps

```bash
> expo login
> expo eject
> npm install

// add icons and splash screen before next steps
> expo publish
> npm install
```



6. create release apk

```bash
// first
> cd android
> gradlew bundleRelease

// second
> npx react-native run-android --variant=release
```



7. getting the apk files

```bash
aab file: project\android\app\build\outputs\bundle\release
apk files: project\android\app\build\outputs\apk\release
```



## google maps api keys 

in androidManifest.xml

```xml
<meta-data
     android:name="com.google.android.geo.API_KEY"
     android:value="AIzaSyB07OTkTUBKzw7LK-xxIuFDwQLHn0J0UpE"/>
```

