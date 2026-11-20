# CL · Chile — ficha de datos (CONVOCA)

Estado: **parcial**. Hay 12 meses de licitaciones cerradas con monto, rubro UNSPSC, región y adjudicatario; **no hay procesos abiertos** (la descarga masiva abierta solo trae licitaciones ya cerradas) y las coordenadas son de capital regional, no de comuna.

## Fuentes

| # | Fuente | URL exacta | Consulta / uso | Descarga (UTC) | Registros |
|---|---|---|---|---|---|
| 1 | Datos Abiertos ChileCompra, descarga masiva de licitaciones (Mercado Público). Portal: https://datos-abiertos.chilecompra.cl/ | `https://transparenciachc.blob.core.windows.net/lic-da/<AAAA>-<M>.zip` para 2025-11, 2025-12, 2026-1 … 2026-11 (13 archivos, mes sin cero a la izquierda) | GET anónimo con `curl`, sin login ni ticket. CSV `;`, Windows-1252, una fila por ítem × oferta | 2026-11-20T02:45:17Z a 02:51:36Z | 1.660.673 filas CSV → 96.625 licitaciones únicas → 91.325 en el periodo → 91.206 tras exclusiones |
| 2 | Dólar observado, UF, UTM, euro (mindicador.cl, series del Banco Central de Chile) | `https://mindicador.cl/api` | Dólar 1.002,07 CLP (2026-11-19); UF 41.364,17 (2026-11-19); euro 1.099,60 (2026-11-19); UTM 72.440 (2026-11) | 2026-11-20T02:47Z | 4 valores |
| 3 | Coordenadas | sin fuente oficial accesible (ver Limitaciones) | Centroide urbano aproximado de las 16 capitales regionales, conocimiento geográfico estándar, codificado en `scripts/cl_2_agregar.py` (`CAP`) | — | 16 puntos, todos marcados `"geo":"region"` |

Detalle por archivo de la fuente 1 (la fecha "generado" es el `Last-Modified` del blob: el estado de cada licitación es el de ese día, no el de hoy):

| Archivo | Bytes ZIP | Licitaciones | Generado |
|---|---|---|---|
| 2025-11 | 15.306.407 | 8.627 (3.320 desde el día 19) | 2026-04-15 |
| 2025-12 | 12.180.712 | 7.070 | 2026-04-15 |
| 2026-1 | 13.974.048 | 7.198 | 2026-04-28 |
| 2026-2 | 14.754.340 | 7.526 | 2026-05-28 |
| 2026-3 | 17.076.058 | 7.893 | 2026-07-01 |
| 2026-4 | 17.215.166 | 7.764 | 2026-07-30 |
| 2026-5 | 15.341.368 | 7.537 | 2026-08-30 |
| 2026-6 | 18.074.028 | 8.649 | 2026-10-01 |
| 2026-7 | 16.591.032 | 8.208 | 2026-10-28 |
| 2026-8 | 17.573.664 | 8.296 | 2026-11-17 |
| 2026-9 | 16.599.377 | 7.871 | 2026-11-17 |
| 2026-10 | 15.038.040 | 8.282 | 2026-11-17 |
| 2026-11 | 3.121.387 | 1.704 | 2026-11-20 |

## Periodo real
`FechaPublicacion` entre **2025-11-19 y 2026-11-13** (último día con datos en el archivo de noviembre). No se anualizó ni extrapoló nada. Noviembre 2026 es parcial (1.701 licitaciones, solo las ya cerradas).

## Reproducir
```sh
sh scripts/cl_0_descargar.sh <tmp>                                   # 13 ZIP + mindicador
for m in 2025-11 2025-12 2026-1 ... 2026-11; do python3 -I scripts/cl_1_reducir.py <tmp>/raw/lic-$m.zip <tmp>/red/$m.jsonl; done
python3 -I scripts/cl_2_agregar.py <tmp>/red <tmp>/mindicador.json <ISO descarga> cl.json
unzip -p <tmp>/raw/lic-2026-9.zip | perl scripts/cl_3_verificar.pl <CodigoExterno>   # verificación
```

