# Perú (pe) — verificación

Fecha: 2026-11-19 (22:05 hora de Lima).

## Método

- Selección al azar: `node scripts/pe_verify.mjs pe.json 20261119` (PRNG mulberry32, **semilla 20261119**, sobre los 640 procesos de la muestra de los 8 rubros). Repetir el comando da los mismos 3.
- Contraste: consulta independiente a la fuente oficial, el registro completo por id de la API OCDS (`/api/v1/record/{ocid}`), distinta de las descargas masivas mensuales con que se armó `pe.json`.
- Además se abrió en navegador la ficha pública (`url`) del tercer proceso.

## Resultado: 3 de 3 procesos cuadran en los 12 campos comparados. No hubo que corregir `pe.json`.

### ocds-dgv273-seacev3-1213114 (rubro papeleria)
HTTP 200 https://contratacionesabiertas.oece.gob.pe/api/v1/record/ocds-dgv273-seacev3-1213114?format=json
ficha: https://contratacionesabiertas.oece.gob.pe/proceso/ocds-dgv273-seacev3-1213114
UNSPSC ítems: 44101809
| campo | pe.json | API record | ¿cuadra? |
|---|---|---|---|
| ref | "LP-ABR-1-2026-MINEDU/UE120-1" | "LP-ABR-1-2026-MINEDU/UE120-1" | sí |
| entidad | "UNIDAD EJECUTORA 120 PROGRAMA NACIONAL DE DOTACION DE MATERIALES EDUCATIVOS" | "UNIDAD EJECUTORA 120 PROGRAMA NACIONAL DE DOTACION DE MATERIALES EDUCATIVOS" | sí |
| ciudad | "SAN BORJA" | "SAN BORJA" | sí |
| region | "LIMA" | "LIMA" | sí |
| objeto | "ADQUISICIÓN DE CALCULADORAS GRAFICADORAS PARA LOS ESTUDIANTES DE LOS COLEGIOS DE ALTO RENDIMIENTO - DOTACIÓN 2027" | "ADQUISICIÓN DE CALCULADORAS GRAFICADORAS PARA LOS ESTUDIANTES DE LOS COLEGIOS DE ALTO RENDIMIENTO - DOTACIÓN 2027" | sí |
| monto | 328260.6 | 328260.6 | sí |
| modalidad | "Licitación Pública Abreviada" | "Licitación Pública Abreviada" | sí |
| estado | "CONTRATADO" | "CONTRATADO" | sí |
| publicado | "2026-05-07" | "2026-05-07" | sí |
| duracion_dias | 125 | 125 | sí |
| proveedor | "KS INTERNATIONAL GROUP SAC" | "KS INTERNATIONAL GROUP SAC" | sí |
| monto_adjudicado | 253125 | 253125 | sí |

### ocds-dgv273-seacev3-1253292 (rubro dotacion)
HTTP 200 https://contratacionesabiertas.oece.gob.pe/api/v1/record/ocds-dgv273-seacev3-1253292?format=json
ficha: https://contratacionesabiertas.oece.gob.pe/proceso/ocds-dgv273-seacev3-1253292
UNSPSC ítems: 46181509
| campo | pe.json | API record | ¿cuadra? |
|---|---|---|---|
| ref | "LP-ABR-17-2026-SENASA CENTRAL-1" | "LP-ABR-17-2026-SENASA CENTRAL-1" | sí |
| entidad | "SERVICIO NACIONAL DE SANIDAD AGRARIA" | "SERVICIO NACIONAL DE SANIDAD AGRARIA" | sí |
| ciudad | "JESUS MARIA" | "JESUS MARIA" | sí |
| region | "LIMA" | "LIMA" | sí |
| objeto | "ADQUISICIÓN DE MAMELUCO DE PROTECCIÓN DESCARTABLE PARA EL PERSONAL DE SENASA" | "ADQUISICIÓN DE MAMELUCO DE PROTECCIÓN DESCARTABLE PARA EL PERSONAL DE SENASA" | sí |
| monto | 77931 | 77931 | sí |
| modalidad | "Licitación Pública Abreviada" | "Licitación Pública Abreviada" | sí |
| estado | "SUSPENDIDO" | "SUSPENDIDO" | sí |
| publicado | "2026-09-25" | "2026-09-25" | sí |
| duracion_dias | null | null | sí |
| proveedor | "EROSMEDIC S.A.C." | "EROSMEDIC S.A.C." | sí |
| monto_adjudicado | 50568.56 | 50568.56 | sí |

