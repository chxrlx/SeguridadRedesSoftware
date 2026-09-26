## Descripción
Can you login to this website?

## Solución
- Accedemos a la página principal de la instancia (`http://chatelaine.cylabacademy.net:23201/`), que muestra un formulario de inicio de sesión con campos para usuario y contraseña.
- Al inspeccionar el código fuente del formulario (`Ctrl + U`), observamos un campo oculto:
  ```html
  <input type="hidden" name="debug" value="0">
  ```
- Si cambiamos el valor de `debug` a `1` al enviar el formulario (a través de las DevTools, un proxy o mediante `curl`), el servidor expone la consulta SQL que se ejecuta internamente:
  ```sql
  SELECT * FROM users WHERE name='admin' AND password='admin'
  ```
- Esto evidencia que los valores de entrada se concatenan de forma directa en la instrucción SQL sin validación ni parametrización, lo que permite una inyección SQL (SQLi).
- Para evadir la verificación de la contraseña, ingresamos como nombre de usuario:
  ```text
  admin' --
  ```
  (o alternativamente `' OR 1=1 --`) dejando el campo de contraseña con cualquier valor arbitrario.
- La consulta ejecutada en la base de datos se transforma en:
  ```sql
  SELECT * FROM users WHERE name='admin' --' AND password='...';
  ```
  El delimitador `'` cierra la cadena del nombre y `--` comenta el resto de la consulta, anulando por completo la comprobación del password.
- Al enviar la solicitud, el servidor valida el acceso y responde:
  ```html
  <h1>Logged in! But can you see the flag, it is in plainsight.</h1>
  <p hidden>Your flag is: academy{L00k5_l1k3_y0u_solv3d_it_8fcb45de}</p>
  ```
- Dado que el párrafo contiene el atributo HTML `hidden`, no es visible a simple vista en la pantalla del navegador, pero al inspeccionar el DOM con las herramientas de desarrollador (`F12`) o ver el código fuente de la página (`Ctrl + U`), obtenemos la bandera:

```text
academy{L00k5_l1k3_y0u_solv3d_it_8fcb45de}
```

## Notas Adicionales
- **Inyección SQL (CWE-89):** Esta vulnerabilidad ocurre cuando datos suministrados por el usuario se concatenan directamente en la consulta a la base de datos sin saneamiento ni uso de sentencias preparadas, permitiendo al usuario redefinir la estructura lógica de la consulta.
- **Comentarios en SQL:** En dialectos como SQLite, PostgreSQL y MySQL/MariaDB, la secuencia de dos guiones (`--`) indica el inicio de un comentario hasta el fin de la línea, descartando cualquier condición subsiguiente.
- **Riesgo del modo Debug (CWE-200 / OWASP A05):** Exponer parámetros de depuración como `debug=1` en entornos productivos facilita que un atacante comprenda la estructura de las tablas, nombres de columnas y el motor de base de datos utilizado.
- **Atributo `hidden` en HTML:** El atributo global `hidden` indica al navegador que no debe representar visualmente el elemento, pero el contenido se transmite íntegro en la respuesta HTTP en texto plano, por lo que nunca debe utilizarse para proteger información sensible.
- **Mitigación (Consultas Parametrizadas):** Se debe emplear siempre parametrización de consultas o sentencias preparadas (Prepared Statements) a través de abstracciones como PDO en PHP:
  ```php
  $stmt = $pdo->prepare('SELECT * FROM users WHERE name = :username AND password = :password');
  $stmt->execute(['username' => $username, 'password' => $password]);
  ```

## Referencias
- [OWASP - SQL Injection Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [CWE-89: Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')](https://cwe.mitre.org/data/definitions/89.html)
- [MDN Web Docs - Atributo global hidden](https://developer.mozilla.org/es/docs/Web/HTML/Global_attributes/hidden)