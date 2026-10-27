## Descripción
Your mission is to enter Dr. Evil's laboratory and retrieve the blueprints for his Doomsday Project. The laboratory is protected by a series of locked vault doors. Each door is controlled by a computer and requires a password to open. Unfortunately, our undercover agents have not been able to obtain the secret passwords for the vault doors, but one of our junior agents obtained the source code for each vault's computer! You will need to read the source code for each level to figure out what the password is for that vault door. As a warmup, we have created a replica vault in our training facility.

The source code for the training vault is here: VaultDoorTraining.java

## Solución
- Analizamos el código fuente proporcionado en Java:
  ```java
  import java.util.*;

  class VaultDoorTraining {
      public static void main(String args[]) {
          VaultDoorTraining vaultDoor = new VaultDoorTraining();
          Scanner scanner = new Scanner(System.in); 
          System.out.print("Enter vault password: ");
          String userInput = scanner.next();
          String input = userInput.substring("academy{".length(),userInput.length()-1);
          if (vaultDoor.checkPassword(input)) {
              System.out.println("Access granted.");
          } else {
              System.out.println("Access denied!");
          }
     }

      // The password is below. Is it safe to put the password in the source code?
      // What if somebody stole our source code? Then they would know what our
      // password is. Hmm... I will think of some ways to improve the security
      // on the other doors.
      //
      // -Minion #9567
      public boolean checkPassword(String password) {
          return password.equals("w4rm1ng_Up_w1tH_jAv4_1633b42d7f1");
      }
  }
  ```
- En el método `main`, el programa extrae el contenido interno de la bandera removiendo el prefijo `academy{` y el caracter final `}` con `userInput.substring("academy{".length(), userInput.length()-1)`.
- El método `checkPassword` compara directamente la entrada con la cadena en texto claro:
  ```java
  return password.equals("w4rm1ng_Up_w1tH_jAv4_1633b42d7f1");
  ```
- Por lo tanto, concatenando el prefijo y sufijo del formato de la bandera obtenemos la bandera completa:
```text
academy{w4rm1ng_Up_w1tH_jAv4_1633b42d7f1}
```

## Notas Adicionales
- Este reto inicial de ingeniería inversa ilustra una vulnerabilidad clásica: el almacenamiento de credenciales o secretos en texto claro (hardcoded credentials) dentro del código fuente de la aplicación.
- Herramientas utilizadas:
  - Inspección directa de código fuente con editor de texto/cat.
  - Compilador de Java (`javac` y `java`) para verificar la ejecución y validación de la contraseña.

## Referencias
- [CWE-798: Use of Hard-coded Credentials](https://cwe.mitre.org/data/definitions/798.html)
