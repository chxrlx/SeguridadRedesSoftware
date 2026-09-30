## Descripción
Decode this message from the moon.

## Solución
- El archivo `message.wav` contiene una señal de audio analógica codificada en **SSTV** (*Slow-Scan Television*), una técnica utilizada en transmisiones espaciales para enviar imágenes por ondas de radio.
- En Kali Linux podemos decodificar las transmisiones SSTV utilizando la herramienta gráfica `qsstv` o mediante un script en Python con la biblioteca `sstv`:
  ```bash
  # Instalar decodificador sstv
  python3 -m pip install sstv --break-system-packages

  # Decodificar el audio a imagen (detecta automáticamente el modo Scottie 1)
  python3 -c '
  import sstv
  images = sstv.decode_from_wav("message.wav")
  # La imagen resultante se rota 180 grados para facilitar su lectura
  img = images[0].rotate(180)
  img.save("result.png")
  '
  ```
- Al decodificar el audio y observar la imagen resultante (en el modo *Scottie 1*), aparece una nota adhesiva amarilla donde se lee claramente la bandera:
```text
picoCTF{beep_boop_im_in_space}
```

## Notas Adicionales
- SSTV fue el formato utilizado por misiones como Apollo 11 para transmitir imágenes de televisión desde la Luna a la Tierra.

## Referencias
- [Slow-scan television - Wikipedia](https://en.wikipedia.org/wiki/Slow-scan_television)
- [sstv Python Package](https://pypi.org/project/sstv/)
- [QSSTV - Open Source SSTV and DRM Decoder](http://users.telenet.be/on4qz/qsstv/)