## Descripción
Can you read files in the root file?
## Solución
- Nos conectamos a la instancia SSH que nos dan
- Ejecutamos el siguiente comando para ver qué comandos podemos ejecutar como usuario con privilegios de root: `ls -l`
- Vemos que nos da permiso para ejecutar vi y ejecutamos el siguiente comando: `sudo /usr/bin/vi`
- Una vez dentro de vi escribimos: `:shell` y le damos enter para que nos mande a la terminal con permisos root
- Como el comando ls por si solo no nos lista el contenido de la carpeta root, usamos el siguiente comando para que nos muestre los archivos dentro incluyendo los archivos ocultos: `ls -la /root`
- La bandera viene en un archivo llamado: `.flag.txt` vemos su contenido con: `cat /root/.flag.txt`
```text
picoCTF{uS1ng_v1m_3dit0r_55878b51}
```
## Notas Adicionales
## Referencias
Usé IA para que me dijera algunos comandos para vi y para ls (nadie puede salir de vi)