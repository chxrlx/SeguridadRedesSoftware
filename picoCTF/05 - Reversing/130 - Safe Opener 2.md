## Descripción
What can you do with this file? I forgot the key to my safe but this file is supposed to help me with retrieving the lost key. Can you help me unlock my safe?

## Solución
- El archivo proporcionado es un archivo compilado de bytecode de Java (`SafeOpener.class`).
- Para inspeccionar el contenido del archivo compilado, podemos utilizar la herramienta de desensamblado `javap` que incluye el JDK:
  ```bash
  javap -c -p SafeOpener.class
  ```
- Al desensamblar el método `openSafe`, observamos directamente la comparación de cadenas:
  ```java
  public static boolean openSafe(java.lang.String);
    Code:
         0: ldc           #24                 // String academy{SAf3_0p3n3rr_y0u_solv3d_it_501f2cf5}
         2: astore_1
         3: aload_0
         4: aload_1
         5: invokevirtual #25                 // Method java/lang/String.equals:(Ljava/lang/Object;)Z
         8: ifeq          21
        11: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
        14: ldc           #26                 // String Sesame open
        16: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        19: iconst_1
        20: ireturn
        21: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
        24: ldc           #27                 // String Password is incorrect\n
        26: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
        29: iconst_0
        30: ireturn
  ```
- De manera alternativa, también es posible recuperar la cadena directamente mediante la utilidad `strings`:
  ```bash
  strings SafeOpener.class | grep "academy{"
  ```
- La cadena requerida para desbloquear la caja fuerte es la bandera del reto:
```text
academy{SAf3_0p3n3rr_y0u_solv3d_it_501f2cf5}
```

## Notas Adicionales
- Los archivos `.class` de Java almacenan constantes (cadenas de texto, nombres de clases, métodos, firmas) en la tabla del pool de constantes (*Constant Pool*). A diferencia de los binarios nativos compilados y optimizados/despojados de símbolos, las cadenas literales en los archivos de clase de Java son inmediatamente visibles e inspeccionables mediante descompiladores (como Jadx, CFR o Fernflower), desensambladores (`javap`) o utilidades de inspección de texto plano (`strings`).
- Herramientas utilizadas:
  - `javap`: Desensamblador oficial de bytecode Java.
  - `strings`: Extracción de secuencias imprimibles del binario compilado.

## Referencias
- [javap - Java Class File Disassembler](https://docs.oracle.com/en/java/javase/17/docs/specs/man/javap.html)
- [The Java Virtual Machine Specification - The Constant Pool](https://docs.oracle.com/javase/specs/jvms/se17/html/jvms-4.html#jvms-4.4)
