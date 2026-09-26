## Descripción
Can you get the flag? Go to this website and see what you can discover.

## Solución
- Accedemos al portal web del reto proporcionado por la instancia (`http://chatelaine.cylabacademy.net:37791/`), el cual presenta un formulario de inicio de sesión con campos para usuario y contraseña.
- Ingresamos credenciales arbitrarias (por ejemplo, `admin` / `admin`) para analizar el comportamiento y el flujo de autenticación del sitio.
- La aplicación nos redirige a `login.php` mostrando el mensaje "Log In Failed".
- Abrimos las herramientas de desarrollo del navegador (`F12`) o inspeccionamos el código fuente de `login.php` (`Ctrl + U`), donde encontramos que se importa un script externo:
  ```html
  <script src="secure.js"></script>
  ```
  Además, observamos en el script en línea que la verificación de inicio de sesión se realiza en el cliente mediante la función `checkPassword()`:
  ```javascript
  loggedIn = checkPassword(window.username, window.password);
  
  if(loggedIn)
  {
    document.getElementById('msg').innerHTML = "Log In Successful";
    document.getElementById('adminFormHash').value = "2196812e91c29df34f5e217cfd639881";
    document.getElementById('hiddenAdminForm').submit();
  }
  ```
- Accedemos directamente al archivo `secure.js` desde la pestaña *Sources* o navegando a su URL, donde encontramos las credenciales codificadas en texto claro dentro del código fuente:
  ```javascript
  function checkPassword(username, password)
  {
    if( username === 'admin' && password === 'strongPassword098765' )
    {
      return true;
    }
    else
    {
      return false;
    }
  }
  ```
- Regresamos a la página de login e ingresamos las credenciales obtenidas:
  - **Username:** `admin`
  - **Password:** `strongPassword098765`
- La validación es exitosa y el formulario envía automáticamente la petición `POST` a `admin.php`, desplegando la bandera:

```text
academy{j5_15_7r4n5p4r3n7_4fcce45f}
```

## Notas Adicionales
- **Autenticación en el lado del cliente (Client-Side Authentication):** Nunca se debe delegar la lógica de autenticación o autorización al cliente. Dado que el navegador tiene acceso y control total sobre el código JavaScript, cualquier validación o secreto que resida en el front-end puede ser inspeccionado, modificado o eludido.
- **Flujo de autenticación seguro:** La comprobación de credenciales siempre debe realizarse en el backend contra una base de datos segura (utilizando funciones de hashing robustas con salting como bcrypt o Argon2), y mantener el estado de sesión mediante cookies seguras (`HttpOnly`, `SameSite`, `Secure`) o tokens criptográficos firmados.

## Referencias
- [OWASP - Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)