## Descripción
Who doesn't love cookies? Try to figure out the best one.
## Solución
- En la herramienta burpsuit nos vamos a `Proxy` > `Intercept` > `Intercept is on`
- En el navegador que se nos abre, ingresamos la URL de la instancia. En la página, ingresaremos el texto que está en el placeholder del input: `snickerdoodle`
- Burpsuit interceptará la petición GET, hacemos clic derecho en cualquier parte del texto de la petición capturada y seleccionamos Send to Intruder
- En la pestaña de positions ahí vamos a buscar el valor de name = 0, seleccionamos el 0 y le damos al botón Add, luego en la pestaña de payloads seleccionamos el payload type en numbers, vamos a poner el rango de From: 0 To: 100
- Iniciamos el ataque y observamos la longitud de las respuestas en las peticiones que se hacen
- Al principio todas van a tener el mismo valor de longitud, pero en cuanto cambie el tamaño de la longitud vamos a seleccionar esa petición y en la pestaña de response vamos a ver el HTML de la respuesta, ahí viene la bandera
```text
picoCTF{3v3ry1_l0v3s_c00k135_a4dadb49}
```
## Notas Adicionales
## Referencias