## Descripción
Welcome to the challenge! In this challenge, you will explore a web application and find an endpoint that exposes a file containing a hidden flag. The application is a simple blog website where you can read articles about various topics, including an article about API Documentation. Your goal is to explore the application and find the endpoint that generates files holding the server's memory, where a secret flag is hidden.
`http://xebec.cylabacademy.net:13411/`

## Solución
- Accedemos al sitio web del blog en `http://xebec.cylabacademy.net:13411/`
- Al explorar la página y los artículos relacionados con documentación de APIs, identificamos el endpoint `/api-docs/` que contiene la interfaz interactiva de Swagger UI
- Inspeccionamos las rutas documentadas en la API (visibles en Swagger o directamente en el archivo `/api-docs/swagger-ui-init.js`) y descubrimos un endpoint sensible:
  - `GET /heapdump`: Genera un volcado de memoria (heap snapshot) del servidor Node.js/V8
- Descargamos el volcado de memoria utilizando `curl`:
  ```bash
  curl -s http://xebec.cylabacademy.net:13411/heapdump -o heapdump.json
  ```
- Buscamos el patrón de la bandera dentro del archivo JSON generado mediante `grep`:
  ```bash
  grep -o -E 'academy\{[^}]+\}' heapdump.json
  ```
- El comando localiza la bandera directamente almacenada en la memoria del servidor

```text
academy{Pat!3nt_15_Th3_K3y_cc0f4fda}
```

## Notas Adicionales
- **Heap Dump (Volcado de memoria)**: Captura el estado en memoria de todos los objetos, cadenas y variables asignadas en tiempo de ejecución. Si se exponen públicamente, pueden filtrar claves privadas, contraseñas, tokens de sesión y banderas.
- **Exposición de Swagger / Documentación de API**: Dejar consolas de documentación Swagger accesibles sin autenticación permite a atacantes mapear la superficie de ataque y descubrir endpoints internos o de depuración que no estaban enlazados en el menú principal.

## Referencias
- [Node.js Documentation - v8.getHeapSnapshot()](https://nodejs.org/api/v8.html#v8getheapsnapshot)
- [OWASP Web Security Testing Guide - Review Webpage Content for Information Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Webpage_Content_for_Information_Leakage)