### ocds-dgv273-seacev3-1263408 (rubro dotacion)
HTTP 200 https://contratacionesabiertas.oece.gob.pe/api/v1/record/ocds-dgv273-seacev3-1263408?format=json
ficha: https://contratacionesabiertas.oece.gob.pe/proceso/ocds-dgv273-seacev3-1263408
UNSPSC ítems: 53102710
| campo | pe.json | API record | ¿cuadra? |
|---|---|---|---|
| ref | "LP-ABR-5-2026-GRELL/OC-1" | "LP-ABR-5-2026-GRELL/OC-1" | sí |
| entidad | "GOBIERNO REGIONAL DE LA LIBERTAD - GERENCIA REGIONAL DE EDUCACION LA LIBERTAD" | "GOBIERNO REGIONAL DE LA LIBERTAD - GERENCIA REGIONAL DE EDUCACION LA LIBERTAD" | sí |
| ciudad | "TRUJILLO" | "TRUJILLO" | sí |
| region | "LA LIBERTAD" | "LA LIBERTAD" | sí |
| objeto | "ADQUISICIÓN DE UNIFORMES PARA EL PERSONAL ADMINISTRATIVO NOMBRADO Y CONTRATADO COMPRENDIDO EN EL RÉGIMEN DEL D.L. 276 LEY DE BASES DE CARRERA ADMINISTRATIVA DE LA GERENCI" | "ADQUISICIÓN DE UNIFORMES PARA EL PERSONAL ADMINISTRATIVO NOMBRADO Y CONTRATADO COMPRENDIDO EN EL RÉGIMEN DEL D.L. 276 LEY DE BASES DE CARRERA ADMINISTRATIVA DE LA GERENCI" | sí |
| monto | null | null | sí |
| modalidad | "Licitación Pública Abreviada" | "Licitación Pública Abreviada" | sí |
| estado | "CONVOCADO" | "CONVOCADO" | sí |
| publicado | "2026-11-03" | "2026-11-03" | sí |
| duracion_dias | null | null | sí |
| proveedor | null | null | sí |
| monto_adjudicado | null | null | sí |

## Ficha pública en el portal (navegador)

`https://contratacionesabiertas.oece.gob.pe/proceso/ocds-dgv273-seacev3-1263408` muestra: nomenclatura LP-ABR-5-2026-GRELL/OC-1, Licitación Pública Abreviada, categoría Bienes, inicio de convocatoria 2026-11-03, consultas del 2026-11-04 al 2026-11-06, entidad GOBIERNO REGIONAL DE LA LIBERTAD - GERENCIA REGIONAL DE EDUCACION LA LIBERTAD (Trujillo, La Libertad) y la leyenda "Monto reservado". Coincide con `pe.json` (`monto: null`, `publicado: 2026-11-03`).

## Lo que la verificación no cubre

- `abierto` no se puede contrastar: ni la API ni la ficha publican la fecha límite de ofertas. En el proceso 1263408 las consultas cerraron el 2026-11-06 y sigue en CONVOCADO; se marca abierto por la regla de 30 días (estimación).
- `cierre` es `null` en todos los procesos por la misma razón.
- El proceso 1253292 tiene proveedor adjudicado y estado SUSPENDIDO: el dato es fiel a la fuente, pero la adjudicación no está firme.
- No se comparó contra el buscador de SEACE (`prod2.seace.gob.pe`), que exige captcha.
