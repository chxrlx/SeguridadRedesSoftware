## Descripción
Can you get the flag? Go to this website and see what you can discover.

## Solución
- Accedemos a la URL de la instancia proporcionada (`http://xebec.cylabacademy.net:33617/`).
- Hacemos clic derecho sobre la página y seleccionamos **Ver código fuente de la página** (o presionamos la combinación de teclas `Ctrl + U`).
- También se puede presionar `F12` o `Ctrl + Shift + I` para abrir las herramientas de desarrollo del navegador (DevTools) e inspeccionar el DOM en la pestaña **Elements**.
- Al final del cuerpo (`<body>`) del documento HTML, encontramos un comentario con la bandera:
  ```html
  <!--academy{1n5p3t0r_0f_h7ml_a2b543be}-->
  ```

```text
academy{1n5p3t0r_0f_h7ml_a2b543be}
```

## Notas Adicionales
- **Visibilidad de comentarios HTML:** La sintaxis `<!-- ... -->` define comentarios en HTML. Aunque el motor de renderizado del navegador no los muestra visualmente en la página, todo el código fuente HTML se transfiere íntegro al cliente, por lo que cualquier usuario puede leerlos inspeccionando el código fuente.
- **Diferencia con lenguajes de backend:** Los comentarios en lenguajes que se ejecutan en el servidor (como Python, PHP, Java) o en motores de plantillas no se transfieren al cliente si el servidor está configurado correctamente, a diferencia de los comentarios HTML, CSS o JS.

## Referencias
- [MDN Web Docs - Comentarios en HTML](https://developer.mozilla.org/es/docs/Learn/HTML/Introduction_to_HTML/Getting_started#comentarios_en_html)