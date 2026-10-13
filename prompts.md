# Prompts

Aquí van **todos los prompts que lanzaste** para hacer el ejercicio, en el orden en que los
lanzaste, con el modelo y la herramienta de cada uno.

Esto no es papeleo. Lo que se revisa es **cómo pediste las cosas**, no solo lo que salió: un
resultado flojo con un prompt bueno y un resultado flojo con un prompt vago necesitan feedback
distinto, y sin este archivo no se distinguen.

## Cómo rellenarlo

- Un apartado `## Prompt N` por cada prompt.
- **Pega el prompt tal cual lo lanzaste**, dentro del bloque de código, aunque ocupe diez líneas
  y aunque tenga faltas. No lo reescribas para que quede bien: el que arreglaste mentalmente
  después no es el que lanzaste.
- Incluye también los que **no funcionaron**. Suelen ser los más útiles de leer.
- `Modelo` y `Herramienta` en todos. Si cambiaste de una a otra a mitad, se nota aquí.

Borra el ejemplo de abajo cuando escribas el primero.

---

## Prompt 1

**Modelo:** Sonet 5.5 1M High
**Herramienta:** Claude Code

```
lee el archivo CLAUDE.md y enumera las reglas de proceso, junto con una breve descripción de su cometido, que indican qué hacer antes de modificar código, cuándo crear commits, y otros procedimientos que encuentres
```

**Qué salió:** enumeró 5 reglas de proceso descritas en AGENTS.md


## Prompt 2

**Modelo:** Sonet 5.5 1M High
**Herramienta:** Claude Code

```
crea una tabla vacía de 5 columnas en el archivo docs/auditoria/eps.md, con las siguientes cabeceras:
  1. qué pide la regla, 2. qué se revisó para comprobarla, 3. veredicto, 4. evidencia, 5. qué harías con ella
Después, inicializa cada fila de la tabla con las 5 reglas de proceso localizadas en la primera columna (mantén el número de regla que has usado en esta conversación)
```

**Qué salió:** escribió la tabla solicitada en el archivo indicado con una fila por regla


## Prompt 3

**Modelo:** Sonet 5.5 1M High
**Herramienta:** Claude Code

```
 usa esa tabla como plantilla para crear las siguientes, revisa los últimos 15 commits para ver si cada una de esas reglas se aplicaron y rellena las columnas 2, 3 y 4, para la columna con el verdicto indica si se cumplió o no la regla o no hay información suficiente para determinarlo, en caso de dudas indícalo también. Para la columna con la evidencia escríbela en una línea. Si la regla no se cumple, indica dónde se incumple. Si no se puede comprobar, explica qué información falta. La última columna no la rellenes. Escribe todas las demás tablas en el mismo fichero eps.md. No corrijas ningún error detectado en el código.
```

**Qué salió:** escribió una tabla por cada uno de los 15 commits para facilitar la tarea de auditar las reglas


## Prompt 4

**Modelo:** Sonet 5.5 1M High
**Herramienta:** Claude Code

```
rellena la primera tabla que se usó como plantilla para resumir el resultado del análisis realizado en las otras 15, excepto la última columna que la rellenaremos después
```

**Qué salió:** resumió su análisis de los 15 commits en la tabla indicada


## Prompt 4

**Modelo:** Sonet 5.5 1M High
**Herramienta:** Claude Code

```
sugiere qué harías con cada una de esas reglas según los datos recopilados, dejarla, borrarla, o convertirla en una comprobación automática
```

**Qué salió:** realizó la propuesta solicitada para ayudarme con el análisis
