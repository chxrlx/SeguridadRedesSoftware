## Descripción
Download this disk image and find the flag.

## Solución
- Descomprimimos la imagen con `gzip`:
  ```bash
  gzip -dk disk.flag.img.gz
  ```
- Examinamos la tabla de particiones del disco con `mmls`:
  ```bash
  mmls disk.flag.img
  ```
  Salida:
  ```text
  DOS Partition Table
  Offset Sector: 0
  Units are in 512-byte sectors

        Slot      Start        End          Length       Description
  000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
  001:  -------   0000000000   0000002047   0000002048   Unallocated
  002:  000:000   0000002048   0000206847   0000204800   Linux (0x83)
  003:  000:001   0000206848   0000360447   0000153600   Linux Swap / Solaris x86 (0x82)
  004:  000:002   0000360448   0000614399   0000253952   Linux (0x83)
  ```
- La partición de mayor tamaño que contiene la raíz del sistema de archivos Linux inicia en el sector `360448`.
- Listamos recursivamente los archivos de dicha partición buscando referencias a la bandera con `fls`:
  ```bash
  fls -r -o 360448 disk.flag.img | grep -i flag
  ```
  Resultado:
  ```text
  ++ r/r * 2082(realloc):	flag.txt
  ++ r/r 2371:	flag.uni.txt
  ```
- Localizamos el archivo `flag.uni.txt` asociado al inodo `2371` en la ruta `root/my_folder/flag.uni.txt`.
- Extraemos el contenido del inodo con `icat`:
  ```bash
  icat -o 360448 disk.flag.img 2371
  ```
- Debido a que el archivo está codificado en formato Unicode (UTF-16LE con bytes nulos intermedios), podemos limpiarlo eliminando los bytes nulos:
  ```bash
  icat -o 360448 disk.flag.img 2371 | tr -d '\0'
  ```
- Obtenemos la bandera:
```text
academy{by73_5urf3r_217f681f}
```

## Notas Adicionales
- The Sleuth Kit (TSK) permite investigar la estructura interna del sistema de archivos directamente desde la imagen sin necesidad de montarla con privilegios de superusuario ni alterar metadatos.
- `fls -r` efectúa un recorrido recursivo por los inodos de directorios.
- `icat` permite volcar el contenido binario de un archivo especificando únicamente su número de inodo y el offset de la partición.
- Herramientas utilizadas en Kali Linux:
  - `gzip`: Descompresión del archivo de imagen.
  - `mmls`: Identificación de particiones y cálculo de offsets.
  - `fls`: Listado recursivo de archivos e inodos.
  - `icat`: Extracción de contenido a partir del inodo.
  - `tr`: Eliminación de bytes nulos (`\0`) de la cadena Unicode.

## Referencias
- [The Sleuth Kit (TSK) Documentation](https://www.sleuthkit.org/sleuthkit/docs.php)
- [fls(1) - Linux man page](https://manpages.debian.org/testing/sleuthkit/fls.1.en.html)
- [icat(1) - Linux man page](https://manpages.debian.org/testing/sleuthkit/icat.1.en.html)
