## Descripción
Have you heard of Rust? Fix the syntax errors in this Rust file to print the flag!
Recursos: `fixme1.tar.gz`

## Solución
- Nos ubicamos en la carpeta y extraemos el contenido del archivo comprimido `fixme1.tar.gz`:
  ```bash
  tar -zxvf fixme1.tar.gz
  cd fixme1
  ```
- Al inspeccionar el archivo `fixme1/src/main.rs`, identificamos tres errores de sintaxis básicos en Rust acompañados de comentarios guía:
  1. **Falta de punto y coma (`;`):**
     ```rust
     // Original:
     let key = String::from("CSUCKS") // How do we end statements in Rust?
     // Corregido:
     let key = String::from("CSUCKS");
     ```
  2. **Instrucción de retorno inválida (`ret`):**
     ```rust
     // Original:
     ret; // How do we return in rust?
     // Corregido:
     return;
     ```
  3. **Especificador de formato erróneo en macro `println!`:**
     ```rust
     // Original:
     println!(
         ":?", // How do we print out a variable in the println function? 
         String::from_utf8_lossy(&decrypted_buffer)
     );
     // Corregido:
     println!(
         "{:?}", 
         String::from_utf8_lossy(&decrypted_buffer)
     );
     ```
- Código corregido completo de `src/main.rs`:
  ```rust
  use xor_cryptor::XORCryptor;

  fn main() {
      // Key for decryption
      let key = String::from("CSUCKS");

      // Encrypted flag values
      let hex_values = ["71", "35", "11", "73", "2f", "17", "53", "71", "01", "1c", "7e", "59", "63", "e1", "61", "25", "7f", "5a", "60", "50", "11", "38", "1f", "3a", "60", "e9", "62", "20", "0c", "e6", "50", "d3", "35"];

      // Convert the hexadecimal strings to bytes and collect them into a vector
      let encrypted_buffer: Vec<u8> = hex_values.iter()
          .map(|&hex| u8::from_str_radix(hex, 16).unwrap())
          .collect();

      // Create decrpytion object
      let res = XORCryptor::new(&key);
      if res.is_err() {
          return;
      }
      let xrc = res.unwrap();

      // Decrypt flag and print it out
      let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);
      println!(
          "{:?}", 
          String::from_utf8_lossy(&decrypted_buffer)
      );
  }
  ```
- Compilamos y ejecutamos el proyecto con Cargo (o emulamos el algoritmo en Python):
  ```bash
  cargo run
  ```
- La salida muestra la bandera descifrada:

```text
academy{4r3_y0u_4_ru$t4c30n_n0w?}
```

## Notas Adicionales
- **Sintaxis de Rust**: Cada declaración de variable o llamada a función que no actúa como valor de retorno de una expresión debe finalizar con punto y coma (`;`). La palabra clave para retorno anticipado es `return`.
- **Macros de impresión y formateo**: La macro `println!` utiliza delimitadores `{}` para evaluar e imprimir variables según el trait `Display`, o `{:?}` para formateo de depuración según el trait `Debug`.
- **Gestión de dependencias con Cargo**: Cargo administra la compilación, resolución de dependencias (especificadas en `Cargo.toml`) y ejecución mediante `cargo run`.

## Referencias
- [The Rust Programming Language - Syntax and Basics](https://doc.rust-lang.org/book/)
- [Rust std::fmt - Macros de formateo](https://doc.rust-lang.org/std/fmt/)
- [Crate xor_cryptor en crates.io](https://crates.io/crates/xor_cryptor)