## Reglas de limpieza y decisiones
- **Unidad**: una licitación = un `CodigoExterno`. El CSV trae una fila por ítem × oferta; se deduplica por código. Los campos de cabecera se toman de la primera fila.
- **Monto del proceso** = `MontoEstimado` de la licitación (presupuesto publicado), no la suma adjudicada. Números con coma decimal y notación `3,815e+09` se convierten a número.
- **Monedas**: 1.824 licitaciones vienen en UF, USD, UTM o EUR; se convierten a CLP con los valores de mindicador.cl del 2026-11-19 (una sola fecha, no la de cada proceso).
- **Monto nulo**: `MontoEstimado` ausente o menor a 1.000 CLP (marcadores tipo "1") queda `null`: 529 licitaciones cuentan en `n` y no suman monto.
- **Rubro**: UNSPSC (`CodigoProductoONU`) por ítem, con los prefijos del brief. Cada licitación va a **un solo rubro**: el más frecuente entre sus ítems distintos (contando "otros"); en empate gana un rubro sobre "otros", en el orden del brief. Así ningún monto se cuenta dos veces. Eventos = 801416, 801419, 901116 y 9015.
- **Adjudicación**: ofertas con `Oferta seleccionada = Seleccionada`; `monto_adjudicado` = suma de `MontoLineaAdjudica`. `proveedor` = razón social del adjudicatario con mayor monto. Los RUT se usan solo para agrupar y no salen al JSON.
- **Muestra de procesos** (80 por rubro): como no hay abiertos, son los adjudicados más recientes por `FechaAdjudicacion` (si la fecha es posterior a hoy o anterior a la publicación, se usa la de publicación), con cuotas de al menos 12 de la Región Metropolitana y 2 de cada una de las 8 regiones con más procesos del rubro.
- **Entidad** = `NombreOrganismo` tal como viene. `region` = la más frecuente de sus unidades compradoras; `ciudad` = comuna más frecuente dentro de esa región.
- **`duracion_dias`**: `TiempoDuracionContrato` × unidad (2 = días, 3 = semanas ×7, 4 = meses ×30, 5 = años ×365). La equivalencia de códigos es la de la documentación de la API de Mercado Público y coincide con la distribución observada (unidad 4 concentra 12, 24 y 36). Es `null` cuando el valor es 0 o la unidad es 0/1.
- **URL**: `https://www.mercadopublico.cl/Procurement/Modules/RFB/DetailsAcquisition.aspx?idlicitacion=<CodigoExterno>`.

## Exclusiones
- **5.300** licitaciones del archivo 2025-11 publicadas antes del 2025-11-19 (fuera de periodo).
- **119 licitaciones excluidas por monto absurdo** (salen de `n` y de `monto`; suman 1,5 × 10^15 CLP, 92 veces el total válido):
  - 88 con `MontoEstimado` ≥ 100.000.000.000 CLP. El top 20 previo mostraba, p. ej., un servicio de aseo de tribunales por 2,4 × 10^14 CLP y una recolección de residuos municipal por 1,0 × 10^12 CLP.
  - 31 con monto superior a 10 veces el límite legal de su propio tramo (L1/E2 100 UTM, LE/CO 1.000 UTM, LP/LQ/B2/H2 5.000 UTM; UTM = 72.440 CLP). Ejemplo: una licitación L1 (menor a 100 UTM) por 7,0 × 10^10 CLP.
  - 107 de las 119 están declaradas en UF, USD o UTM: el patrón es un monto digitado en pesos con la moneda marcada como UF.
- **166 adjudicaciones** no se usan en `monto_adjudicado`, `proveedores` ni `top_proveedor_share` porque la suma adjudicada es ≥ 10^11 CLP o supera 10 veces el monto estimado (precios unitarios mal digitados). El proceso sigue contando en `n` y `monto`.
- **849 filas** del archivo 2026-3 (0,5 % de sus 160.271) tienen un número de columnas distinto al encabezado y se omiten.

## Limitaciones
1. **Sin procesos abiertos.** Los 13 archivos solo contienen estados Adjudicada (63.843), Cerrada (14.063), Desierta (10.732), Revocada (2.533) y Suspendida (35); ninguna "Publicada", y el cierre más tardío es 2026-11-18. Por eso `abiertos` = 0 en todos los rubros y `abierto` = false en toda la muestra. Las licitaciones vigentes solo están en la API `api.mercadopublico.cl`, que exige ticket, y en el buscador web; no se usó ninguna de las dos.
2. **Estado desfasado en meses antiguos.** Los archivos de 2025-11 a 2026-7 se generaron entre abril y octubre de 2026 y no se regeneran: una licitación "Cerrada" ahí puede estar adjudicada hoy.
3. **Coordenadas a nivel región.** La API oficial de División Político-Administrativa (`apis.digital.gob.cl/dpa`) no resolvió DNS y `apis.modernizacion.cl` rechazó la conexión. Todas las entidades de una región caen en el mismo punto (su capital). `ciudad` sí es la comuna real de la unidad compradora.
4. **Solo licitaciones** (públicas y privadas). No incluye convenio marco, trato directo ni compra ágil, que están en las órdenes de compra (`oc-da`, unos 100 MB por mes, no procesadas).
5. **Montos estimados dudosos bajo el tope.** Las licitaciones LR (más de 5.000 UTM) no tienen límite superior y quedan algunas sospechosas, p. ej. "mantención de cuentas corrientes" de una municipalidad por 6,3 × 10^10 CLP. Las medianas y percentiles por rubro son robustos a esto; los totales y los rankings por monto, menos.
6. **`monto_adjudicado` es aproximado.** En convenios de suministro la línea adjudicada suele ser un precio unitario, así que puede ser muy inferior al estimado.
7. **Región y comuna inconsistentes en origen.** El 1,6 % de las licitaciones trae una región que no corresponde a su comuna (p. ej. comuna Talca con Región Metropolitana). Se dejan como vienen. 7.497 licitaciones no traen comuna (`ciudad` = null).
8. **Conversión de moneda con tasa de una sola fecha** (2026-11-19) para las 1.824 licitaciones no denominadas en CLP.
