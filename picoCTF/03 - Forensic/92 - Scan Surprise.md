## Descripción
I've gotten bored of handing out flags as text. Wouldn't it be cool if they were an image instead?

## Solución
- Descomprimimos el archivo `challenge.zip` para inspeccionar su contenido:
  ```bash
  unzip challenge.zip
  ```
- El archivo contiene una imagen PNG dentro del directorio `home/ctf-player/drop-in/flag.png`.
- Al analizar la imagen o visualizarla, comprobamos que se trata de un código QR.
- Para decodificar el código QR desde la terminal en Linux, podemos utilizar herramientas como `zbarimg` (del paquete `zbar-tools`) o un script en Python con la biblioteca `pyzbar` / `Pillow`:
  ```bash
  # Opción 1: Con zbarimg
  zbarimg home/ctf-player/drop-in/flag.png

  # Opción 2: Con Python y pyzbar
  python3 -c "
  from PIL import Image
  from pyzbar.pyzbar import decode
  data = decode(Image.open('home/ctf-player/drop-in/flag.png'))
  for d in data:
      print(d.data.decode('utf-8'))
  "
  ```
- Salida obtenida:
```text
academy{p33k_@_b00_5f0d36e7}
```

## Notas Adicionales
- Los códigos QR (Quick Response) son matrices bidimensionales diseñadas para codificar datos alfanuméricos mediante patrones geométricos de alto contraste y mecanismos de corrección de errores (Reed-Solomon).
- Herramientas utilizadas en Kali Linux:
  - `unzip`: Descompresión del archivo ZIP del reto.
  - `python3` con `pyzbar` y `Pillow`: Lectura y decodificación programática de códigos QR e imágenes.
  - `zbar-tools` (`zbarimg`): Utilidad CLI para lectura de códigos de barras y QR.

## Referencias
- [QR Code Technology Standard (ISO/IEC 18004)](https://www.qrcode.com/en/about/standards.html)
- [pyzbar Documentation](https://pypi.org/project/pyzbar/)
- [zbarimg(1) - Linux man page](https://manpages.debian.org/testing/zbar-tools/zbarimg.1.en.html)
