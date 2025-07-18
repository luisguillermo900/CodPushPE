# MiAppCodePushPE

Este proyecto fue creado con [React Native](https://reactnative.dev) y usa `react-native-code-push` para actualizaciones OTA (Over-The-Air), permitiendo distribuir nuevas versiones sin necesidad de Play Store.

---

## Requisitos previos

Antes de comenzar, asegúrate de tener instalado lo siguiente:

- Node.js 18.x o superior
- Android Studio (con emulador o dispositivo físico conectado)
- JDK 11 o superior
- React Native CLI:

```bash
npm install -g react-native-cli
```

- App Center CLI:

```bash
npm install -g appcenter-cli
```

---

## Instalación del proyecto

```bash
npx react-native init MiAppCodePushPE
cd MiAppCodePushPE
```

Instala las dependencias necesarias:

```bash
npm install react-native-code-push@8.0.4             appcenter             appcenter-analytics             appcenter-crashes
```

---

## Configuración de Android
### 1. Clave de CodePush

Edita el archivo `android/app/build.gradle` y agrega la clave:

```gradle
android {
    ...
    defaultConfig {
        ...
        resValue "string", "CodePushDeploymentKey", '"TU_DEPLOYMENT_KEY"'
    }
}
```

### 2. MainApplication.java

Edita `android/app/src/main/java/com/miappcodepushpe/MainApplication.java`:

```java
import com.microsoft.codepush.react.CodePush;

@Override
protected String getJSBundleFile() {
  return CodePush.getJSBundleFile();
}

@Override
protected List<ReactPackage> getPackages() {
  return Arrays.<ReactPackage>asList(
      new MainReactPackage(),
      new CodePush(BuildConfig.CodePushDeploymentKey, getApplicationContext(), BuildConfig.DEBUG)
  );
}
```

---

## 🧹 Limpiar y reconstruir

```bash
cd android
./gradlew clean
cd ..
```

---

## Ejecutar la app

### Inicia Metro:

```bash
npm start
```

### En otra terminal:

```bash
npx react-native run-android
```

---

## Código de ejemplo (`App.js` o `App.tsx`)

```tsx
import React, { useEffect } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import codePush from 'react-native-code-push';

let codePushOptions = { checkFrequency: codePush.CheckFrequency.ON_APP_RESUME };

const App = () => {
  useEffect(() => {
    codePush.sync({
      updateDialog: {
        title: "Actualización disponible",
        optionalUpdateMessage: "¿Deseas instalar la nueva versión?",
        optionalIgnoreButtonLabel: "Luego",
        optionalInstallButtonLabel: "Actualizar"
      },
      installMode: codePush.InstallMode.IMMEDIATE
    });
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Versión inicial v1.0</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, justifyContent: 'center', alignItems: 'center' },
  text: { fontSize: 18 }
});

export default codePush(codePushOptions)(App);
```

---

## Subir una actualización OTA

1. Haz cambios en tu app.
2. Luego ejecuta:

```bash
appcenter codepush release-react -a TU_USUARIO/TU_APP -d Staging
```

> Asegúrate de reemplazar `TU_USUARIO/TU_APP` con tu nombre de usuario y nombre de la app en App Center.

---

## Recursos útiles

- [Documentación oficial de React Native](https://reactnative.dev/docs/getting-started)
- [react-native-code-push en GitHub](https://github.com/microsoft/react-native-code-push)
- [App Center](https://appcenter.ms)
