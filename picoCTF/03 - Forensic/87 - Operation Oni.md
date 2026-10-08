## Descripción
Download this disk image and find the key. The key will be used to log into the remote machine.

## Solución
- Descomprimimos la imagen con `gzip`:
  ```bash
  gzip -dk disk.img.gz
  ```
- Inspeccionamos la tabla de particiones del disco mediante `mmls`:
  ```bash
  mmls disk.img
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
  003:  000:001   0000206848   0000471039   0000264192   Linux (0x83)
  ```
- La segunda partición Linux (iniciando en el sector `206848`) contiene el sistema de archivos principal.
- Exploramos el directorio `/root` usando `fls`:
  ```bash
  fls -p -r -o 206848 disk.img 470
  ```
  Salida:
  ```text
  r/r 2344:	.ash_history
  d/d 3916:	.ssh
  r/r 2345:	.ssh/id_ed25519
  r/r 2346:	.ssh/id_ed25519.pub
  ```
- Extraemos la clave privada SSH (`id_ed25519`, inodo `2345`) usando `icat` y ajustamos sus permisos a `600`:
  ```bash
  icat -o 206848 disk.img 2345 > id_ed25519
  chmod 600 id_ed25519
  ```
- Nos conectamos por SSH a la instancia del reto con el usuario `ctf-player` y leemos el archivo `flag.txt`:
  ```bash
  ssh -i id_ed25519 -p 22788 -o StrictHostKeyChecking=no ctf-player@xebec.cylabacademy.net "cat flag.txt"
  ```
- Obtenemos la bandera:
```text
academy{k3y_5l3u7h_0e000cd7}
```

## Notas Adicionales
- Las claves privadas SSH requieren permisos estrictos (`chmod 600`) para que el cliente OpenSSH permita su uso por razones de seguridad.
- The Sleuth Kit permite extraer archivos sensibles (como claves privadas o historiales de shell) directamente de imágenes forenses sin necesidad de montar la imagen en el sistema operativo anfitrión.
- Herramientas utilizadas en Kali Linux:
  - `gzip`: Descompresión del archivo de imagen.
  - `mmls`: Listado de particiones y obtención de offsets.
  - `fls`: Listado de archivos en la partición.
  - `icat`: Extracción de la clave privada `id_ed25519` por su inodo.
  - `ssh`: Conexión remota autenticada con clave privada.

## Referencias
- [The Sleuth Kit (TSK) - icat Manual](https://wiki.sleuthkit.org/index.php?title=Icat)
- [OpenSSH Client Documentation](https://www.openssh.com/manual.html)
- [SSH Key Permissions (chmod 600)](https://www.ssh.com/academy/ssh/keygen)
