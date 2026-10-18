## Descripción
Now you DON'T see me. This report has some critical data in it, some of which have been redacted correctly, while some were not. Can you find an important key that was not redacted properly?

## Solución
- El archivo proporcionado es un documento PDF (`Financial_Report_for_ABC_Labs.pdf`) que aparenta tener información censurada con un bloque negro sobre el texto.
- En muchos casos de redacción incorrecta en PDFs, la capa de texto permanece incrustada en el archivo y solo se encuentra visualmente cubierta por una figura geométrica de color negro (rectángulo), sin haber sido eliminada del flujo de contenido.
- Podemos extraer el texto completo subyacente utilizando la herramienta `pdftotext`:
  ```bash
  pdftotext Financial_Report_for_ABC_Labs.pdf -
  ```
- Salida generada:
  ```text
  Financial Report for ABC Labs, Kigali, Rwanda for the year 2021.
  Breakdown - Just painted over in MS word.

  Cost Benefit Analysis
  Credit

  Debit

  This is not the flag, keep looking

  Expenses from the
  academy{C4n_Y0u_S33_m3_fully}

  Redacted document.
  ```
- También es posible filtrar directamente el patrón de la bandera:
  ```bash
  pdftotext Financial_Report_for_ABC_Labs.pdf - | grep -o "academy{[^}]*}"
  ```
- Obtenemos la bandera:
```text
academy{C4n_Y0u_S33_m3_fully}
```

## Notas Adicionales
- El fallo de redacción ocurre frecuentemente en entornos corporativos cuando los usuarios aplican barras negras, resaltadores o formas geométricas sobre texto sensible en procesadores de texto (como MS Word o LibreOffice) antes de exportar a PDF, creyendo erróneamente que el contenido fue destruido.
- Para realizar una redacción segura y permanente de documentos PDF, se deben utilizar herramientas de redacción que rastericen la zona afectada o eliminen completamente los objetos de texto y metadatos subyacentes.
- Herramientas utilizadas en Kali Linux:
  - `pdftotext`: Extracción de contenido de texto sin formato de documentos PDF.
  - `grep`: Búsqueda y filtrado de patrones de texto.

## Referencias
- [pdftotext(1) - Linux man page](https://manpages.debian.org/testing/poppler-utils/pdftotext.1.en.html)
- [NSA Guidance on How to Protect Sensitive Information in PDF Documents](https://media.defense.gov/2021/Aug/04/2002821835/-1/-1/0/REDACTION_GUIDANCE.PDF)
