## Descripción
Morse code is well known. Can you decrypt this? Download the file here.

Wrap your answer with academy{}, put underscores in place of pauses, and use all uppercase.

## Solución
- El archivo `morse_chal.wav` es una grabación de audio monoaural (PCM a 44100 Hz) que transmite una señal en código Morse.
- Podemos demodular la señal analizando la envolvente de potencia (amplitud RMS en ventanas cortas de 10 ms) o mediante software como Audacity, herramientas CLI como `morse2ascii`, o un script en Python con el módulo estándar `wave`.
- Al medir las duraciones de los pulsos activos e inactivos:
  - Pulso corto (*punto* `.`): ~100 ms (10 unidades de 10 ms).
  - Pulso largo (*raya* `-`): ~300 ms (30 unidades de 10 ms).
  - Pausa entre símbolos: ~100 ms.
  - Pausa entre letras: ~300 a 400 ms.
  - Pausa entre palabras: ~700 a 800 ms.
- Se implementó el siguiente script en Python para procesar el audio y decodificar el mensaje:
  ```python
  import wave, struct

  MORSE_DICT = {
      '.-': 'A', '-...': 'B', '-.-.': 'C', '-..': 'D', '.': 'E',
      '..-.': 'F', '--.': 'G', '....': 'H', '..': 'I', '.---': 'J',
      '-.-': 'K', '.-..': 'L', '--': 'M', '-.': 'N', '---': 'O',
      '.--.': 'P', '--.-': 'Q', '.-.': 'R', '...': 'S', '-': 'T',
      '..-': 'U', '...-': 'V', '.--': 'W', '-..-': 'X', '-.--': 'Y',
      '--..': 'Z', '-----': '0', '.----': '1', '..---': '2', '...--': '3',
      '....-': '4', '.....': '5', '-....': '6', '--...': '7', '---..': '8',
      '----.': '9'
  }

  with wave.open('morse_chal.wav', 'rb') as w:
      num_frames = w.getnframes()
      framerate = w.getframerate()
      frames = w.readframes(num_frames)

  samples = struct.unpack(f'<{num_frames}h', frames)
  chunk_size = framerate // 100
  powers = [((sum(samples[i:i+chunk_size][j]**2 for j in range(len(samples[i:i+chunk_size]))) / chunk_size)**0.5) for i in range(0, len(samples), chunk_size)]
  threshold = max(powers) * 0.3
  is_on = [1 if p > threshold else 0 for p in powers]

  runs = []
  cur_val, cur_len = is_on[0], 0
  for val in is_on:
      if val == cur_val:
          cur_len += 1
      else:
          runs.append((cur_val, cur_len))
          cur_val, cur_len = val, 1
  runs.append((cur_val, cur_len))

  morse_words = []
  cur_word, cur_letter = [], []

  for val, l in runs:
      if val == 1:
          cur_letter.append('.' if l < 20 else '-')
      else:
          if l >= 55:
              if cur_letter: cur_word.append(''.join(cur_letter)); cur_letter = []
              if cur_word: morse_words.append(cur_word); cur_word = []
          elif l >= 20:
              if cur_letter: cur_word.append(''.join(cur_letter)); cur_letter = []

  if cur_letter: cur_word.append(''.join(cur_letter))
  if cur_word: morse_words.append(cur_word)

  decoded_words = [''.join([MORSE_DICT.get(c, '?') for c in w]) for w in morse_words]
  flag = f"academy{'{' + '_'.join(decoded_words) + '}'}"
  print(flag)
  ```
- Secuencias de Morse identificadas:
  - Word 1: `.--` (W) `....` (H) `....-` (4) `--...` (7) $\rightarrow$ `WH47`
  - Word 2: `....` (H) `....-` (4) `--...` (7) `....` (H) $\rightarrow$ `H47H`
  - Word 3: `----.` (9) `-----` (0) `-..` (D) $\rightarrow$ `90D`
  - Word 4: `.--` (W) `..---` (2) `-----` (0) `..-` (U) `----.` (9) `....` (H) `--...` (7) $\rightarrow$ `W20U9H7`
- Uniendo las palabras con guiones bajos y en mayúsculas como solicita la descripción:
```text
academy{WH47_H47H_90D_W20U9H7}
```

## Notas Adicionales
- La frase transmitida es una versión en l33tspeak de la famosa cita bíblica *"What hath God wrought"* (Números 23:23), el primer mensaje oficial transmitido por telégrafo Morse en 1844 por Samuel Morse.
- Herramientas utilizadas:
  - Python 3 (`wave`, `struct`): Demodulación y decodificación de la señal de audio sin dependencias externas.

## Referencias
- [International Morse Code Recommendation (ITU-R M.1677-1)](https://www.itu.int/rec/R-REC-M.1677-1-200910-I/en)
- [History of the Telegraph - "What hath God wrought"](https://www.loc.gov/item/today-in-history/may-24/)
- [Python wave module documentation](https://docs.python.org/3/library/wave.html)
