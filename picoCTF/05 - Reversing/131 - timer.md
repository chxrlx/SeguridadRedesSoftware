## Descripción
You will find the flag after analysing this apk Download here.

## Solución
- El archivo proporcionado es una aplicación de Android empaquetada (`timer.apk`).
- Para analizar el contenido del paquete APK podemos recurrir a herramientas estándar como `apktool`, `jadx` o directamente inspeccionar las cadenas del archivo comprimido.
- Al descompilar el APK con `apktool`:
  ```bash
  apktool d timer.apk -o timer_extracted
  ```
  O al inspeccionar el manifiesto con `jadx`:
  ```bash
  jadx -d timer_decompiled timer.apk
  ```
- Al revisar el archivo `AndroidManifest.xml` (o la clase `BuildConfig.java`), se observa que la bandera se encuentra configurada en el atributo de versión de la aplicación (`versionName`):
  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android"
      android:versionCode="1"
      android:versionName="academy{t1m3r_r3v3rs3d_succ355fully_17496}"
      package="com.example.timer">
  ```
  Y en `com/example/timer/BuildConfig.java`:
  ```java
  public final class BuildConfig {
      public static final String VERSION_NAME = "academy{t1m3r_r3v3rs3d_succ355fully_17496}";
  }
  ```
- También es posible extraerla directamente leyendo el flujo comprimido con `unzip` o `strings`:
  ```bash
  unzip -p timer.apk | grep -a "academy{"
  ```
- Obtenemos la bandera:
```text
academy{t1m3r_r3v3rs3d_succ355fully_17496}
```

## Notas Adicionales
- Los archivos APK son contenedores en formato ZIP que albergan el archivo `AndroidManifest.xml` compilado en formato binario XML (AXML), el código ejecutable de la máquina virtual Dalvik/ART (`classes.dex`) y los recursos de la aplicación.
- Con frecuencia, los desarrolladores configuran metadatos de compilación como `versionName` en el archivo `build.gradle`, los cuales se propagan automáticamente al manifiesto final y a la clase generada `BuildConfig`.
- Herramientas utilizadas:
  - `jadx` / `apktool`: Descompilación de recursos y código fuente del APK.
  - `unzip` / `grep`: Inspección rápida de cadenas en el paquete.

## Referencias
- [Apktool Documentation](https://ibotpeaches.github.io/Apktool/)
- [JADX - Dex to Java decompiler](https://github.com/skylot/jadx)
- [Android App Manifest Overview](https://developer.android.com/guide/topics/manifest/manifest-intro)
