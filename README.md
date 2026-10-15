## Nombres
Andres Barrios
Santiago Panadero
Samuel Rodriguez


# Parcial de Bash en Debian 13: respuestas justificadas

## Tabla resumen

| # | Resp. | # | Resp. | # | Resp. | # | Resp. | # | Resp. |
|---|-------|---|-------|---|-------|---|-------|---|-------|
| 1 | B | 6 | D | 11 | A | 16 | D | 21 | D |
| 2 | B | 7 | A | 12 | B | 17 | C | 22 | A |
| 3 | A | 8 | C | 13 | C | 18 | A | 23 | D |
| 4 | C | 9 | B | 14 | A | 19 | B | 24 | A |
| 5 | D | 10 | D | 15 | B | 20 | C | 25 | C |

---

## Justificaciones

### Pregunta 1: **B**
La **sustitución de comandos** `$(date +%Y%m%d_%H%M%S)` ejecuta `date` y reemplaza la expresión por su salida. Así se arma el nombre, por ejemplo `respaldo_$(date +%F_%H%M).tar.gz`. La expansión de llaves (A) solo genera listas de cadenas, no consulta el reloj. La redirección de entrada (C) no obtiene la hora. `PWD` (D) guarda la ruta del directorio actual, no una fecha.

### Pregunta 2: **B**
`>>` **anexa** al final del archivo y conserva lo anterior. `>` (A) lo sobrescribe. `<` (C) es redirección de entrada. `|` (D) conecta la salida de un comando con la entrada de otro, no escribe en archivos.

### Pregunta 3: **A**
Con un valor numérico entero se usa `if [ "$uso" -gt 85 ]; then ...`. `-gt` es "mayor que" numérico. `=` (B) compara cadenas. `case` (C) compara patrones, no es la herramienta natural para un umbral. `for` con `-n` (D) no tiene sentido, porque `-n` prueba si una cadena no está vacía.

### Pregunta 4: **C**
`*/30 * * * *` significa "cada 30 minutos" en el campo de minutos, con el resto en comodín. `30 * * * *` (A) corre solo en el minuto 30 de cada hora. `0 */30 * * *` (B) corre a minuto 0 en horas múltiplos de 30, o sea solo a la hora 0. `* */30 * * *` (D) correría cada minuto durante la hora 0.

### Pregunta 5: **D**
El patrón estándar es:
```bash
while IFS=',' read -r usuario nombre grupo; do
  ...
done < archivo.csv
```
`IFS=','` parte cada línea por comas y `read` asigna los campos a variables. Un `for` sobre `cat` (A) divide por espacios y saltos de línea, no fila por fila. `test -f` (B) solo verifica que exista un archivo. `shift` (C) desplaza parámetros posicionales.

### Pregunta 6: **D**
`useradd` necesita **privilegios de root** (info I), y además falla si el usuario **ya existe** (info II). Para asegurar que se creen todas las cuentas hacen falta las dos condiciones. Con una sola, el otro motivo de fallo sigue abierto.

### Pregunta 7: **A**
- Afirmación 1: verdadera. `-mtime +7` selecciona archivos cuya última **modificación** fue hace más de 7 días.
- Afirmación 2: falsa. `-mtime` evalúa la fecha de **modificación**. La de último acceso es `-atime`.

### Pregunta 8: **C**
Ambas son verdaderas y la razón explica la afirmación. Si la variable está vacía, `rm -rf "$DIR"/*` se convierte en `rm -rf /*` y borra todo el sistema. Por eso se valida antes (`[ -n "$DIR" ]`, o `${DIR:?}`).

### Pregunta 9: **B**
`systemctl is-active --quiet servicio` no imprime nada y devuelve **código 0 si el servicio está activo**, ideal para un `if`. `is-enabled` (A) solo indica si inicia en el arranque. `daemon-reload` (C) recarga las definiciones de unidades. `list-units` (D) lista unidades y no informa de consumo de memoria.

### Pregunta 10: **D** (I, II, IV, III)
Primero se consulta el estado (I). Si está detenido, se reinicia (II). Luego se verifica que el reinicio funcionó (IV). Por último se registra el **resultado final** en la bitácora (III), que debe ir después de la verificación para reflejar el estado real.

