## Descripción
Connect to this PostgreSQL server and find the flag!
`psql -h xebec.cylabacademy.net -p 45021 -U postgres pico`
Password is `postgres`

## Solución
- Nos conectamos al servidor PostgreSQL remoto utilizando el cliente de terminal `psql`:
  ```bash
  psql -h xebec.cylabacademy.net -p 45021 -U postgres pico
  ```
- Introducimos la contraseña proporcionada: `postgres`
- Dentro de la consola interactiva de PostgreSQL (`pico=>`), listamos las tablas de la base de datos con el comando `\dt`:
  ```sql
  \dt
  ```
  El resultado muestra una tabla de nombre `flags`:
  ```text
           List of relations
   Schema | Name  | Type  |  Owner   
  --------+-------+-------+----------
   public | flags | table | postgres
  (1 row)
  ```
- Realizamos una consulta a la tabla `flags` con `SELECT * FROM flags;`:
  ```sql
  SELECT * FROM flags;
  ```
  La salida devuelve las siguientes filas:
  ```text
   id | firstname | lastname  |                address                 
  ----+-----------+-----------+----------------------------------------
    1 | Luke      | Skywalker | academy{L3arN_S0m3_5qL_t0d4Y_caacb18c}
    2 | Leia      | Organa    | Alderaan
    3 | Han       | Solo      | Corellia
  (3 rows)
  ```
- La bandera se encuentra en el campo `address` correspondiente al registro de Luke Skywalker

```text
academy{L3arN_S0m3_5qL_t0d4Y_caacb18c}
```

## Notas Adicionales
- **PostgreSQL**: Es un sistema de gestión de bases de datos relacionales de código abierto ampliamente utilizado en entornos de producción.
- **Parámetros del comando psql**:
  - `-h`: Host remoto donde se ubica el servidor de base de datos.
  - `-p`: Puerto de conexión (el estándar es 5432).
  - `-U`: Usuario para la autenticación.
  - `pico`: Base de datos objetivo especificada al final del comando.
- **Comandos esenciales en psql**:
  - `\l`: Listar todas las bases de datos del servidor.
  - `\dt`: Listar todas las tablas del esquema actual.
  - `\d <tabla>`: Describir la estructura y columnas de una tabla.
  - `\q`: Salir del intérprete psql.

## Referencias
- [Documentación Oficial de PostgreSQL - psql](https://www.postgresql.org/docs/current/app-psql.html)
- [PostgreSQL Tutorial - psql commands](https://www.postgresqltutorial.com/postgresql-cheat-sheet/)