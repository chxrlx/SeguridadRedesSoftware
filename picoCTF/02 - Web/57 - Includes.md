## Descripción
Can you get the flag? Go to this website and see what you can discover.

## Solución
- Accedemos a la página web del reto proporcionada por la instancia (`http://xebec.cylabacademy.net:32011/`).
- Abrimos el código fuente HTML de la página (clic derecho -> **Ver código fuente de la página** o mediante el atajo `Ctrl + U`).
- Al revisar el código HTML observamos que incluye dos archivos externos:
  - Una hoja de estilos en la cabecera: `<link rel="stylesheet" href="style.css">`
  - Un archivo de scripts al inicio del body: `<script src="script.js"></script>`
- Accedemos a `style.css` haciendo clic en su enlace en el código fuente o navegando a `http://xebec.cylabacademy.net:32011/style.css`, donde encontramos comentada la primera parte de la bandera:
  ```css
  /*  academy{1nclu51v17y_1of2_  */
  ```
- Accedemos a `script.js` navegando a `http://xebec.cylabacademy.net:32011/script.js`, donde encontramos comentada la segunda parte de la bandera:
  ```javascript
  //  f7w_2of2_64d6df37}
  ```
- Unimos ambas partes para formar la bandera completa:

```text
academy{1nclu51v17y_1of2_f7w_2of2_64d6df37}
```

## Notas Adicionales
- **Separación de responsabilidades en la web:** Es un estándar de diseño web separar la estructura semántica (HTML), la capa de estilos y presentación (CSS), y el comportamiento dinámico (JavaScript) en archivos independientes vinculados mediante etiquetas `<link>` y `<script>`.
- **Seguridad en el lado del cliente (Client-Side):** Todo recurso descargado y procesado por el navegador (HTML, CSS, JS, imágenes, metadatos) es de acceso público para el cliente. Nunca se deben dejar comentarios con secretos, tokens, credenciales o lógica sensible en estos archivos.
- **Formato de bandera:** En esta instancia de la plataforma CyLab Academy, las banderas utilizan el prefijo `academy{...}` en lugar de `picoCTF{...}`.

## Referencias
- [MDN Web Docs - Elemento link](https://developer.mozilla.org/es/docs/Web/HTML/Element/link)
- [MDN Web Docs - Elemento script](https://developer.mozilla.org/es/docs/Web/HTML/Element/script)