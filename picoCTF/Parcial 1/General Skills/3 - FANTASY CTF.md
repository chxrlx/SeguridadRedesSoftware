## Descripción
Experience the interactive simulation to learn about the rules and ethics of the competition!
Conexión: netcat

## Solución
- Nos conectamos al reto mediante Netcat
- El servidor inicia una simulación narrativa interactiva en terminal ("FANTASY CTF SIMULATION") protagonizada por una estudiante llamada Eibhilin y su asistente de inteligencia artificial, Nyx
- Avanzamos en los diálogos presionando `Enter` cuando el texto muestre `(Press Enter to continue...)`
- Durante la historia se nos presentan preguntas de opción múltiple enfocadas en las reglas y la ética de la competencia:
  1. **Pregunta 1 (Registro de cuenta)**:
     ```text
     Options:
     A) *Register multiple accounts*
     B) *Share an account with a friend*
     C) *Register a single, private account*
     [a/b/c] > c
     ```
     - Seleccionamos `c` para registrar una única cuenta privada, respetando la regla contra cuentas múltiples o compartidas
  2. **Pregunta 2 (Resolución del reto)**:
     ```text
     Options:
     A) *Play the game*
     B) *Search the Ether for the flag*
     [a/b] > a
     ```
     - Seleccionamos `a` para jugar y resolver el reto legítimamente, sin buscar banderas filtradas
- Al completar la simulación, Eibhilin y Nyx revelan la bandera obtenida al terminar el juego

```text
academy{m1113n1um_3d1710n_07462474}
```

## Notas Adicionales
- **Reglas y Ética de CTF**: Este reto introductorio busca instruir a los competidores sobre las normas de conducta fundamentales:
  1. Registrar solamente una cuenta por participante.
  2. No compartir cuentas, banderas ni archivos de retos con competidores ajenos a tu equipo.
  3. No publicar soluciones (*writeups*) en internet hasta que la competencia haya concluido formalmente y los organizadores hayan publicado los resultados.
- **Herramienta Netcat**: Permite conectarse bidireccionalmente a través de sockets TCP a servicios interactivos basados en texto desde la terminal.

## Referencias
- [Reglas Oficiales de picoCTF](https://picoctf.org/rules.html)
- [Netcat Cheat Sheet](https://sansorg.egnyte.com/dl/bUvLzFkH5V)