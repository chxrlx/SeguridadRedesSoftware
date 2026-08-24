## Descripción
Can you look at the data in this binary? The bash script might help!
## Solución
- Descargamos los dos archivos con `wget`
- Vemos que hay un archivo bash llamado `ltdis.sh` y otro llamado `static`
- Les damos permisos de ejecución `chmod +x`
- Usamos el script bash que nos da: `./ltdis.sh static`
- Nos arroja un archivo llamado: `static.ltdis.x86_64.txt`
- Usamos `grep` para encontrar la bandera
```text
picoCTF{d15a5m_t34s3r_20335e41}
```
## Notas Adicionales
Los archivos .sh son scripts bash muy populares en Linux que sirven para muchas tareas
## Referencias