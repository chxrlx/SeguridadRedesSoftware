## Descripción
We have several pages hidden. Can you find the one with the flag?

## Solución
- Accedemos a la página principal de la instancia del reto (`http://chatelaine.cylabacademy.net:18118/`).
- Abrimos las herramientas de desarrollo o el código fuente (`Ctrl + U`). En los enlaces a recursos estáticos observamos que los archivos se encuentran en un subdirectorio llamado `secret/`:
  ```html
  <link href="secret/assets/index.css" rel="stylesheet" />
  <img src="secret/assets/DX1KYM.jpg" ... />
  ```
- Navegamos directamente a dicho directorio: `http://chatelaine.cylabacademy.net:18118/secret/`.
- Al revisar el código fuente de esta nueva página, encontramos un mensaje *"Finally. You almost found me. you are doing well"* y una hoja de estilos vinculada a una ruta relativa en `hidden/`:
  ```html
  <link rel="stylesheet" href="hidden/file.css" />
  ```
- Navegamos al siguiente nivel: `http://chatelaine.cylabacademy.net:18118/secret/hidden/`.
- Esta página muestra una interfaz simulada de inicio de sesión. Al revisar su código fuente (`Ctrl + U`), encontramos referencias a una tercera carpeta anidada llamada `superhidden/`:
  ```html
  <link href="superhidden/login.css" rel="stylesheet" />
  ...
  <input type="hidden" name="db" value="superhidden/xdfgwd.html" />
  ```
- Navegamos al directorio más profundo descubierto: `http://chatelaine.cylabacademy.net:18118/secret/hidden/superhidden/`.
- En esta página final, el código HTML contiene la bandera en una etiqueta `<h3>`:
  ```html
  <h1>Finally. You found me. But can you see me</h1>
  <h3 class="flag">academy{succ3ss_@h3n1c@10n_e77e3359}</h3>
  ```
  *(Nota: En la interfaz visual del navegador el texto parece invisible debido a que en el archivo `mycss.css` la clase `.flag` tiene definidos tanto el color del texto como el fondo en color blanco: `background-color: white; color: white;`).*

```text
academy{succ3ss_@h3n1c@10n_e77e3359}
```

## Notas Adicionales
- **Descubrimiento de contenido forzado (Forced Browsing / Content Discovery):** Las rutas no vinculadas directamente en los menús de navegación pueden ser descubiertas rastreando rutas relativas en elementos `<link>`, `<script>`, `<img>` y campos ocultos de formularios (`<input type="hidden">`).
- **Seguridad por Oscuridad:** Ocultar recursos anidándolos en directorios con nombres como `secret`, `hidden` y `superhidden` sin aplicar autenticación ni controles de acceso reales es una mala práctica. Cualquier usuario o herramienta automatizada de escaneo de directorios (como `gobuster`, `dirsearch` o `ffuf`) puede identificarlos con facilidad.
- **Ocultamiento mediante estilos CSS:** Modificar los estilos (como igualar el color de fuente al color de fondo o usar `display: none`) oculta el elemento visualmente en pantalla, pero no impide que viaje en texto claro dentro del DOM, haciéndolo trivial de leer mediante las herramientas de desarrollo o el código fuente.

## Referencias
- [OWASP - Forced Browsing](https://owasp.org/www-community/attacks/Forced_browsing)
- [OWASP WSTG - Conduct Search Engine Discovery Reconnaissance for Information Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/01-Conduct_Search_Engine_Discovery_Reconnaissance_for_Information_Leakage)