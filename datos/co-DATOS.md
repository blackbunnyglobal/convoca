# Colombia (co) — fuentes, consultas y reglas de limpieza

Snapshot descargado el **2026-11-19 entre 21:51 y 22:06 (hora de Colombia)** = 2026-11-20T02:51Z–03:06Z.
Todo sale de datos abiertos oficiales en `datos.gov.co` (API Socrata, sin llave, sin login, User-Agent `curl/8`).
Periodo: procesos con `fecha_de_publicacion_del` entre **2025-11-19 y 2026-11-19** (12 meses completos; el último proceso publicado en la fuente al descargar es del 2026-11-18 10:30).

## Fuentes

| Fuente | Endpoint | Uso | Registros |
|---|---|---|---|
| SECOP II - Procesos de Contratación (`p6dx-8zbt`) | https://www.datos.gov.co/resource/p6dx-8zbt.json | Fuente principal: procesos, montos, estados, adjudicaciones | 1.833.452 filas en el periodo (agregadas en servidor); 352.102 filas descargadas a detalle |
| SECOP II - Contratos Electrónicos (`jbjy-vk9h`) | https://www.datos.gov.co/resource/jbjy-vk9h.json | Segunda fuente: recupera el UNSPSC de procesos sin código; verificación | 254.509 contratos (firma >= 2026-08-15) |
| DIVIPOLA - Códigos municipios (`gdxc-w37w`) | https://www.datos.gov.co/resource/gdxc-w37w.json | Latitud/longitud oficial por municipio | 1.122 |
| Tasa Representativa del Mercado - TRM (`32sa-8pi3`) | https://www.datos.gov.co/resource/32sa-8pi3.json | COP por USD | 5 (últimas vigencias) |

**Tasa USD usada: 3.326,96 COP por 1 USD** (TRM vigente el 2026-11-19).

## Consultas exactas (todas con inicio 2026-11-20T02:51:41Z)

`PERIODO` = `fecha_de_publicacion_del >= '2025-11-19T00:00:00' AND fecha_de_publicacion_del < '2026-11-20T00:00:00'`
`VALIDO` = `PERIODO AND precio_base < 10000000000000 AND estado_del_procedimiento not in ('Cancelado','Borrador') AND modalidad_de_contratacion not in ('Solicitud de información a los Proveedores','Subasta de prueba')`

