# México (mx) — fuentes y reglas

Estado: **ok** (con limitaciones, ver abajo). Periodo real: **2025-11-19 a 2026-11-19** (fecha de publicación del procedimiento). Descarga: 2026-11-20T02:51:15Z.

## Fuentes

| Fuente | URL exacta | Consulta | Descarga (UTC) | Registros |
|---|---|---|---|---|
| Compras MX, Datos Abiertos, "Expedientes publicados en Compras MX 2025" | https://upcp-cnetservicios.buengobierno.gob.mx/norah/documentos/recursos/lck/expedientes/obtener/Expedientes_ComprasMX_2025.csv | archivo completo (CSV latin-1) | 2026-11-20T02:51:15Z | 23 448 filas |
| Ídem 2026 | .../obtener/Expedientes_ComprasMX_2026.csv | archivo completo | 2026-11-20T02:51:15Z | 7 129 filas |
| Compras MX, Datos Abiertos, "Contratos de la Plataforma Integral Compras MX 2025" | .../obtener/Contratos_ComprasMX_2025.csv | archivo completo | 2026-11-20T02:51:15Z | 32 967 filas (1 malformada descartada) |
| Ídem 2026 | .../obtener/Contratos_ComprasMX_2026.csv | archivo completo | 2026-11-20T02:51:15Z | 24 866 filas |
| Banco de México, tipo de cambio FIX | https://www.banxico.org.mx/tipcamb/tipCamMIAction.do | FIX del 19/11/2026 = 19.0270 MXN/USD | 2026-11-20 | 1 |
| INEGI, Catálogo Único de Claves Geoestadísticas (API wscatgeo, sin token) | https://gaia.inegi.org.mx/wscatgeo/v2/localidades/{ent}/{mun} | localidad 0001 del municipio capital de cada entidad | 2026-11-20 | 32 capitales (`scripts/mx_geo_capitales.json`) |

Portal: https://comprasmx.buengobierno.gob.mx/datos-abiertos (sin login). Las URL directas de los CSV se obtuvieron abriendo esa página en un navegador y pulsando cada documento; después se descargan con `curl` sin cabeceras especiales (`scripts/mx_descarga.sh`). Una petición HEAD devuelve 403; GET funciona.
Se descartó el archivo viejo `upcp-compranet.buengobierno.gob.mx/cnetassets/datos_abiertos_contratos_expedientes/Contratos_CompraNet2025.csv` (última modificación 2025-09-29, anterior al periodo).

Scripts: `scripts/mx_descarga.sh`, `scripts/mx_geo.mjs`, `scripts/mx_build.py` (`python3 -I mx_build.py <crudos> mx_geo_capitales.json mx.json`), `scripts/mx_verif_muestra.mjs`.

## Cómo se construyó

- **Unidad = procedimiento de contratación**, clave `Número de procedimiento` (`id`); `ref` = código del expediente (un expediente puede contener varios procedimientos, cada uno con su URL).
- Universo = unión de (a) "Expedientes publicados" con `Tipo elemento = PROCEDIMIENTO DE CONTRATACIÓN` y (b) procedimientos que solo aparecen en "Contratos". Resultado en el periodo: **31 211 procedimientos** (9 386 vienen de Expedientes; 21 825 solo de Contratos).
- Fecha del proceso = `Fecha publicación`. En 147 procedimientos que solo están en Contratos y no traen fecha de publicación se usó la fecha de firma o, si falta, la de inicio del contrato.
- **Monto = monto contratado** (suma de `Importe DRC` de los contratos del procedimiento, MXN, sin impuestos; en contratos abiertos es el importe mínimo). **La fuente no publica presupuesto base**: los procesos abiertos y los que aún no tienen contrato llevan `monto: null`. `monto` y `monto_adjudicado` son el mismo valor. 23 731 de 31 211 procedimientos tienen monto.
- **Clasificación**: partida específica del Clasificador por Objeto del Gasto (COG), no UNSPSC. Un procedimiento entra a un rubro solo si TODAS sus partidas caen en ese rubro (los mixtos quedan fuera). Mapa: alimentacion 221xx; aseo 216xx, 358xx; dotacion 271xx, 272xx; mantenimiento 351xx, 241xx-249xx, 291xx, 292xx; papeleria 211xx, 212xx; eventos 382xx, 383xx, 384xx; software 327xx, 515xx, 591xx, 597xx, 33301, 33304; transporte 347xx, 318xx. Es un mapeo propio (estimado).
- **Abierto hoy** = procedimiento de "Expedientes publicados" con estatus VIGENTE / EN ACLARACIONES / EN REPREGUNTAS / EN ATENCIÓN DE PREGUNTAS / PENDIENTE DE APERTURA / EN APERTURA y `Fecha apertura` (presentación y apertura de proposiciones) >= 2026-11-19. `cierre` = esa fecha.
- **Estado**: el de la fuente; para los procedimientos que solo están en Contratos el archivo no trae estatus y se puso `CONTRATO PUBLICADO` (en el portal figuran como ADJUDICADO).
- **Región** = `Entidad Federativa` del expediente (9 386). Para los que solo están en Contratos (sin ese campo): si es gobierno estatal (`Orden de gobierno = GEM`) el estado de su ramo (2 145); si no, la entidad federativa más frecuente de su unidad compradora en "Expedientes publicados" (19 637, inferido); 43 quedan con `region: null`.
- **Coordenadas**: capital de la entidad federativa (INEGI), siempre `"geo":"region"`; `ciudad` es `null` (la fuente no trae municipio). La región de una entidad compradora es la más frecuente entre sus procedimientos (p. ej. SICT sale en Oaxaca aunque es nacional).
- `duracion_dias` = fin menos inicio del contrato cuando el procedimiento tiene un solo contrato.
- Proveedores: solo el nombre (nunca el RFC). Aparecen personas físicas por nombre, tal como las publica la fuente.

## Exclusiones (conteos sobre todo el archivo, antes del filtro de periodo salvo indicación)

- 570 filas "PROYECTO DE CONVOCATORIA" (no son procedimientos).
- 4 contratos duplicados por código de contrato (se conserva el del archivo 2026) y 1 fila malformada.
- 151 contratos NO FORMALIZADO o RESCINDIDO.
- 115 contratos del periodo en moneda distinta de MXN (USD/EUR): el procedimiento se cuenta, su importe no se suma.
- Importes <= 0: no se suman. Regla anti-digitación: contrato > 50 000 millones MXN se excluye; **0 casos** (el mayor contrato del periodo es de 8 998 millones, tren de pasajeros, plausible; se revisó el top 20).
- 158 procedimientos sin ninguna fecha y 35 505 fuera del periodo.

## Limitaciones

- Solo la plataforma federal Compras MX (dependencias federales y estados/municipios cuando usan recursos federales). No es todo el gasto público de México.
- "Expedientes publicados" trae muchos menos procedimientos que "Contratos" (solo ~25 % de los expedientes con contrato aparecen en él), por eso el universo es la unión de ambos.
- Sin presupuesto base: mediana/p25/p75 son de montos contratados.
- La clasificación por partida deja fuera procedimientos de varias partidas y puede incluir objetos atípicos (la partida 21101 a veces se usa para mobiliario).
