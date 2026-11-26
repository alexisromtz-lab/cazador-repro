# cazador-repro

Nodo Linux efímero para **reproducir hallazgos de seguridad sobre software de código abierto**, en su
commit exacto, sin depender de servidores de nadie y sin publicar el hallazgo antes de tiempo.

## Qué hace

1. Aprovisiona un Ubuntu 24.04 con `cmake`, `g++`, `make`, `python3`.
2. Materializa desde un secreto (`CAPSULA_TGZ_B64`) los guiones de la cápsula: la receta de
   construcción del sujeto y el reproductor.
3. Ejecuta el reproductor y publica en el registro **tres números**: `rc_repro`, `asserts_pass`,
   `asserts_fail`, más las líneas de estado del sujeto que el reproductor marca con prefijo
   (`apta_build`, `binario`, `blanco`, `pid`, `log`, `no_medido`). Son hechos sobre el
   comportamiento del sujeto, no sobre el hallazgo.
4. Con `modo=asan` construye **el mismo sujeto** con AddressSanitizer y publica la clase del
   defecto (`asan_clase`, `asan_operacion`, `asan_bytes`) — no su localización: el informe completo
   va como `recibo_cifrado`, cifrado con una clave que no está en este repositorio. Sin la clave esa
   línea es opaca.

## Qué no hace, y por qué está escrito aquí

- **No publica el hallazgo.** El cuerpo del estímulo, el locus del fallo y los asertos viven en un
  secreto, no en el árbol. Un hallazgo sin divulgar no se escribe en un repositorio público.
- **No usa el nodo para otra cosa.** El repositorio contiene exactamente esto: un aprovisionamiento
  y un caso que mapea cápsula → reproductor. No hay trabajo ajeno a la investigación que declara.
- **No firma nada en nombre de nadie.** No comenta en repositorios de terceros ni abre issues.

## Cómo se usa

```bash
gh workflow run repro.yml -f capsula=dynolog-ipc-dos -f modo=normal
gh workflow run repro.yml -f capsula=dynolog-ipc-dos -f modo=asan
gh run watch
```

`rc_repro=0` significa que el invariante se rompió en un nodo limpio; `rc_repro=3` significa que el
nodo no pudo medir (sujeto no compila, sin privilegio) y entonces **no se escribe ninguna cápsula**.

## Por qué la salida es un número y no un informe

Porque el informe es el producto y este repositorio es público. El informe se redacta donde se
cobra: en la divulgación privada al mantenedor o en la auditoría contratada.
