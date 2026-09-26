## Descripción
The flag is somewhere on this web application not necessarily on the website. Find it.

## Solución
- El título del reto (*Roboto Sans*) y la descripción hacen alusión al archivo estándar de rastreo web `robots.txt`.
- Accedemos al archivo `robots.txt` en la raíz de la instancia (`http://chatelaine.cylabacademy.net:31065/robots.txt`) o mediante `curl`:
  ```bash
  curl -s http://chatelaine.cylabacademy.net:31065/robots.txt
  ```
- El archivo devuelve el siguiente contenido:
  ```text
  User-agent *
  Disallow: /cgi-bin/
  Think you have seen your flag or want to keep looking.

  ZmxhZzEudHh0;anMvbXlmaW
  anMvbXlmaWxlLnR4dA==
  svssshjweuiwl;oiho.bsvdaslejg
  Disallow: /wp-admin/
  ```
- Identificamos varias líneas con cadenas codificadas en Base64. En particular, la cadena con relleno de igualdad `anMvbXlmaWxlLnR4dA==`:
- Procedemos a decodificarla desde la terminal:
  ```bash
  echo "anMvbXlmaWxlLnR4dA==" | base64 -d
  ```
  O en PowerShell:
  ```powershell
  [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("anMvbXlmaWxlLnR4dA=="))
  ```
  La decodificación nos revela la ruta: `js/myfile.txt`.
- Consultamos la ruta descubierta en el navegador o mediante `curl`:
  ```bash
  curl -s http://chatelaine.cylabacademy.net:31065/js/myfile.txt
  ```
- El archivo de texto plano contiene la bandera del reto:

```text
academy{Who_D03sN7_L1k5_90B0T5_662b27d3}
```

## Notas Adicionales
- **Robots Exclusion Standard (`robots.txt`):** Es un archivo público situado en el directorio raíz de un servidor web para comunicarse con rastreadores y motores de búsqueda, señalando qué rutas no deben indexarse mediante directivas `Disallow`.
- **Falsa sensación de seguridad (Seguridad por oscuridad):** Dado que `robots.txt` es accesible públicamente sin autenticación, los atacantes suelen revisarlo como uno de los primeros pasos durante la fase de reconocimiento (recon) para descubrir paneles administrativos, rutas de respaldo o directorios privados.
- **Base64 no es cifrado:** Base64 es únicamente un formato de codificación binario a texto (RFC 4648). No provee seguridad ni confidencialidad algorítmica, ya que puede revertirse inmediatamente sin necesidad de una clave.

## Referencias
- [OWASP WSTG - Review Webserver Metafiles for Information Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/03-Review_Webserver_Metafiles_for_Information_Leakage)
- [Google Search Central - Introducción al archivo robots.txt](https://developers.google.com/search/docs/crawling-indexing/robots/intro)
- [RFC 4648 - The Base16, Base32, and Base64 Data Encodings](https://datatracker.ietf.org/doc/html/rfc4648)