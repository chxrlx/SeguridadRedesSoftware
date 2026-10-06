## Descripción
We found this packet capture and key. Recover the flag.

## Solución
- El archivo `webnet1-capture.pcap` contiene tráfico de red cifrado con TLS/HTTPS, y contamos con la clave privada RSA en el archivo `picopico.key`.
- Al configurar la clave privada en Wireshark o `tshark` para descifrar las comunicaciones TLS, observamos que las cabeceras HTTP de respuesta contienen una bandera trampa o señuelo (`Pico-Flag: academy{this.is.not.your.flag.anymore}`).
- Al inspeccionar los objetos HTTP transferidos durante la sesión cifrada, encontramos la descarga de una imagen llamada `vulture.jpg`.
- Para extraer los objetos HTTP transferidos y analizar sus metadatos con `exiftool`:
  ```bash
  # Opción 1: Exportar objetos HTTP descifrados con tshark e inspeccionar metadatos
  tshark -r webnet1-capture.pcap -o "uat:rsa_keys:\"picopico.key\",\"\"" --export-objects "http,./"
  exiftool vulture.jpg | grep -i "Artist"

  # Opción 2: Buscar directamente en el volcado detallado de los paquetes descifrados
  tshark -r webnet1-capture.pcap -o "uat:rsa_keys:\"picopico.key\",\"\"" -Y "http" -V | grep -i "honey.roasted.peanuts"
  ```
- En los metadatos EXIF de la imagen `vulture.jpg`, específicamente en la etiqueta `Artist`, se encuentra la bandera legítima:
```text
academy{honey.roasted.peanuts}
```

## Notas Adicionales
- En Wireshark se puede realizar gráficamente yendo a *Edit* -> *Preferences* -> *Protocols* -> *TLS* -> *RSA keys list*, agregando la clave `picopico.key`, y luego exportando los objetos mediante *File* -> *Export Objects* -> *HTTP* -> Guardar `vulture.jpg`.
- Herramientas utilizadas en Kali Linux:
  - `tshark` / `Wireshark`: Para descifrar el tráfico TLS usando la clave RSA y exportar objetos HTTP.
  - `exiftool` / `strings`: Para inspeccionar los metadatos EXIF incrustados en la imagen `vulture.jpg`.

## Referencias
- [Wireshark TLS Decryption](https://gitlab.com/wireshark/wireshark/-/wikis/TLS)
- [Wireshark Export Objects](https://www.wireshark.org/docs/wsug_html_chunked/ChIOExportSection.html)
- [ExifTool by Phil Harvey](https://exiftool.org/)
