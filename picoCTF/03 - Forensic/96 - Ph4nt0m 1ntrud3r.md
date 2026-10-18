## Descripción
A digital ghost has breached my defenses, and my sensitive data has been stolen! Your mission is to uncover how this phantom intruder infiltrated my system and retrieve the hidden flag. To solve this challenge, you'll need to analyze the provided PCAP file and track down the attack method. The attacker has cleverly concealed his moves in well timely manner. Dive into the network traffic, apply the right filters and show off your forensic prowess and unmask the digital intruder!

## Solución
- Analizamos la captura de tráfico de red `myNetworkTraffic.pcap` con `tshark`:
  ```bash
  tshark -r myNetworkTraffic.pcap
  ```
- Observamos una serie de 22 paquetes TCP transmitidos desde `10.200.0.2` hacia `192.168.1.2:80` con la bandera SYN activa pero con carga útil (payload) inusual.
- Al inspeccionar el contenido de los paquetes, encontramos cadenas codificadas en Base64 en las cargas útiles de TCP. La mayoría de los paquetes son paquetes de relleno de longitud 8, mientras que los fragmentos de la bandera se encuentran en paquetes con longitudes de carga útil de 12 bytes (y el paquete final de 4 bytes).
- Además, como se menciona en la descripción (*"in well timely manner"*), los paquetes en el archivo pcap no se encuentran en orden cronológico.
- Filtramos los paquetes que contienen los fragmentos válidos (`tcp.len == 12 || tcp.len == 4`), los ordenamos por marca de tiempo (`frame.time_epoch`), convertimos la carga útil de hexadecimal a texto y decodificamos desde Base64:
  ```bash
  tshark -r myNetworkTraffic.pcap -Y "tcp.len == 12 || tcp.len == 4" -T fields -e frame.time_epoch -e tcp.payload | sort -n | awk '{print $2}' | xxd -r -p | base64 -d
  ```
- Fragmentos decodificados en orden temporal:
  - `YWNhZGVteQ==` -> `academy`
  - `ezF0X3c0cw==` -> `{1t_w4s`
  - `bnRfdGg0dA==` -> `nt_th4t`
  - `XzM0c3lfdA==` -> `_34sy_t`
  - `YmhfNHJfNg==` -> `bh_4r_6`
  - `YjA5ODYxZg==` -> `b09861f`
  - `fQ==`         -> `}`
- Concatenando los fragmentos obtenemos la bandera completa:
```text
academy{1t_w4snt_th4t_34sy_tbh_4r_6b09861f}
```

## Notas Adicionales
- La técnica utilizada por el atacante consiste en exfiltración encubierta de datos a través de paquetes TCP SYN modificados fuera de orden temporal para evadir inspecciones de tráfico simples.
- `tshark` permite extraer campos específicos de paquetes y aplicar filtros de visualización complejos para análisis forense de tráfico de red.
- Herramientas utilizadas en Kali Linux:
  - `tshark`: Analizador de paquetes de red en línea de comandos.
  - `sort`: Ordenamiento numérico por marca de tiempo (timestamp).
  - `awk`: Extracción de columnas de texto.
  - `xxd`: Conversión de datos hexadecimales a representación binaria.
  - `base64`: Decodificación de cadenas en Base64.

## Referencias
- [tshark(1) - Wireshark Network Analyzer](https://www.wireshark.org/docs/man-pages/tshark.html)
- [Wireshark Display Filter Reference: TCP](https://www.wireshark.org/docs/dfref/t/tcp.html)
