## Descripción
The factory is hiding things from all of its users.
## Solución
- En la página del login, iniciamos sesión con cualquier usuario y contraseña
- Nos arroja un mensaje que hemos iniciado sesión pero no nos muestra la bandera
- Abrimos la herramienta de desarrollador y accedemos a la pestaña Application. vamos a la opción de cookies y vemos que hay un nombre `admin` con un valor `False`, cambiamos el valor a `True` y recargamos la página para obtener la bandera
```text
picoCTF{th3_c0nsp1r4cy_l1v3s_4d184b0d}
```
## Notas Adicionales
- Las cookies en informática son pequeños archivos de texto que los sitios web envían y guardan en tu navegador (ordenador, móvil o tablet) cuando los visitas. No son programas ejecutables ni contienen virus, sino fragmentos de datos que sirven como la "memoria" del sitio web.
## Referencias