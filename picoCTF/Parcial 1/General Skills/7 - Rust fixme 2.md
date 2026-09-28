## Descripción
The Rust saga continues? I ask you, can I borrow that, pleeeeeaaaasseeeee?
Recursos: `fixme2.tar.gz`

## Solución
- Nos ubicamos en la carpeta y extraemos el contenido del archivo comprimido `fixme2.tar.gz`:
  ```bash
  tar -zxvf fixme2.tar.gz
  cd fixme2
  ```
- Al inspeccionar `fixme2/src/main.rs` e intentar compilarlo con `cargo run`, el compilador de Rust arroja el error `cannot borrow *borrowed_string as mutable, as it is behind a & reference`:
  - En Rust, las referencias son inmutables por defecto. Para que una función modifique el valor referenciado mediante métodos como `.push_str()`, debe recibir una referencia mutable (`&mut String`).
  - Asimismo, la variable original en la función `main` debe ser declarada explícitamente como mutable (`mut`) y pasarse como referencia mutable (`&mut party_foul`).
- Modificaciones realizadas:
  1. **Firma de la función `decrypt`:**
     ```rust
     // Original:
     fn decrypt(encrypted_buffer:Vec<u8>, borrowed_string: &String){
     // Corregido:
     fn decrypt(encrypted_buffer: Vec<u8>, borrowed_string: &mut String){
     ```
  2. **Declaración de variable mutable en `main`:**
     ```rust
     // Original:
     let party_foul = String::from("Using memory unsafe languages is a: ");
     // Corregido:
     let mut party_foul = String::from("Using memory unsafe languages is a: ");
     ```
  3. **Paso de referencia mutable en la invocación:**
     ```rust
     // Original:
     decrypt(encrypted_buffer, &party_foul);
     // Corregido:
     decrypt(encrypted_buffer, &mut party_foul);
     ```
- Código corregido completo de `src/main.rs`:
  ```rust
  use xor_cryptor::XORCryptor;

  fn decrypt(encrypted_buffer: Vec<u8>, borrowed_string: &mut String) {
      // Key for decryption
      let key = String::from("CSUCKS");

      // Editing our borrowed value
      borrowed_string.push_str("PARTY FOUL! Here is your flag: ");

      // Create decrpytion object
      let res = XORCryptor::new(&key);
      if res.is_err() {
          return;
      }
      let xrc = res.unwrap();

      // Decrypt flag and print it out
      let decrypted_buffer = xrc.decrypt_vec(encrypted_buffer);
      borrowed_string.push_str(&String::from_utf8_lossy(&decrypted_buffer));
      println!("{}", borrowed_string);
  }

  fn main() {
      // Encrypted flag values
      let hex_values = ["71", "35", "11", "73", "2f", "17", "53", "71", "01", "1c", "7e", "59", "63", "e1", "61", "25", "0d", "c4", "60", "f2", "12", "a0", "18", "03", "51", "03", "36", "05", "0e", "f9", "42", "5b"];

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
  Using memory unsafe languages is a: PARTY FOUL! Here is your flag: academy{4r3_y0u_h4v1n5_fun_y31?}
  ```

```text
academy{4r3_y0u_h4v1n5_fun_y31?}
```

## Notas Adicionales
- **Modelo de Préstamos (*Borrowing*) en Rust**: Permite pasar referencias a datos sin transferir la propiedad (*ownership*). Por defecto, los préstamos son inmutables (`&T`), impidiendo que el receptor modifique el contenido original.
- **Referencias Mutables (`&mut T`)**: Habilitan la modificación del valor prestado en memoria. Rust impone la regla de que solo puede existir una referencia mutable a un recurso en un alcance determinado (*aliasing XOR mutability*), evitando condiciones de carrera de datos en tiempo de compilación.

## Referencias
- [The Rust Programming Language - References and Borrowing](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)
- [Rust Reference - Variables and Mutability](https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html)