| Archivo crudo | Dataset | Consulta SoQL | Registros |
|---|---|---|---|
| tot_bruto | p6dx-8zbt | `$select=count(*), count(distinct id_del_proceso), sum(precio_base), min/max(fecha_de_publicacion_del)` `$where=PERIODO` | 1 (1.833.452 filas; 1.833.367 ids; 602.488.775.015.654 COP) |
| tot_valido | p6dx-8zbt | mismo select, `$where=VALIDO` | 1 (1.786.963 filas; 495.101.763.404.230 COP) |
| tot_valido_port | p6dx-8zbt | `$select=count(distinct id_del_portafolio)` `$where=VALIDO` | 1 (1.755.575) |
| dup_port | p6dx-8zbt | `$select=id_del_portafolio,nit_entidad,entidad,departamento_entidad,ciudad_entidad,count(*),sum(precio_base),max(precio_base)` `$where=VALIDO` `$group=(los 5 campos)` `$having=count(*)>1` | 22.113 |
| top_montos | p6dx-8zbt | `$where=PERIODO` `$order=precio_base DESC` `$limit=40` | 40 |
| excl_estado | p6dx-8zbt | `$select=estado_del_procedimiento,count(*),sum(precio_base)` `$where=PERIODO` `$group=estado_del_procedimiento` | 9 |
| tot_modalidad | p6dx-8zbt | `$select=modalidad_de_contratacion,count(*),sum(precio_base)` `$where=VALIDO` `$group=modalidad_de_contratacion` | 15 |
| tot_entidad | p6dx-8zbt | `$select=nit_entidad,entidad,departamento_entidad,ciudad_entidad,count(*),sum(precio_base)` `$where=VALIDO` `$group=(los 4 campos)` `$order=monto DESC` `$limit=300` | 300 |
| tot_region | p6dx-8zbt | `$select=departamento_entidad,count(*),sum(precio_base)` `$where=VALIDO` `$group=departamento_entidad` | 34 |
| rubro_alimentacion | p6dx-8zbt | `$where=PERIODO AND (starts_with(codigo_principal_de_categoria,'V1.50') OR ...'V1.9010')`, paginado 25.000, `$order=id_del_proceso,:id` | 11.205 |
| rubro_aseo | p6dx-8zbt | ídem con `V1.7611`, `V1.4713` | 10.389 |
| rubro_dotacion | p6dx-8zbt | ídem con `V1.53`, `V1.4618` | 7.448 |
| rubro_mantenimiento | p6dx-8zbt | ídem con `V1.7210`, `V1.7215`, `V1.31`, `V1.27`, `V1.30` | 54.120 |
| rubro_papeleria | p6dx-8zbt | ídem con `V1.4412`, `V1.1411`, `V1.4410` | 16.689 |
| rubro_eventos | p6dx-8zbt | ídem con `V1.801416`, `V1.801419`, `V1.901116`, `V1.9015` | 9.655 |
| rubro_software | p6dx-8zbt | ídem con `V1.4323`, `V1.8111`, `V1.8116`, `V1.4321` | 35.904 |
| rubro_transporte | p6dx-8zbt | ídem con `V1.7811`, `V1.7810` | 11.116 |
| sin_codigo | p6dx-8zbt | `$where=PERIODO AND codigo_principal_de_categoria='UNSPECIFIED' AND justificaci_n_modalidad_de != 'Servicios profesionales y apoyo a la gestión'`, paginado 25.000 | 195.576 |
| contratos | jbjy-vk9h | `$select=proceso_de_compra,id_contrato,codigo_de_categoria_principal,proveedor_adjudicado,valor_del_contrato,fecha_de_firma` `$where=fecha_de_firma >= '2026-08-15T00:00:00'`, paginado 50.000 | 254.509 |
| divipola | gdxc-w37w | `$limit=5000` | 1.122 |
| trm | 32sa-8pi3 | `$order=vigenciadesde DESC` `$limit=5` | 5 |
| kwval_muestra (validación) | p6dx-8zbt | filas con UNSPSC publicadas 2026-08-03..2026-08-14, sin personas ni solicitudes de información (descargado 2026-11-19 22:08) | 39.527 |

Los parámetros literales quedan además en el `fetch_log.json` que escribe `co_fetch.mjs`.

## Scripts (reproducibles, `convoca/datos/scripts/`)

1. `node co_fetch.mjs <dir_crudos>` — descarga todo lo anterior (≈15 min; ≈530 MB de crudos, van al scratchpad, no al proyecto).
2. `node --max-old-space-size=8000 co_build.mjs <dir_crudos> ../co.json [stats.json]` — limpia, clasifica, deduplica y escribe `co.json`.
3. `node co_kwval.mjs <dir>` — mide las palabras clave contra filas que sí traen UNSPSC.
4. `node co_verify.mjs ../co.json <salida.json> [semilla]` — verificación al azar (ver `co-VERIFICACION.md`).
5. `co_lib.mjs` — rubros, prefijos UNSPSC y expresiones de palabras clave.

## Hallazgo importante: la fuente dejó de publicar el UNSPSC

Desde mediados de **septiembre de 2026** el campo `codigo_principal_de_categoria` del dataset de procesos viene como `UNSPECIFIED` (sep-2026: 75.897 de 159.293 filas; oct-2026: 147.817 de 148.827; nov-2026: 72.794 de 73.172). Por eso **todos los procesos abiertos hoy vienen sin código**. Se resolvió así, en este orden:

