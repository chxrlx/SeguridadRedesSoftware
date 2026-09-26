## Descripción
Can you get the flag? Go to this website and see what you can discover.

## Solución
- Accedemos a la página web del reto proporcionada por la instancia (`http://xebec.cylabacademy.net:15551/`), donde se nos muestra un título *"Online Gradebook"* y un botón *"Continue as guest"*.
- Abrimos las herramientas de desarrollo del navegador (`F12`) o inspeccionamos el código fuente de la página (`Ctrl + U`) y observamos que se carga un script externo:
  ```html
  <script src="guest.js"></script>
  ```
- Al hacer clic en el botón *"Continue as guest"*, se ejecuta la función `continueAsGuest()`, definida en `guest.js`:
  ```javascript
  function continueAsGuest()
  {
    window.location.href = '/check.php';
    document.cookie = "isAdmin=0";
  }
  ```
- El script asigna en el cliente la cookie `isAdmin=0` y redirige a `/check.php`, donde se nos muestra el mensaje de que no hay servicios disponibles para invitados:
  ```text
  We apologize, but we have no guest services at the moment.
  ```
- Dado que el servidor evalúa los privilegios administrativos confiando directamente en el valor que el cliente envía en la cookie `isAdmin`, procedemos a alterar su valor:
  - **Desde el navegador:** En la pestaña **Application** (o **Almacenamiento** en Firefox) -> **Cookies**, seleccionamos el dominio, cambiamos el valor de `isAdmin` de `0` a `1` y recargamos `/check.php` (o en la consola de JavaScript ejecutamos `document.cookie = "isAdmin=1"`).
  - **Desde la terminal con curl:**
    ```bash
    curl -s --cookie "isAdmin=1" http://xebec.cylabacademy.net:15551/check.php
    ```
- El servidor procesa la petición con privilegios de administrador y devuelve la bandera:

```text
academy{gr4d3_A_c00k13_6cf86b5a}
```

## Notas Adicionales
- **Manipulación de Cookies (Cookie Tampering):** Las cookies son almacenadas y enviadas por el cliente, lo que significa que el usuario tiene control absoluto sobre su contenido. Nunca se deben almacenar roles, permisos o identificadores booleanos de privilegios (`isAdmin=0/1`, `role=guest/admin`) en cookies no firmadas ni cifradas.
- **Control de Acceso Roto (Broken Access Control - OWASP A01):** La aplicación asume incorrectamente que los datos enviados en la cabecera `Cookie` son fidedignos sin validar la autenticidad ni el estado de sesión en el backend.
- **Buenas Prácticas de Mitigación:** Los roles y privilegios deben gestionarse en el lado del servidor vinculados a identificadores de sesión aleatorios y opacos (Session IDs), o mediante tokens firmados criptográficamente (como JWT con algoritmos robustos). Asimismo, las cookies sensibles deben protegerse con las directivas `HttpOnly`, `Secure` y `SameSite`.

## Referencias
- [OWASP Top 10: Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)
- [MDN Web Docs - Uso de cookies HTTP](https://developer.mozilla.org/es/docs/Web/HTTP/Cookies)