## Descripción
We found this packet capture. Recover the flag that was transmitted in it.

## Solución
- Analizamos la captura de paquetes `shark-on-wire-2-capture.pcap` en Kali Linux utilizando `tshark` o `scapy`.
- Al filtrar el tráfico UDP con destino al puerto 22 (`udp.dstport == 22`), se observa una transmisión encubierta que inicia y finaliza con el puerto de origen `5000` (cuyos payloads contienen los textos `start` y `end`).
- En los paquetes intermedios, cada carácter de la bandera se encuentra codificado en el puerto de origen UDP (*source port*), utilizando la fórmula `5000 + código_ascii` (por ejemplo, el puerto `5097` representa `5097 - 5000 = 97`, que equivale al carácter `'a'`).
- Podemos extraer estos puertos y decodificarlos directamente en terminal con `tshark` y `awk`, o mediante Python:
  ```bash
  # Opción 1: Con tshark y awk
  tshark -r shark-on-wire-2-capture.pcap -Y "udp.dstport == 22" -T fields -e udp.srcport | awk '$1 > 5000 {printf "%c", $1-5000} END {print ""}'

  # Opción 2: Script en Python con Scapy
  python3 -c '
  from scapy.all import rdpcap, UDP
  packets = rdpcap("shark-on-wire-2-capture.pcap")
  flag = [chr(p[UDP].sport - 5000) for p in packets if p.haslayer(UDP) and p[UDP].dport == 22 and 5000 < p[UDP].sport < 5256]
  print("".join(flag))
  '
  ```
- Al ejecutar el comando se reconstruye la bandera transmitida:
```text
academy{p1LLf3r3d_data_v1a_st3g0}
```

## Notas Adicionales
- La técnica empleada es la exfiltración de información mediante un canal encubierto (*covert channel*), ocultando datos dentro de los campos de control de protocolo (en este caso, los puertos efímeros UDP) en vez de en el cuerpo del mensaje.
- Herramientas utilizadas en Kali Linux:
  - `tshark`: Herramienta de línea de comandos de Wireshark para filtrado e inspección de tráfico de red.
  - `awk`: Utilidad de procesamiento de texto para restar el offset y convertir valores numéricos a caracteres ASCII.
  - `scapy`: Biblioteca de análisis y manipulación de paquetes en Python.

## Referencias
- [tshark(1) - Wireshark CLI](https://www.wireshark.org/docs/man-pages/tshark.html)
- [Covert Channel - Wikipedia](https://en.wikipedia.org/wiki/Covert_channel)
- [Scapy Documentation](https://scapy.readthedocs.io/en/latest/)
