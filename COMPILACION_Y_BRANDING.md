# Guía de Compilación y Branding para Kumi

## 📱 Cambios de Branding Realizados

### Nombre de la App
El nombre de la app ha sido cambiado a **"Kumi"** en los siguientes archivos:
- ✅ `android/app/src/main/AndroidManifest.xml` - Nombre visible en Android
- ✅ `ios/Runner/Info.plist` - Nombre visible en iOS (CFBundleDisplayName)
- ✅ `pubspec.yaml` - Descripción del proyecto

---

## 🎨 Cómo Cambiar el Icono/Logo de la App

### Para Android

Los iconos de Android se encuentran en:
```
android/app/src/main/res/
├── mipmap-mdpi/ic_launcher.png (48x48 px)
├── mipmap-hdpi/ic_launcher.png (72x72 px)
├── mipmap-xhdpi/ic_launcher.png (96x96 px)
├── mipmap-xxhdpi/ic_launcher.png (144x144 px)
└── mipmap-xxxhdpi/ic_launcher.png (192x192 px)
```

**Pasos para reemplazar los iconos:**

1. **Crea tu icono base** en alta resolución (1024x1024 px recomendado)
2. **Genera los tamaños necesarios** usando una herramienta como:
   - [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/icons-launcher.html)
   - [AppIcon.co](https://www.appicon.co/)
   - O manualmente con un editor de imágenes

3. **Reemplaza los archivos** `ic_launcher.png` en cada carpeta con tus nuevos iconos

4. **Opcional:** También puedes actualizar los iconos adaptativos en:
   ```
   android/app/src/main/res/mipmap-anydpi-v26/
   ```
   (Estos son para Android 8.0+ con iconos adaptativos)

### Para iOS

Los iconos de iOS se encuentran en:
```
ios/Runner/Assets.xcassets/AppIcon.appiconset/
```

**Tamaños requeridos para iOS:**

| Tamaño | Archivo | Uso |
|--------|---------|-----|
| 20x20 | 20.png, 40.png | Notificaciones |
| 29x29 | 29.png, 58.png, 87.png | Settings |
| 40x40 | 40.png, 80.png, 120.png | Spotlight |
| 60x60 | 120.png, 180.png | App Icon (iPhone) |
| 76x76 | 76.png, 152.png | App Icon (iPad) |
| 83.5x83.5 | 167.png | App Icon (iPad Pro) |
| 1024x1024 | 1024.png | App Store |

**Pasos para reemplazar los iconos:**

1. **Crea tu icono base** en 1024x1024 px
2. **Genera todos los tamaños** usando:
   - [AppIcon.co](https://www.appicon.co/)
   - [IconKitchen](https://icon.kitchen/)
   - O Xcode (File > New > File > App Icon Set)

3. **Reemplaza todos los archivos PNG** en la carpeta `AppIcon.appiconset/`

4. **El archivo `Contents.json`** ya está configurado correctamente, no necesitas modificarlo

**Nota:** Asegúrate de que todos los iconos tengan fondo transparente o sólido según tu diseño, y que sean cuadrados (iOS redondea automáticamente las esquinas).

---

## 🔨 Compilación para Android

### Requisitos Previos

1. **Flutter SDK** instalado y configurado
   ```bash
   flutter doctor
   ```

2. **Android Studio** o **Android SDK** instalado
   - Android SDK Platform-Tools
   - Android SDK Build-Tools
   - Android SDK Command-line Tools

3. **Java Development Kit (JDK)** 11 o superior
   ```bash
   java -version
   ```

4. **Keystore para firmar la app** (solo para release)
   - El proyecto está configurado para usar un keystore desde `environment/key.properties`
   - Si no existe, necesitarás crear uno (ver sección de Release)

### Compilar para Desarrollo (Debug)

```bash
# Verificar que todo esté configurado
flutter doctor

# Obtener dependencias
flutter pub get

# Compilar APK de debug
flutter build apk --debug

# O compilar App Bundle (recomendado para Play Store)
flutter build appbundle --debug
```

El APK se generará en: `build/app/outputs/flutter-apk/app-debug.apk`

### Compilar para Producción (Release)

#### 1. Crear Keystore (si no existe)

```bash
keytool -genkey -v -keystore ~/kumi-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias kumi
```

#### 2. Crear archivo de configuración

Crea el archivo `environment/key.properties` con:
```properties
storePassword=<tu-password-del-keystore>
keyPassword=<tu-password-del-keystore>
keyAlias=kumi
storeFile=<ruta-absoluta-al-keystore>
```

**Ejemplo:**
```properties
storePassword=miPassword123
keyPassword=miPassword123
keyAlias=kumi
storeFile=/home/diego/kumi-release-key.jks
```

#### 3. Compilar APK Release

```bash
flutter build apk --release
```

#### 4. Compilar App Bundle (para Google Play Store)

```bash
flutter build appbundle --release
```

El App Bundle se generará en: `build/app/outputs/bundle/release/app-release.aab`

### Instalar en Dispositivo Android

```bash
# Conecta tu dispositivo Android vía USB y habilita "Depuración USB"
flutter install

# O instala directamente el APK
adb install build/app/outputs/flutter-apk/app-release.apk
```

---

## 🍎 Compilación para iOS

### Requisitos Previos

1. **macOS** (obligatorio - no se puede compilar iOS en Linux/Windows sin macOS)
2. **Xcode** instalado desde App Store (versión más reciente recomendada)
3. **CocoaPods** instalado
   ```bash
   sudo gem install cocoapods
   ```
4. **Cuenta de Desarrollador de Apple** (gratuita para desarrollo, $99/año para distribución)
5. **Flutter SDK** instalado

### Configuración Inicial

```bash
# Verificar configuración
flutter doctor

# Instalar dependencias de iOS
cd ios
pod install
cd ..
```

### Compilar para Desarrollo (Debug)

```bash
# Compilar y ejecutar en simulador o dispositivo
flutter run

# O compilar solo
flutter build ios --debug
```

### Compilar para Producción (Release)

#### 1. Configurar Certificados y Provisioning Profiles

1. Abre el proyecto en Xcode:
   ```bash
   open ios/Runner.xcworkspace
   ```

2. En Xcode:
   - Selecciona el target "Runner"
   - Ve a "Signing & Capabilities"
   - Selecciona tu equipo de desarrollo
   - Xcode generará automáticamente los certificados y provisioning profiles

#### 2. Actualizar Bundle Identifier (Opcional)

Si quieres cambiar el Bundle ID de `org.traccar.client.TraccarClient` a algo como `com.tudominio.kumi`:

- En Xcode: Target Runner > General > Bundle Identifier
- O edita `ios/Runner.xcodeproj/project.pbxproj` (línea 747)

#### 3. Compilar para Release

```bash
# Compilar IPA
flutter build ipa --release
```

El IPA se generará en: `build/ios/ipa/Kumi.ipa`

#### 4. Compilar para App Store Connect

```bash
# Compilar y subir a App Store Connect
flutter build ipa --release
```

Luego usa **Xcode** o **Transporter** para subir el IPA a App Store Connect.

### Instalar en Dispositivo iOS

#### Opción 1: Desde Xcode
1. Conecta tu iPhone/iPad
2. Selecciona tu dispositivo en Xcode
3. Presiona el botón "Run" (▶️)

#### Opción 2: Desde Terminal
```bash
flutter install
```

#### Opción 3: Instalar IPA directamente
```bash
# Usando ios-deploy (requiere instalación previa)
ios-deploy --bundle build/ios/ipa/Kumi.ipa
```

---

## 📋 Checklist de Compilación

### Antes de Compilar

- [ ] Flutter doctor muestra todo correcto
- [ ] Dependencias instaladas (`flutter pub get`)
- [ ] Iconos actualizados con tu marca
- [ ] Nombre de la app actualizado (✅ Ya hecho - "Kumi")
- [ ] Versión actualizada en `pubspec.yaml` si es necesario

### Para Android Release

- [ ] Keystore creado y configurado
- [ ] `environment/key.properties` configurado correctamente
- [ ] App Bundle compilado (`flutter build appbundle --release`)
- [ ] APK probado en dispositivo físico

### Para iOS Release

- [ ] Xcode configurado con tu cuenta de desarrollador
- [ ] Certificados y provisioning profiles configurados
- [ ] Bundle Identifier actualizado (si es necesario)
- [ ] IPA compilado (`flutter build ipa --release`)
- [ ] App probada en dispositivo físico

---

## 🔍 Verificación de Cambios

Para verificar que el nombre "Kumi" se aplicó correctamente:

### Android
- El nombre debería aparecer como "Kumi" en el launcher del dispositivo
- Verifica en: `android/app/src/main/AndroidManifest.xml` línea 7

### iOS
- El nombre debería aparecer como "Kumi" en la pantalla de inicio
- Verifica en: `ios/Runner/Info.plist` línea 14

---

## 📚 Recursos Adicionales

- [Documentación oficial de Flutter - Build and Release](https://docs.flutter.dev/deployment)
- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) - Generador de iconos
- [AppIcon.co](https://www.appicon.co/) - Generador de iconos multiplataforma
- [Flutter Build Modes](https://docs.flutter.dev/testing/build-modes)

---

## ⚠️ Notas Importantes

1. **Bundle Identifier / Package Name**: Los identificadores `org.traccar.client` están configurados en varios lugares. Si planeas publicar en las tiendas, considera cambiarlos a algo único como `com.tudominio.kumi`.

2. **Firebase**: El proyecto usa Firebase. Asegúrate de actualizar los archivos de configuración de Firebase (`google-services.json` para Android y `GoogleService-Info.plist` para iOS) con tus propias credenciales si cambias el Bundle ID.

3. **URL Schemes**: El scheme `org.traccar.client` está configurado en ambos sistemas. Si cambias el Bundle ID, también deberías actualizar estos schemes.

4. **Licencia de Background Geolocation**: El proyecto incluye una licencia para `flutter_background_geolocation`. Si cambias el Bundle ID, necesitarás una nueva licencia.

---

## 🚀 Próximos Pasos

1. ✅ Nombre cambiado a "Kumi"
2. ⏳ Reemplazar iconos con tu diseño de marca
3. ⏳ Compilar y probar en dispositivos
4. ⏳ (Opcional) Actualizar Bundle ID/Package Name si planeas publicar
5. ⏳ Configurar Firebase con tus credenciales si es necesario

