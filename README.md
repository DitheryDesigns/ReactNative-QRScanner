# React Native "expo-camera/next"

This project demonstrates the use of the `expo-camera/next` module in a React Native application managed by Expo. It includes features for handling camera permissions and toggling the camera between front and back.

## Installation

Before running the app, you need to install the necessary package:

```bash
expo install expo-camera
```

# Setup for Web Applications

If creating a web app in React Native, you will need to use polyfills for some functionality to work as expected. You can skip the poly fill section if it isn't needed.

## Install Polyfill:

```bash
expo install crypto-browserify
```

## Setup fallback in webpack.config.js for polyfill:
If you don't already have a webpack.config.js in your project directory, create one. Import Expo's default Webpack config and modify it to include your polyfills. Here’s an example of how you might set this up:

```javascript
const createExpoWebpackConfigAsync = require('@expo/webpack-config');

module.exports = async function(env, argv) {
  const config = await createExpoWebpackConfigAsync(env, argv);

  // Add a fallback for the crypto module
  config.resolve.fallback = config.resolve.fallback || {};
  config.resolve.fallback.crypto = require.resolve('crypto-browserify');

  return config;
};
This configuration ensures that whenever something requires crypto, crypto-browserify is used if crypto is not found in list of dependencies (A fallback).
```

## Usage

The main component in the app is the `App` function which manages the camera settings and user permissions.

### App.js

```javascript
import { CameraView, useCameraPermissions } from 'expo-camera/next';
import { useState } from 'react';
import { Button, StyleSheet, Text, TouchableOpacity, View } from 'react-native';

export default function App() {
  const [facing, setFacing] = useState('back'); // State to toggle camera facing direction
  const [permission, requestPermission] = useCameraPermissions(); // Hook to request camera permissions

  // Handling permission state loading
  if (!permission) {
    return <View />;
  }

  // Display a message and button to request permissions if not granted
  if (!permission.granted) {
    return (
      <View style={styles.container}>
        <Text style={{ textAlign: 'center' }}>We need your permission to show the camera</Text>
        <Button onPress={requestPermission} title="grant permission" />
      </View>
    );
  }

  // Function to toggle the camera facing direction
  function toggleCameraFacing() {
    setFacing(current => (current === 'back' ? 'front' : 'back'));
  }

  // Function called when a barcode is scanned
  const handleCodeScanned = (result) => {
    console.log(result.data);
  }

  // Main camera view setup
  return (
    <View style={styles.container}>
      <CameraView barcodeScannerSettings={{
        barcodeTypes: ["qr"],
      }}
      onBarcodeScanned={handleCodeScanned}
      style={styles.camera} facing={facing}>
        <View style={styles.buttonContainer}>
          <TouchableOpacity style={styles.button} onPress={toggleCameraFacing}>
            <Text style={styles.text}>Flip Camera</Text>
          </TouchableOpacity>
        </View>
      </CameraView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  camera: {
    flex: 1,
  },
  buttonContainer: {
    flex: 1,
    flexDirection: 'row',
    backgroundColor: 'transparent',
    margin: 64,
  },
  button: {
    flex: 1,
    alignSelf: 'flex-end',
    alignItems: 'center',
  },
  text: {
    fontSize: 24,
    fontWeight: 'bold',
    color: 'white',
  },
});
```


## Functionality

- **Request Camera Permission**: The app initially requests the user to grant camera permission.
- **Toggle Camera**: Users can toggle between the front and back cameras.
- **Scan QR Codes**: The camera scans for QR codes and logs the data to the console.
