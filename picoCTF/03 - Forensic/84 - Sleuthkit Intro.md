## Descripción
Download the disk image and use `mmls` on it to find the size of the Linux partition. Connect to the remote checker service to check your answer and get the flag.

## Solución
- Descomprimimos la imagen de disco con `gzip`:
  ```bash
  gzip -dk disk.img.gz
  ```
- Utilizamos la herramienta `mmls` de The Sleuth Kit (TSK) para inspeccionar la tabla de particiones del archivo `disk.img`:
  ```bash
  mmls disk.img
  ```
  Salida obtenida:
  ```text
  DOS Partition Table
  Offset Sector: 0
  Units are in 512-byte sectors

        Slot      Start        End          Length       Description
  000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
  001:  -------   0000000000   0000002047   0000002048   Unallocated
  002:  000:000   0000002048   0000204799   0000202752   Linux (0x83)
  ```
- Observamos que la longitud en sectores (512-byte sectors) de la partición Linux (`002: 000:000`) es `202752`.
- Nos conectamos al servicio mediante netcat y enviamos el tamaño solicitado:
  ```bash
  echo "202752" | nc chatelaine.cylabacademy.net 41650
  ```
- El servicio valida el resultado y devuelve la bandera:
```text
academy{mm15_f7w!}
```

## Notas Adicionales
- `mmls` muestra el diseño del sistema de particiones en una imagen de disco, permitiendo identificar los desplazamientos (offsets), tamaños en sectores y tipos de partición sin necesidad de montar la imagen.
- Herramientas utilizadas en Kali Linux:
  - `gzip`: Para descomprimir la imagen `disk.img.gz`.
  - `mmls`: Herramienta de The Sleuth Kit para listar particiones.
  - `nc` (netcat): Para comunicarse con el servicio TCP remoto e ingresar la longitud calculada.

## Referencias
- [The Sleuth Kit (TSK) - mmls tool documentation](https://wiki.sleuthkit.org/index.php?title=Mmls)
- [mmls(1) - Linux man page](https://manpages.debian.org/testing/sleuthkit/mmls.1.en.html)
