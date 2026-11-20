# Colombia (co) — verificación de 3 procesos al azar

- Comando: `node scripts/co_verify.mjs co.json <salida.json> 20261119`
- Azar: generador mulberry32 con **semilla 20261119** sobre los 640 procesos de la muestra (8 rubros × 80); índices elegidos: **372, 234, 178**.
- Fecha de la verificación: 2026-11-19 22:10 (hora de Colombia).
- Portal público (`community.secop.gov.co`, la `url` de cada proceso): respondió **HTTP 403** a las tres consultas automáticas (bloqueo de bots). Según el brief se comparó entonces contra consultas independientes de la fuente oficial: (a) registro completo por `id_del_proceso` en `p6dx-8zbt`, (b) todas las fases del `id_del_portafolio`, (c) el contrato en **Contratos Electrónicos `jbjy-vk9h`** por `proceso_de_compra` cuando existe.

## Caso 1 — papeleria · CO1.REQ.11216443 (portafolio CO1.BDOS.10677014)

| Campo | co.json | Fuente (registro por id) | ¿Cuadra? |
|---|---|---|---|
| entidad | MUNICIPIO DE GUADALUPE | MUNICIPIO DE GUADALUPE | sí |
| ref | MG-SASI-007-2026 (Presentación de oferta) | igual | sí |
| ciudad / region | null / Huila | No Definido / Huila | sí (sin municipio → null) |
| monto | 92.090.050 | precio_base 92090050 | sí |
| modalidad | Selección abreviada subasta inversa | igual | sí |
| estado / abierto | Seleccionado / false | Seleccionado; cierre 2026-10-23 | sí |
| publicado / cierre | 2026-10-15 / 2026-10-23 08:00 | 2026-10-15T19:18 / 2026-10-23T08:00 | sí |
| duracion_dias | 60 | 2 Mes(es) | sí (2 × 30) |
| proveedor / monto_adjudicado | BYG SISTEMAS SAS / 18.255.000 | BYG SISTEMAS SAS / 18255000 | sí |
| url | noticeUID=CO1.NTC.11071003 | igual | sí |
| clasif | palabras clave | código UNSPECIFIED, sin contrato aún | sí (estimado) |

Fases del portafolio: 2 filas (observaciones, 2026-08-14; oferta, 2026-10-15), ambas con precio 92.090.050 → se contó una sola vez. Contratos Electrónicos: aún sin contrato. Nota: el adjudicado (18,3 M) es mucho menor que el presupuesto (92,1 M); así está en la fuente (posible adjudicación por lotes).

## Caso 2 — dotacion · CO1.REQ.11271415 (portafolio CO1.BDOS.10984014)

| Campo | co.json | Procesos (por id) | Contratos Electrónicos (2ª fuente) | ¿Cuadra? |
|---|---|---|---|---|
| entidad | AEROCIVIL | AEROCIVIL | AEROCIVIL | sí |
| ref | 26001477 H1 (Manifestación de interés (Menor Cuantía)) (Presentación de oferta) | igual (con doble espacio, colapsado) | 26001477 H1 | sí |
| ciudad / region | Bogotá / Distrito Capital de Bogotá | No Definido / Distrito Capital de Bogotá | No Definido / Distrito Capital de Bogotá | sí (regla Bogotá) |
| objeto | 63500C1237 ADQUIRIR ELEMENTOS DE PROTECCIÓN PERSONAL… | igual | igual | sí |
| monto | 298.379.404 | 298379404 | — | sí |
| modalidad | Selección Abreviada de Menor Cuantía | igual | igual | sí |
| estado | Adjudicado | Seleccionado; estado_resumen Adjudicado | contrato Aprobado | sí |
| publicado / cierre | 2026-10-26 / 2026-11-03 17:00 | igual | firma 2026-11-17 | sí |
| duracion_dias | null | 0 día(s) | — | sí (0 → null) |
| proveedor | PROVEEDURIA DEL NORTE S.B.A | igual | PROVEEDURIA DEL NORTE S.B.A | sí |
| monto_adjudicado | 298.341.925 | 298341925 | valor_del_contrato 298341925 | sí |
| codigo / clasif | V1.46181704 / UNSPSC (contrato) | UNSPECIFIED | V1.46181704 | sí |
| url | noticeUID=CO1.NTC.11125505 | igual | mismo noticeUID | sí |

Fases del portafolio: 3 filas con el mismo precio → contado una vez.

## Caso 3 — dotacion · CO1.REQ.11369339 (portafolio CO1.BDOS.11178050) — abierto

| Campo | co.json | Fuente (registro por id) | ¿Cuadra? |
|---|---|---|---|
| entidad | AMAZONAS - CONTRALORIA DEPARTAMENTAL DEL AMAZONAS | igual | sí |
| ref | MC-08-2026 | igual | sí |
| ciudad / region | Leticia / Amazonas | Leticia / Amazonas | sí |
| objeto | CONTRATAR LA ADQUISICIÓN DE DOTACION DE CALZADO Y VESTIDO DE LABOR… | igual | sí |
| monto | 1.500.000 | 1500000 | sí |
| modalidad | Mínima cuantía | igual | sí |
| estado / abierto | Publicado / true | Publicado, fase Presentación de oferta | sí |
| publicado / cierre | 2026-11-13 / 2026-11-19 17:00 | 2026-11-13T09:35 / 2026-11-19T17:00 | sí |
| duracion_dias | 30 | 1 Mes(es) | sí |
| proveedor / monto_adjudicado | null / null | No Definido / 0 | sí |
| url | noticeUID=CO1.NTC.11224480 | igual | sí |
| clasif | palabras clave | UNSPECIFIED, sin contrato | sí (estimado; el objeto es dotación de calzado y vestido) |

Una sola fase. Ojo: cerraba el mismo 19 de noviembre a las 17:00; se cuenta como abierto por la regla `cierre >= 2026-11-19` (ver `cierre_hora`).

## Resultado

**3 de 3 procesos cuadran en todos los campos**; no hubo que corregir `co.json`. El caso 2 se confirmó además con una segunda fuente independiente (Contratos Electrónicos): entidad, proveedor, valor, modalidad, código UNSPSC y URL. Lo que NO se pudo hacer: leer la ficha en el portal SECOP II (403 a clientes automáticos); las `url` son las que publica la fuente y conviene abrir una a mano en el navegador.

## Otras comprobaciones de consistencia

- `total.n` = 1.786.963 filas válidas − 31.388 filas de fases repetidas = **1.755.575**, igual a `count(distinct id_del_portafolio)` calculado aparte en el servidor (1.755.575).
- Bruto − válido = 107,39 billones COP = 4 outliers (91,94) + Cancelado (15,36) + Borrador (0,08). Filas: 1.833.452 − 1.786.963 = 46.489 = 15.814 + 146 + 4 + 30.295 + 230.
- `co.json` validado con `node -e "JSON.parse(...)"` (545 KB, minificado, UTF-8).