### Pregunta 11: **A**
`grep -i error archivo | wc -l`: `-i` ignora mayúsculas y `wc -l` cuenta **líneas**. `wc -w` (B) cuenta palabras. `grep -vc` (C) cuenta las líneas que **no** contienen la palabra. `grep -il ... | sort` (D) lista nombres de archivos, no cuenta.

### Pregunta 12: **B**
`awk '{print $5}'`. `$5` es el quinto campo. `NR` (A) es el número de registro, `$0` (C) es la línea completa y `NF` (D) es la cantidad de campos.

### Pregunta 13: **C**
`sudo apt-get install -y paquete`. `-y` responde "sí" automáticamente a las confirmaciones. `-d` (A) solo descarga, `-q` (B) reduce la salida y `-s` (D) simula sin instalar. `nohup` no da privilegios.

### Pregunta 14: **A**
`apt-get install -y pkg && echo "OK" >> log || echo "FALLO" >> log`. `&&` ejecuta lo siguiente solo si el comando anterior tuvo éxito (código 0). `||` lo ejecuta solo si falló. B está invertida. `&` y `|` (C) son segundo plano y tubería. `>>` y `<<` (D) son redirecciones, no operadores lógicos.

### Pregunta 15: **B**
`for h in "${hosts[@]}"; do ping -c1 "$h"; done`. Con comillas, `"${arr[@]}"` entrega cada elemento como palabra independiente. `${#hosts[@]}` (A) es la cantidad de elementos. `"${hosts[*]}"` (C) junta todo en una sola cadena. `${!hosts}` (D) es indirección o índices, no el recorrido de elementos.

### Pregunta 16: **D**
Un host apagado hace que `ping` devuelva un valor distinto de cero. El `if` lo evalúa como falso, se ejecuta la rama **else** y queda registrado como **inaccesible**.

### Pregunta 17: **C**
En `case`, los patrones alternativos se separan con `|`:
```bash
*.jpg|*.png|*.gif) mv "$f" imagenes/ ;;
```
La coma, el punto y coma o el `&` (A, B, D) no funcionan como alternancia de patrones.

### Pregunta 18: **A**
`trap limpiar EXIT` ejecuta la función al terminar el script, ya sea por finalización normal, error o interrupción como Ctrl+C. `SIGKILL` y `SIGSTOP` (C, D) **no se pueden atrapar**. `alias` (B) y `export` (D) no registran manejadores de señales.

### Pregunta 19: **B**
`ls -t` ordena por fecha, de más reciente a más antigua. `tail -n +$((N+1))` salta las N primeras (las más nuevas) y deja las restantes, que se borran. `-S` (A) ordena por tamaño y `-a` (D) solo muestra ocultos. En C, `head` conservaría las más nuevas y no sirve para seleccionar cuáles borrar.

### Pregunta 20: **C**
`awk -F: '$3 == 0 {print $1}' /etc/passwd`. El separador es `:` y el UID es el **campo 3**. El campo 4 (B) es el GID y el 1 (D) es el nombre de usuario.

### Pregunta 21: **D**
`ss -tuln`: `-t` TCP, `-u` UDP, `-l` solo en escucha, `-n` puertos numéricos. No incluye `-p`, así que no muestra procesos (B). No incluye `-x`, así que no muestra sockets UNIX (C). `-l` muestra los que escuchan, no las conexiones establecidas (A).

### Pregunta 22: **A**
`awk 'BEGIN{OFS=","} {$1=$1; print}'`. `OFS` es el separador de **salida** de campos. Asignar `$1=$1` fuerza a awk a reconstruir la línea con ese separador. `FS` (B) es el separador de entrada, `ORS` (C) el de registros de salida y `RS` (D) el de registros de entrada.

### Pregunta 23: **D**
`systemctl enable --now apache2` habilita el servicio para el arranque **y** lo inicia de una vez. `start` (A) solo inicia, `enable` (B) solo habilita y `reload` (C) recarga la configuración.

### Pregunta 24: **A**
`$#` es la cantidad de argumentos:
```bash
if [ "$#" -ne 2 ]; then echo "Uso: $0 origen destino"; exit 1; fi
```
`$?` (B) es el código de salida del último comando, `$$` (C) es el PID del script y `$0` (D) es el nombre del script.

### Pregunta 25: **C**
`PS3` es el prompt que usa `select`. `PS1` (A) es el prompt principal del shell, `PS2` (B) el de continuación de línea y `PS4` (D) el de trazado con `set -x`.
