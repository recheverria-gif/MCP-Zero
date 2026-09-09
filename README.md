## Qué es mcp-zero — principio de diseño 
(La pauta de implementación se encuentra dentro de MCP-Zero.7z)

- Un servidor MCP local que conecta Claude Code con el iSeries por  SSH/SFTP.
- **Colaborativo, no dueño de la lógica:** el MCP valida y orquesta; el iSeries ejecuta con **sus propios comandos nativos y  tablas**.
- El MCP nunca reimplementa una lógica de negocio que el iSeries ya  resuelve mejor — la delega.

## Los programas iSeries que sostienen el MCP

**Biblioteca compartida `QGPL`:**
- `CLAUDE_GET`, 
- `CLAUDE_MER`
- `CLAUDEINV1` + `CLAUDEINV2`
- Tabla `CLAUDEWLST` (Lista de bibliotecas permitidas para buscar fuentes sincronizada con Aldon)
- Tabla `CLAUDESKIP` (comandos nativos que no participan dentro de una investigacion - buscaserie)

** Lo que vive en la Biblioteca personal del desarrollador:**
- `QCLAUDE` (landing zone de PUT, mantenida por el desarrollador — auditable)
- `QGETSOL`/`QGETRES`,  (peticion de fuentes al iseries y resultado)
- `QMRGSOL`/`QMRGRES`,  (peticion de PUT+Merge al iseries y resultado)
- `QINVRES` (petición investigar por concepto - Buscaserie AS/400)

**Comandos nativos IBM invocados, nunca reimplementados:**
- `MRGSRC`,  (Merge del fuente modificado directo a la librería del desarrollador)
- `FNDSTRPDM`,  (Búsqueda de string)

## Las 5 tools del MCP

| Tool | Qué hace |

| `iseries_solicitar_fuentes` | Resuelve archivo/biblioteca/tipo de un miembro solo con su nombre |
| `iseries_descargar_fuentes` | Trae el fuente real (GET), decodifica EBCDIC del lado cliente |
| `iseries_subir_fuentes` | Sube a `QCLAUDE` (landing zone), nunca al destino real (PUT) |
| `iseries_merge_fuentes` | Reubica a destino vía `MRGSRC`, preserva `SRCDAT` hacia la libreria del desarrollador|
| `iseries_investigar_fuentes` | Busca una cadena en el contenido de fuentes, sin descargar nada |


## Skills de Claude Code — 3 hablan directo con el MCP

- **`solicitar-fuente-iseries`** — trae un fuente (GET)
- **`publicar-fuente-iseries`** — sube y reubica un fuente (PUT + MERGE)
- **`investigar-iseries`** — busca una cadena y filtra por funcionalidad
