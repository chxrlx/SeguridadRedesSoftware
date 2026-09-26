## Descripción
I don't like scrolling down to read the code of my website, so I've squished it. As a bonus, my pages load faster!

## Solución
- Accedemos a la página web del reto proporcionada por la instancia (`http://chatelaine.cylabacademy.net:32105/`).
- Al abrir el código fuente HTML (`Ctrl + U` o clic derecho -> *Ver código fuente de la página*), observamos que todo el contenido ha sido "aplastado" o minificado en una única línea larga de texto sin indentaciones ni saltos de línea.
- Para encontrar la bandera podemos optar por varios métodos:
  - **Búsqueda directa:** Presionar `Ctrl + F` en la ventana del código fuente y buscar el prefijo de la bandera `academy{`.
  - **Herramientas de desarrollo (DevTools):** Abrir las herramientas de desarrollador (`F12`), donde la pestaña **Elements** formatea y organiza automáticamente el DOM en un árbol legible.
  - **Desde la terminal:** Consultar la página con `curl` y extraer el patrón de la bandera mediante `grep`:
    ```bash
    curl -s http://chatelaine.cylabacademy.net:32105/ | grep -oE "academy\{[a-zA-Z0-9_]+\}"
    ```
- Inspeccionando los elementos del documento, encontramos un párrafo que contiene la bandera definida como su clase:
  ```html
  <p class="academy{pr3tty_c0d3_f2dc9e9e}"></p>
  ```

```text
academy{pr3tty_c0d3_f2dc9e9e}
```

## Notas Adicionales
- **Minificación de código web:** Es una técnica estándar de optimización de rendimiento front-end en la que se eliminan espacios en blanco, tabulaciones, saltos de línea y comentarios para disminuir el tamaño del archivo y reducir el tiempo de carga del sitio web.
- **Minificación vs. Seguridad:** La minificación no es una técnica de seguridad ni de ofuscación criptográfica. El código original puede ser reconstituido y leído fácilmente utilizando formateadores de código (*Pretty Print*, habitualmente representado con el botón `{}` en navegadores como Chrome o Firefox).
- **Exposición en atributos HTML:** El uso de atributos HTML como `class`, `id` o `data-*` para ocultar información no impide que cualquier usuario inspeccione el documento completo entregado por el servidor.

## Referencias
- [MDN Web Docs - Glosario: Minificación](https://developer.mozilla.org/es/docs/Glossary/Minification)
- [Chrome DevTools - Inspeccionar y depurar código formateado](https://developer.chrome.com/docs/devtools/)