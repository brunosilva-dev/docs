
# React Native Documentation

## References
- [React Native Rocketseat Guide](https://react-native.rocketseat.dev/)

## Project Initialization
- React Native projects can be created using either **EXPO** or **CLI**.
- Example to create a project with **Expo**:  
  ```bash
  expo init imhere --npm
  ```
  Here, `npm` is the package manager.

- After creating the project, navigate to the project’s root directory and install dependencies:  
  ```bash
  npm install
  ```

## Running the Project
- Start the project using the following command in the root directory:  
  ```bash
  npx expo start
  ```

## Project Structure
- Ensure the following directory structure:
  - Create a `src` folder (if it doesn't exist).
  - Inside `src`, create a `screens` folder to store all UI components.

## Writing Components
- In `.tsx` files, ensure to use `imports` and `exports` properly.
- Use `<>`, `<Fragment>`, or `<View>` tags to encapsulate elements in the `return` function.

### Styling with StyleSheet
- Import the StyleSheet module:
  ```tsx
  import { StyleSheet } from 'react-native';
  ```
- Create a style object:
  ```tsx
  const styles = StyleSheet.create({
      container: {
          // Add styling rules here
      },
  });
  ```
- Apply styles to elements:  
  ```tsx
  <View style={styles.container} />
  ```

### Forms and Input
- Use `<TextInput>` for data inputs, importing it from React Native:
  ```tsx
  import { TextInput } from 'react-native';
  <TextInput style={styles.input} placeholder="Enter text" />
  ```

- Use `<TouchableOpacity>` for buttons:
  ```tsx
  import { TouchableOpacity } from 'react-native';
  <TouchableOpacity onPress={handlePress}>
      <Text>Click Me</Text>
  </TouchableOpacity>
  ```

- Group input and buttons for horizontal alignment:
  ```tsx
  <View style={styles.form}>
      <TextInput style={styles.input} placeholder="Name" />
      <TouchableOpacity style={styles.button}>
          <Text>+</Text>
      </TouchableOpacity>
  </View>
  ```

## Scrollable Views
- Import `ScrollView` or `FlatList` for lists:
  ```tsx
  import { ScrollView } from 'react-native';
  <ScrollView>
      {data.map(item => <Text key={item}>{item}</Text>)}
  </ScrollView>
  ```
  - Hide the scrollbar:  
    ```tsx
    <ScrollView showsVerticalScrollIndicator={false} />
    ```

- Using `FlatList` for optimized lists:
  ```tsx
  import { FlatList } from 'react-native';
  <FlatList
      data={data}
      keyExtractor={(item) => item.id}
      renderItem={({ item }) => <Text>{item.name}</Text>}
  />
  ```

- Add an empty list message:
  ```tsx
  <FlatList
      ListEmptyComponent={() => <Text>No items available.</Text>}
  />
  ```

## Hooks
### useState
- Import `useState`:
  ```tsx
  import { useState } from 'react';
  const [state, setState] = useState([]);
  ```
- Update state:
  ```tsx
  setState(prevState => [...prevState, "New Item"]);
  ```

## Organizing with Babel
- Install `babel-plugin-module-resolver`:
  ```bash
  npm install --save-dev babel-plugin-module-resolver
  ```
- Configure `babel.config.js`:
  ```js
  module.exports = {
      presets: ["babel-preset-expo"],
      plugins: [
          [
              "module-resolver",
              {
                  alias: {
                      "@assets": "./src/assets",
                      "@components": "./src/components",
                      "@screens": "./src/screens",
                  },
              },
          ],
      ],
  };
  ```

- Update `tsconfig.json` paths:
  ```json
  {
      "compilerOptions": {
          "baseUrl": "./",
          "paths": {
              "@assets/*": ["./src/assets/*"],
              "@components/*": ["./src/components/*"]
          }
      }
  }
  ```
