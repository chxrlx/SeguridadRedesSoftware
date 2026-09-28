## Descripción
The Rust saga continues... again! Sometimes you just have to do things outside the rules.
Recursos: `fixme3.tar.gz`

## Solución
- Nos ubicamos en la carpeta y extraemos el contenido del archivo comprimido `fixme3.tar.gz`:
  ```bash
  tar -zxvf fixme3.tar.gz
  cd fixme3
  ```
- Al inspeccionar `fixme3/src/main.rs`, encontramos una llamada a `std::slice::from_raw_parts`:
  ```rust
  let decrypted_slice = std::slice::from_raw_parts(decrypted_ptr, decrypted_len);
  ```
- Al intentar compilar con `cargo run`, el compilador genera el siguiente error:
  ```text
  error[E0133]: call to unsafe function `from_raw_parts` is unsafe and requires unsafe function or block
  ```
- **Causa del error:** `std::slice::from_raw_parts` construye un slice a partir de un puntero en bruto (*raw pointer*) y una longitud. Dado que el compilador no puede verificar en tiempo de compilación si el puntero es válido, está alineado o apunta a memoria inicializada válida, esta operación se considera insegura (*unsafe*).
- **Corrección:** Descomentar el bloque `unsafe { ... }` que rodea la operación de desreferenciación:
  ```rust
  // Original:
  // unsafe {
      ...
  // }

  // Corregido:
  unsafe {
      // Decrypt the flag operations 
      let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);

      // Creating a pointer 
      let decrypted_ptr = decrypted_buffer.as_ptr();
      let decrypted_len = decrypted_buffer.len();
      
      // Unsafe operation: calling an unsafe function that dereferences a raw pointer
      let decrypted_slice = std::slice::from_raw_parts(decrypted_ptr, decrypted_len);

      borrowed_string.push_str(&String::from_utf8_lossy(decrypted_slice));
  }
  ```
- Código corregido completo de `src/main.rs`:
  ```rust
  use xor_cryptor::XORCryptor;

  fn decrypt(encrypted_buffer: Vec<u8>, borrowed_string: &mut String) {
      // Key for decryption
      let key = String::from("CSUCKS");

      // Editing our borrowed value
      borrowed_string.push_str("PARTY FOUL! Here is your flag: ");

      // Create decryption object
      let res = XORCryptor::new(&key);
      if res.is_err() {
          return;
      }
      let xrc = res.unwrap();

      unsafe {
          // Decrypt the flag operations 
          let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);

          // Creating a pointer 
          let decrypted_ptr = decrypted_buffer.as_ptr();
          let decrypted_len = decrypted_buffer.len();
          
          // Unsafe operation: calling an unsafe function that dereferences a raw pointer
          let decrypted_slice = std::slice::from_raw_parts(decrypted_ptr, decrypted_len);

          borrowed_string.push_str(&String::from_utf8_lossy(decrypted_slice));
      }
      println!("{}", borrowed_string);
  }

  fn main() {
      // Encrypted flag values
      let hex_values = ["71", "35", "11", "73", "2f", "17", "53", "71", "12", "90", "7e", "53", "63", "e1", "01", "35", "7e", "59", "60", "f6", "03", "86", "7f", "56", "41", "29", "30", "6f", "08", "c3", "61", "f9", "35"];

      // Convert the hexadecimal strings to bytes and collect them into a vector
      let encrypted_buffer: Vec<u8> = hex_values.iter()
          .map(|&hex| u8::from_str_radix(hex, 16).unwrap())
          .collect();

      let mut party_foul = String::from("Using memory unsafe languages is a: ");
      decrypt(encrypted_buffer, &mut party_foul);
  }
  ```
- Compilamos y ejecutamos con `cargo run`:
  ```bash
  cargo run
  ```
- Salida del programa:
  ```text
  Using memory unsafe languages is a: PARTY FOUL! Here is your flag: academy{n0w_y0uv3_f1x3d_1h3m_411}
  ```

```text
academy{n0w_y0uv3_f1x3d_1h3m_411}
```

## Notas Adicionales
- **Superpoderes de Unsafe Rust**: La palabra clave `unsafe` permite ejecutar operaciones que el verificador de memoria estático no puede validar:
  1. Desreferenciar punteros crudos (*raw pointers*: `*const T`, `*mut T`).
  2. Invocar funciones o métodos no seguros (*unsafe*).
  3. Implementar traits no seguros.
  4. Modificar variables estáticas mutables.
  5. Acceder a campos de `union`.
- **Garantías de seguridad**: `unsafe` no suspende el análisis de tipos ni las comprobaciones habituales del compilador; simplemente concede acceso a primitivas de bajo nivel donde la responsabilidad de evitar comportamientos indefinidos (*undefined behavior*) recae en el desarrollador.

## Referencias
- [The Rust Programming Language - Unsafe Rust](https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html)
- [Rust std::slice::from_raw_parts](https://doc.rust-lang.org/std/slice/fn.from_raw_parts.html)
- [The Rustonomicon - The Dark Arts of Unsafe Rust](https://doc.rust-lang.org/nomicon/)