1. **UNSPSC del proceso** (`clasif: "UNSPSC"`): filas con código `V1.xxxxxxxx`, por prefijo.
2. **UNSPSC del contrato** (`clasif: "UNSPSC (contrato)"`): fila sin código cuyo `id_del_portafolio` aparece como `proceso_de_compra` en Contratos Electrónicos, que sí conserva el código. 7.334 filas entraron a un rubro así; 32.612 tenían contrato con código fuera de los 8 rubros y se descartaron.
3. **Palabras clave** (`clasif: "palabras clave"`, estimado): filas sin código y sin contrato; se asigna el rubro cuya expresión aparece primero en la descripción (o en el nombre). 20.239 filas entraron así; 135.391 no calzaron con ningún rubro. Antes se descartan objetos que no son compras para una pyme (prestación de servicios profesionales/apoyo a la gestión, "aunar esfuerzos", convenios, contratos interadministrativos, pólizas/seguros, arrendamientos, empréstitos, comodatos).

Cada rubro trae `n_palabras_clave` y cada proceso de la muestra trae `clasif` y `codigo`.

Calidad de las palabras clave, medida contra 39.527 filas que sí traen UNSPSC (coincidencia exacta con el rubro del código principal):

| Rubro | Precisión | Cobertura (recall) |
|---|---|---|
| alimentacion | 0,44 | 0,30 |
| aseo | 0,57 | 0,51 |
| dotacion | 0,63 | 0,44 |
| mantenimiento | 0,74 | 0,23 |
| papeleria | 0,72 | 0,62 |
| eventos | 0,15 | 0,28 |
| software | 0,71 | 0,42 |
| transporte | 0,57 | 0,59 |

Lectura: la precisión está subestimada porque el código principal que ponen las entidades es ruidoso (p. ej. "apoyo logístico para eventos" suele ir en 8011/9014/9314 y el PAE en 9313, fuera de los prefijos del brief), pero es una clasificación **estimada**; en eventos y alimentación la coincidencia con el código es baja. La cobertura baja implica que **`abiertos` subestima** las oportunidades reales.

## Reglas de limpieza

