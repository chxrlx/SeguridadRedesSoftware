## Descripción
We found this [packet capture](https://challenge-files.cylabacademy.net/library/e64f2c2aaf9bf531af7be1787c1407c47a4cc7b57f251d5108a1151e9721a2d6/shark-on-wire-1-capture.pcap). Recover the flag.
## Solución
- Analizamos la captura de paquetes `shark-on-wire-1-capture.pcap` en Kali Linux inspeccionando los flujos (streams) UDP con `tshark` o `Wireshark`.
- Al revisar las conversaciones UDP, observamos tráfico enviado desde `10.0.0.2:5000` hacia varias IPs de destino.
- El flujo UDP 6 (`udp.stream eq 6`), correspondiente al destino `10.0.0.12:8888`, contiene un carácter por paquete que al concatenarse revela la bandera legítima:
  ```bash
  # Opción 1: Seguir el flujo UDP 6 con tshark
  tshark -r shark-on-wire-1-capture.pcap -q -z follow,udp,ascii,6

  # Opción 2: Extraer y decodificar el payload hexadecimal directamente
  tshark -r shark-on-wire-1-capture.pcap -Y "ip.dst == 10.0.0.12 && udp" -T fields -e data | tr -d '\n' | xxd -r -p; echo

  # Opción 3: Script rápido en Python con Scapy
  python3 -c '
  from scapy.all import rdpcap, Raw, IP
  packets = rdpcap("shark-on-wire-1-capture.pcap")
  flag = "".join(p[Raw].load.decode(errors="ignore") for p in packets if p.haslayer(Raw) and p.haslayer(IP) and p[IP].dst == "10.0.0.12")
  print(flag)
  '
  ```
- Nota: En el flujo 7 (`10.0.0.13:8888`) se encuentra una bandera falsa (`academy{N0t_a_fLag}`). La bandera correcta se encuentra en el flujo 6.
```text
academy{StaT31355_636f6e6e}
```

## Notas Adicionales
- El nombre de la bandera hace referencia al hecho de que UDP es un protocolo no orientado a conexión (*stateless connection*).

## Referencias
- [tshark(1) - Wireshark CLI](https://www.wireshark.org/docs/man-pages/tshark.html)
- [Wireshark - Following Protocol Streams](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowStreamSection.html)
- [Scapy Documentation](https://scapy.readthedocs.io/en/latest/)