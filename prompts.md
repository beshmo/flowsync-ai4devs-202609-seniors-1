# Prompts

---

## Prompt 1

**Modelo:** Sonnet 5.5 1M High
**Herramienta:** Claude Code

```
Describe el comportamiento actual de las capas relacionadas con el acceso y el registro en el fichero docs/spec-viva/eps.md, usa el formato:

## Purpose de una o dos frases: para qué existe esta capability. 
## Requirements
  ### Requirement: en los que el sistema SHALL hacer algo. 
    #### Scenario: bajo cada requisito, al menos uno de cuatro almohadillas, con dos viñetas: WHEN y THEN. No hay casilla para el GIVEN: la precondición se mete dentro del WHEN.

Y ten en cuenta las siguientes instrucciones:

- NO leas el fichero README.md para no interferir con el ejercicio que estoy realizando.
- Texto en castellano, salvo las mayúsculas de la RFC.
- Solo quiero comportamiento observable desde fuera. Ni un nombre de clase, ni un nombre de archivo, ni una ruta de código. En la API, observable es la petición y la respuesta. En la pantalla, observable es lo que una persona ve y puede hacer.
- NO uses ADDED, MODIFIED ni REMOVED. Eso es el vocabulario de un delta, y esto no es un delta: es la verdad actual del sistema.
- NO toques el código. Ni siquiera para arreglar lo que encuentres

En caso de dudas pregúntame.
```

**Qué salió:** funcionó a la primera
