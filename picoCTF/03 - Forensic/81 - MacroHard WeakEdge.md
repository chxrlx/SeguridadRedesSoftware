## Descripción
I've hidden a flag in this file. Can you find it? Forensics is fun!
## Solución
El archivo proporcionado, `Forensics_is_fun.pptm`, cuenta con la extensión de una presentación de Microsoft PowerPoint habilitada para macros.

Los formatos modernos de documentos ofimáticos de Microsoft Office (OOXML, tales como `.docx`, `.xlsx` o `.pptm`) son en realidad contenedores comprimidos en formato **ZIP**. Por lo tanto, es posible descomprimir su contenido para inspeccionar la jerarquía interna de carpetas y archivos XML.

1. **Extracción del contenedor:**
    
    Descomprimimos el archivo directamente utilizando la herramienta `unzip`:
    
    ```
    unzip Forensics_is_fun.pptm -d ppt_extracted
    cd ppt_extracted
    ```
    
2. **Búsqueda e identificación de archivos anómalos:**
    
    Inspeccionamos la estructura de directorios en busca de elementos que no formen parte de la plantilla estándar de OpenXML:
    
    ```
    find . -type f
    ```
    
    Entre los recursos comunes (`ppt/slides/`, `ppt/theme/`, etc.), se identifica un archivo inusual dentro del directorio de patrones de diapositivas:
    
    ```
    ppt/slideMasters/hidden
    ```
    
3. **Análisis y decodificación del contenido:**
    
    Al consultar el contenido de `ppt/slideMasters/hidden`:
    
    ```
    cat ppt/slideMasters/hidden
    ```
    
    Se obtiene la siguiente cadena de caracteres separados por espacios:
    
    ```
    Z m x h Z z o g c G l j b 0 N U R n t E M W R f d V 9 r b j B 3 X 3 B w d H N f c l 9 6 M X A 1 f Q
    ```
    
    La cadena corresponde a texto codificado en **Base64** con espacios intercalados entre cada carácter. Procedemos a eliminar los espacios mediante `tr` y enviar el flujo al decodificador `base64`:
    
    ```
    cat ppt/slideMasters/hidden | tr -d ' ' | base64 -d
    ```
    
    Obteniendo como resultado la bandera:
    
    ```
    flag: academy{D1d_u_kn0w_ppts_r_z1p5}
    ```
## Notas Adicionales
- Herramientas utilizadas en Kali Linux:
    
    - `unzip`: Para extraer los componentes y subdirectorios del archivo `.pptm`.
        
    - `find`: Para auditar la estructura interna y localizar ficheros sospechosos o ajenos a la especificación estándar.
        
    - `tr`: Para sanear la cadena eliminando los espacios en blanco intercalados.
        
    - `base64`: Para revertir la codificación Base64 y obtener el texto legible en texto plano.
        
## Referencias