- **Un proceso = un `id_del_portafolio`** (`CO1.BDOS.*`). El dataset publica una fila por fase (`CO1.REQ.*`: observaciones al borrador, manifestación de interés, presentación de oferta) y cada fase repite el `precio_base`. Solo 85 `id_del_proceso` están repetidos, pero 22.113 portafolios tienen más de una fila. Se cuenta cada portafolio una vez con su mayor `precio_base`. En el total país esto descuenta **31.388 filas y 67.827.171.111.773 COP**. En los rubros se descartaron 10 filas por id repetido y 8.293 por portafolio repetido; la fila representativa es la adjudicada, si no la abierta, si no la más reciente.
- **Outliers**: se miró el top 40 de `precio_base`. Regla: se excluye `precio_base >= 10.000.000.000.000 COP` (10 billones, ≈ 3.000 millones USD). Salen **4 registros** (91,94 billones COP): CO1.REQ.9645201 (FNGRD, 44,77 billones, "aunar esfuerzos"), CO1.REQ.11261044 (Fuerza Aeroespacial, 19,64 billones, mantenimiento), CO1.REQ.10258198 (MinDefensa, 16,53 billones, aeronaves de superioridad aérea), CO1.REQ.10759463 (EFR SAS, 11,00 billones, concesión). Los dos primeros parecen error de digitación; los otros dos pueden ser reales pero no son mercado para pymes y distorsionan cualquier suma. Ninguno cae en los 8 rubros.
- **Estados excluidos**: `Cancelado` (15.814 filas; 15,36 billones) y `Borrador` (146 filas; 0,08 billones).
- **Modalidades excluidas** (no son compras): `Solicitud de información a los Proveedores` (30.295 filas, monto 0) y `Subasta de prueba` (230 filas, monto 0).
- **Personas naturales**: en los rubros se excluye la contratación directa con justificación `Servicios profesionales y apoyo a la gestión` (6.359 filas con UNSPSC de los rubros, sobre todo 8111 y 7611; y no se descargan las 108.966 sin código). Sí cuentan en el total país.
- **Total país** (`total`): 1.755.575 procesos y 427.274.592.292.457 COP = filas válidas menos la corrección por portafolio. Incluye todas las modalidades; régimen especial (881.841 filas) y contratación directa (780.323 filas) son la gran mayoría de los procesos.
- **Rubros**: `n` y `monto` sobre procesos deduplicados; `mediana`, `p25`, `p75` solo sobre procesos con `precio_base > 0` (`n_con_monto`). Total filas en rubros antes de reglas: 156.526 con UNSPSC + 27.573 recuperadas.
- **Abierto** = `estado_del_procedimiento = 'Publicado'` y `fecha_de_recepcion_de >= 2026-11-19T00:00`. El estado `Abierto` de la fuente significa "sobres abiertos" (ya cerró) y no se usa.
- **Proveedores**: solo filas con `adjudicado = 'Si'` y `nombre_del_proveedor` definido; monto = `valor_total_adjudicacion`. Solo nombres, nunca documentos.
- **Duración**: `duracion` × unidad (día 1, semana 7, mes 30, año 365); 0 u horas → `null`.
- **Coordenadas**: cruce por departamento + municipio contra DIVIPOLA normalizando tildes y mayúsculas, con una tabla de alias de grafía (Cali → Santiago de Cali, Cúcuta → San José de Cúcuta, Cartagena → Cartagena de Indias, etc.). Todos los municipios de la salida cruzaron. Si la entidad no trae municipio (`No Definido`) se usa el promedio de las coordenadas de los municipios de su departamento y se marca `"geo":"region"`; sin departamento → `lat/lon: null`. Bogotá (departamento "Distrito Capital de Bogotá") usa la coordenada de Bogotá, D.C.
- **Texto**: trim y colapso de espacios; `objeto` = descripción (o nombre si falta) recortado a 170 caracteres.
- **Muestra de procesos** (80 por rubro): abiertos primero (hasta 45, los que cierran antes y traen monto primero; si hay más de 45 se reservan 2 por cada una de las 8 regiones con más procesos), luego adjudicados más recientes con proveedor, monto y URL; se garantizan 12 de Bogotá y 2 por cada región del top 8.

## Campos extra respecto al esquema del brief

- Proceso: `clasif`, `codigo` (UNSPSC o `null`), `cierre_hora` (`YYYY-MM-DDTHH:MM`, hora de Colombia).
- Rubro: `n_con_monto`, `n_palabras_clave`, `n_adjudicados_con_proveedor`.
- Entidad: `geo: "region"` cuando la coordenada es del departamento.

## Limitaciones

- `precio_base` es presupuesto publicado, no valor contratado. Los `monto` de rubro están dominados por megacontratos (Ecopetrol, Hocol, Invías, TransMilenio): para una pyme usar mediana y cuartiles.
- Un proceso abierto puede traer `monto: null` (la entidad publicó precio 0) y `ciudad`/`region: null`.
- Procesos con cierre 2026-11-19 pueden haber cerrado ya por hora (ver `cierre_hora`); el snapshot llega hasta lo publicado el 2026-11-18 10:30.
- Abiertos y procesos desde sep-2026 dependen de la clasificación estimada descrita arriba.
- Solo una parte de los procesos trae datos de adjudicación en este dataset (régimen especial y directa casi nunca): `proveedores` y `top_proveedor_share` describen esa parte.
- La corrección por portafolio no ve fases publicadas fuera del periodo; `entidades_top` se arma con las 300 entidades de mayor monto antes de la corrección.
- SECOP II no incluye SECOP I ni la Tienda Virtual del Estado (acuerdos marco).
