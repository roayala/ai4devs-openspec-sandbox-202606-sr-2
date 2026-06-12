# ENTREGA — Ejercicio OpenSpec (Spec-Driven Development)

## 1. Evidencia de instalación

Output de `openspec --version`:

```
1.4.1
```

Output de `ls -R openspec/`:

```
openspec/:
changes
config.yaml
specs

openspec/changes:
archive

openspec/changes/archive:

openspec/specs:
```

> Nota: OpenSpec 1.4.x ya no genera `openspec/project.md`. Su rol (la "constitución" del proyecto) lo cumple ahora el campo `context:` de `openspec/config.yaml`, que en este repo ya está rellenado con stack y convenciones.

---

## 2. Plantilla de los 3 Pilares

**Micro-tarea:** Parser sencillo de CSV

**Pilar 1 — Herramienta:** ¿Cuál eliges? ¿Por qué esta y no otra?

Claude Code + OpenSpec CLI 1.4.1. Claude Code es el copiloto ya integrado en el sandbox (el `openspec init` generó sus slash commands en `.claude/commands/opsx/` y skills en `.claude/skills/`), y OpenSpec impone el workflow spec-driven (propose → apply → archive) que es justamente el objeto del ejercicio. Cursor o Copilot servirían para escribir el código, pero no traen el flujo OPSX integrado de serie en este repo.

**Pilar 2 — Contexto:** ¿Qué información estás aportando? ¿Hay algo del contexto que has decidido omitir conscientemente?

Aportado (vía `context:` en `openspec/config.yaml` y en la propuesta):

- Lenguaje: JavaScript puro sobre Node.js v26, sin dependencias externas.
- Forma: módulo único `csv-parser.js` que exporta una función `parseCSV(text)`.
- Alcance: separador coma, campos entrecomillados que contienen comas, comillas escapadas como `""`, primera fila como cabecera, salida como array de objetos.

Omitido conscientemente: streaming de ficheros grandes, separadores configurables, detección de encoding y manejo de BOM. Son mejoras legítimas pero inflarían una micro-tarea; mejor dejarlas explícitas como "fuera de alcance" para que la IA no las improvise.

**Pilar 3 — Prompt:** ¿Cómo lo estructuras? Pega aquí el prompt final que vas a lanzar.

Estructura: tarea + restricciones + contrato de la función + casos borde + criterio de verificación. Sin rol artificial (el contexto del repo ya lo da) y pensado para lanzarse con `/opsx:propose`.

Prompt final:

```
/opsx:propose Crea un parser sencillo de CSV en JavaScript puro (Node v26, sin
dependencias). Un único módulo `csv-parser.js` que exporte `parseCSV(text)`:
recibe un string CSV cuya primera fila es la cabecera y devuelve un array de
objetos (clave = cabecera, valor = string de la celda). Debe soportar campos
entrecomillados que contienen comas y comillas escapadas como "". Fuera de
alcance: streaming, separadores configurables, encoding/BOM. Incluye tests con
node:test cubriendo: fila simple, campo con coma entrecomillado, comilla
escapada, línea vacía final.
```

**Resultado:** ¿Funcionó a la primera o tuviste que iterar? Una mejora que harías si volvieras a hacerlo.

_Pendiente — la implementación del parser se hará en la siguiente sesión siguiendo el flujo propose → apply → archive. Se completará esta sección con el resultado real._

---

## 3. Observaciones de la exploración

1. **`project.md` ya no existe en OpenSpec 1.4.x.** El init genera `openspec/config.yaml` y ahí vive el contexto del proyecto (campo `context:`) y las reglas por artefacto (campo `rules:`). La doc/README que menciona `project.md` corresponde a versiones anteriores.
2. **El workflow OPSX se expone por duplicado para el copiloto:** como slash commands (`.claude/commands/opsx/propose|apply|archive|explore|sync`) y como skills (`.claude/skills/openspec-*`). El flujo completo es propose → apply → archive, con `explore` como modo de pensar antes de proponer y `sync` para volcar delta-specs a las specs principales sin archivar.
3. **La estructura separa el cambio de la verdad:** `openspec/changes/` guarda propuestas activas, `openspec/changes/archive/` las completadas, y `openspec/specs/` las specs vivas del sistema. Una propuesta no toca las specs principales hasta que se sincroniza o archiva — las specs son el estado consolidado, los changes son el delta.
