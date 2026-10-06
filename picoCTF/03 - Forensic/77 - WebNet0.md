## Descripción
We found this packet capture and key. Recover the flag.

## Solución
- El archivo `webnet0-capture.pcap` contiene tráfico de red cifrado mediante TLS/HTTPS, y disponemos de la clave privada RSA en el archivo `picopico.key`.
- Para descifrar las comunicaciones TLS en Kali Linux podemos utilizar `tshark` (o `Wireshark`) cargando la clave privada RSA:
  - **En Wireshark (interfaz gráfica)**:
    Ir a *Edit* -> *Preferences* -> *Protocols* -> *TLS* -> *RSA keys list* -> *Edit...*, agregar una nueva entrada seleccionando el archivo `picopico.key`. Luego filtrar por `http` e inspeccionar los paquetes descifrados.
  - **En tshark (línea de comandos)**:
    Pasamos la clave privada mediante la preferencia `uat:rsa_keys` e inspeccionamos las cabeceras HTTP de respuesta:
    ```bash
    tshark -r webnet0-capture.pcap -o "uat:rsa_keys:\"picopico.key\",\"\"" -Y "http" -T fields -e http.response.line | grep -o "academy{[^}]*}" | head -n 1
    ```
- Al descifrar el tráfico HTTP, se observa una cabecera personalizada del servidor (`Pico-Flag`) que expone la bandera:
```text
academy{nongshim.shrimp.crackers}
```

## Notas Adicionales
- Al no utilizar algoritmos de intercambio de claves con secreto perfecto hacia adelante (*Forward Secrecy* como DHE o ECDHE), el conocimiento de la clave privada del servidor permite a un atacante o analista forense descifrar el tráfico capturado previamente.
- Herramientas utilizadas en Kali Linux:
  - `tshark` / `Wireshark`: Analizadores de red con capacidad de descifrado TLS mediante claves RSA.

## Referencias
- [Wireshark TLS Decryption](https://gitlab.com/wireshark/wireshark/-/wikis/TLS)
- [tshark(1) - Wireshark CLI](https://www.wireshark.org/docs/man-pages/tshark.html)
