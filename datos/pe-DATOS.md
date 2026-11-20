# Perú (pe) — fuentes y método

Estado: **ok** (con advertencias, ver "Limitaciones"). Generado el 2026-11-19 (hora de Lima).

## Fuentes

| # | Fuente | Endpoint / URL exacta | Consulta | Descarga (UTC) | Registros |
|---|---|---|---|---|---|
| 1 | Portal de Contrataciones Abiertas (OCDS) del OECE (antes OSCE), datos SEACE v3. Licencia CC BY 4.0. Sin login. | `https://contratacionesabiertas.oece.gob.pe/api/v1/file/seace_v3/json/{año}/{mes}/` | 13 descargas masivas mensuales (JSON comprimido, "record packages" OCDS 1.1), de `2025/11` a `2026/11` | 2026-11-20 02:52 a 03:02 | 83 441 registros (compiledRelease) |
| 2 | Mismo portal, listado de archivos | `https://contratacionesabiertas.oece.gob.pe/api/v1/files?page=1&paginateBy=40&format=json` | Fecha de generación de cada archivo (todos regenerados el 2026-11-19 entre 12:58 y 14:55 -05) | 2026-11-20 02:52 | 326 archivos listados |
| 3 | Mismo portal, registro por id (solo verificación) | `https://contratacionesabiertas.oece.gob.pe/api/v1/record/{ocid}?format=json` | 3 ocid al azar | 2026-11-20 03:05 | 3 |
| 4 | BCRP, tipo de cambio SBS venta | `https://estadisticas.bcrp.gob.pe/estadisticas/series/api/PD04640PD/json/2026-11-01/2026-11-19` | Serie PD04640PD | 2026-11-20 02:50 | **3.433** S/ por US$ (18.Nov.26; el 19.Nov figura "n.d.") |
| 5 | IGN (Instituto Geográfico Nacional) vía GeoIDEP, capa "Capital Departamental" | `https://www.idep.gob.pe/geoportal/rest/services/DATOS_GEOESPACIALES/CENTROS_POBLADOS/MapServer/0/query?where=1%3D1&outFields=*&outSR=4326&returnGeometry=true&f=json` | Todas las entidades | 2026-11-20 02:56 | 25 puntos (24 capitales + Callao) |

Registros por archivo mensual (fuente 1): 2025-11: 7 125 · 2025-12: 8 403 · 2026-01: 1 015 · 2026-02: 4 913 · 2026-03: 8 272 · 2026-04: 8 106 · 2026-05: 7 679 · 2026-06: 7 396 · 2026-07: 6 667 · 2026-08: 6 741 · 2026-09: 6 784 · 2026-10: 6 706 · 2026-11: 3 634 (hasta el 19). Enero es bajo en la propia fuente (arranque del año fiscal: de 6 a 144 convocatorias por día), no es un archivo truncado (zip íntegro, `content-length` verificado).

Probado y descartado: `contratacionesabiertas.osce.gob.pe` (ya no resuelve DNS; el dominio vigente es `oece.gob.pe`), `www.datosabiertos.gob.pe` (HTTP 418 a clientes automáticos; API CKAN 404), `bi.seace.gob.pe` (401).

Nota técnica: el WAF del portal responde **403 a `curl` y a `python-urllib`** en todo `/api/v1/*`, pero **200 al `fetch` nativo de Node** (User-Agent `node`, sin cabeceras falsas). Por eso los scripts son `.mjs`. El servidor entrega ~35–50 KB/s por conexión; los 13 archivos se bajan en paralelo (~10 min).

## Scripts (reproducibles)

1. `scripts/pe_download.mjs <dir>` — baja los 13 zips y escribe `download_log.json`.
2. `scripts/pe_capitales.mjs <capitales.json>` — coordenadas IGN de capitales departamentales.
3. `scripts/pe_build.mjs <dir_zips> <capitales.json> <pe.json> [--top20]` — limpia, agrega y escribe `pe.json`.
4. `scripts/pe_verify.mjs <pe.json> [semilla]` — verificación aleatoria contra la API por id.

## Reglas de limpieza

