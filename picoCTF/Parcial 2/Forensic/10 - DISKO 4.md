## Descripción
Can you find the flag in this disk image? This time I deleted the file! Let see you get it now!

## Solución
- Descomprimimos la imagen del sistema de archivos con `gzip`:
  ```bash
  gzip -dc disko-4.dd.gz > disko-4.dd
  ```
- Determinamos el tipo de partición y sistema de archivos mediante `file`:
  ```bash
  file disko-4.dd
  # disko-4.dd: DOS/MBR boot sector, ..., FAT (32 bit), ...
  ```
- Dado que la descripción indica que el archivo con la bandera fue eliminado, listamos las entradas de archivos eliminados en el sistema de archivos FAT32 utilizando `fls` con el modificador `-d`:
  ```bash
  fls -r -d disko-4.dd
  ```
- El comando identifica dos archivos eliminados en el directorio `log/`:
  ```text
  r/r * 522629: log/messages
  r/r * 532021: log/dont-delete.gz
  ```
- El archivo `log/dont-delete.gz` corresponde al inodo `532021`. Extraemos su contenido directamente sin montar la partición utilizando `icat`:
  ```bash
  icat disko-4.dd 532021 > dont-delete.gz
  ```
- Descomprimimos y leemos el archivo con `zcat`:
  ```bash
  zcat dont-delete.gz
  ```
- Salida obtenida:
  ```text
  Here is your flag
  academy{d3l_d0n7_h1d3_w3ll_9748d144}
  ```
- Bandera extraída:
```text
academy{d3l_d0n7_h1d3_w3ll_9748d144}
```

## Notas Adicionales
- En sistemas de archivos FAT32, al eliminar un archivo el sistema operativo simplemente marca el primer byte del nombre en la entrada del directorio con `0xE5` y desasigna los clusters en la tabla FAT, pero los metadatos y los datos subyacentes permanecen intactos en los sectores de datos mientras no sean sobreescritos.
- Las herramientas de The Sleuth Kit permiten navegar y reconstruir archivos eliminados inspeccionando las entradas de directorio marcadas como inactivas.
- Herramientas utilizadas:
  - `gzip` / `zcat`: Descompresión de la imagen de disco y del archivo recuperado.
  - `fls` e `icat` (The Sleuth Kit): Localización y extracción del archivo eliminado según su inodo/cluster.

## Referencias
- [The Sleuth Kit (TSK) - FAT File System Analysis](https://wiki.sleuthkit.org/index.php?title=FAT_Implementation_Notes)
- [FAT32 File Deletion Internals](https://en.wikipedia.org/wiki/File_Allocation_Table#Directory_table)
