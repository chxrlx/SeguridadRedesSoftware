## Descripción

How about we take you on an adventure on exploring certificate signing requests? Take a look at this CSR file here.

## Solución

- Un archivo `.csr` es una **Certificate Signing Request** (solicitud de firma de certificado). Contiene información sobre el solicitante, una clave pública y una firma que demuestra la posesión de la clave privada correspondiente.
- Inspeccionamos el CSR con OpenSSL:
  ```bash
  openssl req -in readmycert.csr -noout -text
  ```
- En la salida, dentro del campo `Subject`, aparece:
  ```text
  Subject: CN=academy{read_mycert_a209fd00}, name=ctfPlayer
  ```
- El valor del atributo `CN` (Common Name) contiene directamente la bandera:
  ```text
  academy{read_mycert_a209fd00}
  ```

## Notas Adicionales

- El CSR utiliza una clave RSA de 2048 bits y el algoritmo de firma `sha256WithRSAEncryption`.
- También contiene la extensión solicitada `TLS Web Client Authentication`, pero no es necesario analizarla para obtener la bandera.
- El campo `CN` es uno de los atributos habituales del nombre distinguido (`Subject`) de una solicitud de certificado.

## Referencias

- [OpenSSL `req` command](https://docs.openssl.org/3.0/man1/openssl-req/)
- [Certificate signing request - Wikipedia](https://en.wikipedia.org/wiki/Certificate_signing_request)