- **Unidad**: un procedimiento de selección de SEACE v3 (`compiledRelease` OCDS). Incluye bienes, servicios y obras.
- **Fecha de publicación** (`publicado`): `tender.tenderPeriod.startDate` (inicio de convocatoria, el mismo criterio con que el portal segmenta sus archivos); si falta, `tender.datePublished`. Filtro: 2025-11-19 a 2026-11-19. Fuera de periodo: 3 753.
- **Deduplicación**: (a) por `ocid` (0 repetidos); (b) por entidad + nomenclatura: SEACE reemite el mismo procedimiento con un id nuevo tras nulidades o retrotraídos; se conserva la versión más reciente (`compiledRelease.date`; empate: id mayor). Descartadas **7 003** versiones anteriores. Sin esto los montos se duplicarían.
- **Monto** (`tender.value`): en soles. Si la moneda no es PEN se usa la conversión de la propia fuente (`amount_PEN`). Valor 0 o ausente → `null` (monto reservado o no publicado): **5 404** procesos cuentan en `n` pero no suman en `monto`, ni entran en mediana/p25/p75.
- **Rubro**: código UNSPSC de cada ítem (`items[].additionalClassifications`, esquema UNSPSC; si falta, los 8 primeros dígitos del CUBSO) contra los prefijos del brief. Un proceso se asigna al rubro que domina el valor de sus ítems (`items[].totalValue`; si algún ítem no trae valor, por número de ítems). Los procesos donde domina "otro" no entran en ningún rubro. `eventos` usa solo 801416, 801419, 901116 y 9015.
- **Región / ciudad**: `parties[].address` del comprador: `department` → `region`, `locality` (distrito) → `ciudad`. Es la sede de la entidad, no el lugar de entrega.
- **Coordenadas**: capital departamental del IGN (fuente 5), marcada `"geo":"region"`. No hay fuente por distrito en estos datos.
- **Capital del país** (cuota de 12 por rubro): departamento LIMA y provincia LIMA.
- **Estado**: `statusDetails` más frecuente entre los ítems (CONVOCADO, ADJUDICADO, CONSENTIDO, CONTRATADO, DESIERTO, NULO, etc.).
- **Proveedor / monto adjudicado**: `awards[].suppliers` y `awards[].value`. `proveedor` = el de mayor monto adjudicado en el proceso; `monto_adjudicado` = suma de adjudicaciones en soles (las de otra moneda se convierten con el tipo implícito del propio proceso; si no se puede, no suman). Varios proveedores en una misma adjudicación se unen con " / ". Solo nombres, nunca RUC.
- **`duracion_dias`**: `contracts[0].period.durationInDays` (plazo del contrato); `null` si no hay contrato.
- **`url`**: ficha pública del portal, `https://contratacionesabiertas.oece.gob.pe/proceso/{ocid}` (comprobada en navegador).
- **Texto**: trim y colapso de espacios; `objeto` cortado a 170 caracteres. Los "¿" que aparecen en lugar de comillas vienen así en la fuente.

## Exclusiones

- **Outliers**: revisado el top 20 de montos; el mayor es S/ 783 millones (obra vial, Gobierno Regional de Piura) y todos son obras o servicios nacionales plausibles. Regla explícita en el script: monto > 2×10^10 soles se excluye de sumas. **Registros excluidos: 0.**
- 7 003 versiones anteriores de procesos reconvocados y 3 753 registros fuera de periodo (ver arriba).

## Resultado

- `total.n` = **72 685** procesos; `total.monto` = **S/ 87 079 219 112.61** (67 281 procesos con monto).
- Por rubro (n / monto S/ / abiertos estimados): alimentacion 1 987 / 637.6 M / 170 · aseo 310 / 1 262.6 M / 27 · dotacion 647 / 200.7 M / 26 · mantenimiento 8 372 / 7 892.6 M / 386 · papeleria 127 / 52.5 M / 3 · eventos 184 / 72.9 M / 2 · software 1 910 / 1 960.5 M / 110 · transporte 1 197 / 1 011.1 M / 39.
- 25 regiones (24 departamentos + Callao). 80 procesos de muestra por rubro.

## Limitaciones (la app debe mostrarlas)

1. **"Abierto" es una estimación y `cierre` va siempre `null`.** La fuente no publica la fecha límite de presentación de ofertas (`tender.tenderPeriod.endDate` repite la fecha de convocatoria). Se marca abierto si todos los ítems están en CONVOCADO, no hay adjudicación y (el fin de consultas es >= 2026-11-19 o la convocatoria tiene 30 días o menos). El criterio queda escrito en `abierto_criterio` dentro del JSON. Hay que confirmar la fecha en SEACE antes de ofertar.
2. **Monto reservado**: la mayoría de procesos abiertos no trae monto (`null`) porque la ley peruana permite no publicar la cuantía hasta la buena pro. La app no debe mostrar "S/ 0".
3. **Cobertura**: solo procedimientos de selección de SEACE v3. No incluye contrataciones menores a 8 UIT (órdenes de compra/servicio) ni compras por catálogos electrónicos de Perú Compras, que es donde se mueve mucha papelería y útiles: por eso `papeleria` y `eventos` tienen pocos procesos.
4. **Ubicación = sede de la entidad**, con coordenada de la capital departamental (todas las entidades de un departamento caen en el mismo punto). Las entidades nacionales con sede en Lima concentran procesos que se ejecutan en otras regiones.
5. Los últimos días (17–19 nov) pueden estar incompletos: el archivo se generó el 2026-11-19 a las 14:48 -05.
6. Un proceso puede figurar con proveedor adjudicado y estado SUSPENDIDO, NULO o RETROTRAIDO: la adjudicación existió pero no está firme.
7. Campos extra respecto del esquema del brief: `clasificacion_nota`, `abierto_criterio` (raíz) y `"geo":"region"` en entidades.
