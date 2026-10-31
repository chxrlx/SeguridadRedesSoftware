## Descripción
Can you find the flag in this disk image? Wrap what you find in the academy{} flag format. Download the disk image here.

## Solución
- Descomprimimos la imagen de la partición comprimida con `gzip`:
  ```bash
  gzip -dc timeline-1-partition4.img.gz > timeline1.img
  ```
- Al tratarse de un reto de análisis de línea de tiempo forense, extraemos los metadatos temporales de todos los inodos del sistema de archivos ext4 con `fls` y procesamos la línea de tiempo cronológica con `mactime`:
  ```bash
  fls -r -m / timeline1.img > body1.txt
  mactime -b body1.txt > timeline1.txt
  ```
- Al examinar los eventos más recientes en la línea de tiempo justo antes del apagado del sistema (`poweroff`), encontramos actividad inusual en el directorio `/etc` a las `16:50:07`:
  ```text
  Mon Dec 01 2025 16:50:07     3072 m.c. d/drwxr-xr-x 0        0        32385    /etc
                                 49 macb r/rrw-r--r-- 0        0        32716    /etc/chat
  Mon Dec 01 2025 16:50:19     1024 .a.. d/drwx------ 0        0        172      /root
  Mon Dec 01 2025 16:50:20       12 .a.. l/lrwxrwxrwx 0        0        64902    /usr/bin/shred -> /bin/busybox
  Mon Dec 01 2025 16:50:23       12 .a.. l/lrwxrwxrwx 0        0        125      /sbin/poweroff -> /bin/busybox
  ```
- Se identificó la creación del archivo `/etc/chat` (inodo 32716, tamaño 49 bytes) inmediatamente antes de que se ejecutara `/usr/bin/shred` y se ordenara el apagado del equipo.
- Extraemos el contenido del inodo 32716 mediante la herramienta `icat`:
  ```bash
  icat timeline1.img 32716
  ```
  Obteniendo la cadena codificada en Base64:
  ```text
  NTczNDE3aDEzcl83aDRuXzdoM18xNDU3XzU4NTI3YmIyMjIK
  ```
- Decodificamos la cadena Base64 con el comando `base64`:
  ```bash
  echo "NTczNDE3aDEzcl83aDRuXzdoM18xNDU3XzU4NTI3YmIyMjIK" | base64 -d
  # 573417h13r_7h4n_7h3_1457_58527bb222
  ```
- Envolviendo el texto en el formato de bandera `academy{}`:
```text
academy{573417h13r_7h4n_7h3_1457_58527bb222}
```

## Notas Adicionales
- A diferencia del reto anterior (donde el atacante recurrió a *timestomping* hacia el pasado), en este escenario el archivo fue creado en tiempo real pocos segundos antes de un intento de borrado seguro con `shred` y apagado abrupto del sistema.
- El análisis cronológico de los momentos inmediatamente previos al apagado (*shutdown artifact analysis*) es crucial en incidentes de seguridad para identificar la persistencia o archivos dejados por el intruso antes de desconectarse.
- Herramientas utilizadas:
  - `gzip`: Descompresión del artefacto.
  - `fls`, `mactime`, `icat` (The Sleuth Kit): Análisis de la línea de tiempo y extracción de inodos sin montar la partición.
  - `base64`: Decodificación de la carga útil.

## Referencias
- [The Sleuth Kit Documentation](https://wiki.sleuthkit.org/index.php?title=TSK_Tool_Overview)
- [Digital Forensics - Timeline Analysis Techniques](https://www.sans.org/white-papers/34290/)
