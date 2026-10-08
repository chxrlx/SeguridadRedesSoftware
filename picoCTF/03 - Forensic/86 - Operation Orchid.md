## Descripción
Download this disk image and find the flag.

## Solución
- Descomprimimos la imagen con `gzip`:
  ```bash
  gzip -dk disk.flag.img.gz
  ```
- Inspeccionamos las particiones del archivo de imagen con `mmls`:
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
  003:  000:001   0000206848   0000411647   0000204800   Linux Swap / Solaris x86 (0x82)
  004:  000:002   0000411648   0000819199   0000407552   Linux (0x83)
  ```
- La partición raíz de Linux se encuentra en el offset sector `411648`.
- Listamos los archivos del directorio `/root` usando `fls`:
  ```bash
  fls -p -r -o 411648 disk.flag.img | grep "^r/.*root/"
  ```
  Salida:
  ```text
  r/r 1875:	root/.ash_history
  r/r * 1876(realloc):	root/flag.txt
  r/r 1782:	root/flag.txt.enc
  ```
- Inspeccionamos el historial de comandos en `root/.ash_history` (inodo `1875`) usando `icat`:
  ```bash
  icat -o 411648 disk.flag.img 1875
  ```
  Salida:
  ```sh
  touch flag.txt
  nano flag.txt 
  apk get nano
  apk --help
  apk add nano
  nano flag.txt 
  openssl
  openssl aes256 -salt -in flag.txt -out flag.txt.enc -k unbreakablepassword1234567
  shred -u flag.txt
  ls -al
  halt
  ```
- El historial revela que `flag.txt` fue cifrado con OpenSSL utilizando el algoritmo AES-256 y la contraseña `unbreakablepassword1234567`.
- Extraemos el archivo cifrado `flag.txt.enc` (inodo `1782`) y lo desciframos con `openssl`:
  ```bash
  icat -o 411648 disk.flag.img 1782 > flag.txt.enc
  openssl aes256 -d -salt -in flag.txt.enc -out flag.txt -k unbreakablepassword1234567
  cat flag.txt
  ```
- Obtenemos la bandera:
```text
academy{h4un71ng_p457_0b36d810}
```

## Notas Adicionales
- La inspección de los archivos de historial de la shell (`.bash_history`, `.ash_history`, `.zsh_history`) es una técnica clave en análisis forense para descubrir acciones previas realizadas por los usuarios o atacantes.
- En este caso, el comando de cifrado y la clave secreta quedaron registrados en texto plano en `.ash_history`.
- Herramientas utilizadas en Kali Linux:
  - `gzip`: Descompresión de la imagen de disco.
  - `mmls`: Análisis de la tabla de particiones MBR.
  - `fls`: Exploración del sistema de archivos e identificación de inodos.
  - `icat`: Extracción de archivos a partir de su inodo.
  - `openssl`: Descifrado simétrico AES-256.

## Referencias
- [The Sleuth Kit (TSK) - fls Documentation](https://wiki.sleuthkit.org/index.php?title=Fls)
- [The Sleuth Kit (TSK) - icat Documentation](https://wiki.sleuthkit.org/index.php?title=Icat)
- [OpenSSL enc Manual](https://www.openssl.org/docs/manmaster/man1/openssl-enc.html)
