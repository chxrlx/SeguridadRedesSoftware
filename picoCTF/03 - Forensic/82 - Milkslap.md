## Descripción
🥛

## Solución
- El reto presenta un sitio web con una animación interactiva (*MilkSlap!*). Al revisar el archivo `style.css` del servidor, identificamos que la animación utiliza como imagen de fondo un sprite sheet vertical llamado `concat_v.png`:
  ```bash
  curl -s http://xebec.cylabacademy.net:14115/style.css | grep "background-image"
  # Salida: background-image: url(concat_v.png);
  ```
- Descargamos la imagen del servidor web:
  ```bash
  wget http://xebec.cylabacademy.net:14115/concat_v.png
  ```
- Tal como sugiere la pista *"Look at the problem category"*, no es una vulnerabilidad web sino un reto de **Informática Forense / Esteganografía en imágenes**.
- Analizamos la imagen con `zsteg` o Python para buscar texto oculto en LSB. Debido a las grandes dimensiones de la imagen (1280x47520), se ajusta la pila de ejecución de Ruby (`RUBY_THREAD_VM_STACK_SIZE`) o se analiza el canal azul (`-c b -b 1`):
  ```bash
  # Opción 1: zsteg ajustando la pila de Ruby
  export RUBY_THREAD_VM_STACK_SIZE=500000000
  zsteg -c b -b 1 concat_v.png | grep -o "academy{[^}]*}"

  # Opción 2: Extracción directa en Python (canal azul / LSB)
  python3 -c '
  from PIL import Image
  im = Image.open("concat_v.png")
  crop = im.crop((0, 0, im.width, 10)).convert("RGB")
  bits = [(crop.getpixel((x, y))[2] & 1) for y in range(crop.height) for x in range(crop.width)]
  chars = "".join(chr(int("".join(map(str, bits[i:i+8])), 2)) for i in range(0, len(bits), 8))
  print(chars[chars.find("academy{"):chars.find("}")+1])
  '
  ```
- La bandera se encuentra incrustada en el bit menos significativo del canal azul (`b1,b,lsb,xy`):
```text
academy{imag3_m4n1pul4t10n_sl4p5}
```

## Notas Adicionales
- La aplicación web sirve únicamente como vector de entrega para el recurso gráfico contenedor.
- Herramientas utilizadas en Kali Linux:
  - `curl` / `wget`: Para descargar y analizar las dependencias del servidor web.
  - `zsteg`: Herramienta de detección de esteganografía en imágenes PNG/BMP.
  - `python3` (módulo `Pillow`): Para procesar los píxeles y decodificar el canal azul de la imagen.

## Referencias
- [zsteg GitHub Repository](https://github.com/zed-0xff/zsteg)
- [Least Significant Bit (LSB) Steganography](https://en.wikipedia.org/wiki/Steganography)
