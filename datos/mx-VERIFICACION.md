# México (mx) — verificación

Selección: `node scripts/mx_verif_muestra.mjs mx.json` (generador congruencial con semilla 20261119 sobre los 640 procesos de la muestra; 3 al azar + 1 abierto al azar). Comparados el 2026-11-20 (UTC) contra la ficha pública de cada proceso en el portal Compras MX (su `url`), abierta en navegador.

## Hallazgo y corrección
En la primera pasada la unidad era el expediente. El proceso E-2026-00081911 salió con monto 2 926 891 y proveedor DAVLU DISTRIBUIDORA, pero la ficha del portal mostraba un solo contrato de $134 394.00 con ARYSANTA SA DE CV. Causa: ese expediente tiene dos procedimientos (T-174 y T-176), cada uno con su URL y su contrato. **Corregido**: la unidad pasó a ser el `Número de procedimiento`; se regeneró el JSON y ahora AA-50-GYR-050GYR002-T-174-2026 figura con 134 394 y ARYSANTA (cuadra). Tras la corrección se volvió a sortear con la misma semilla.

## Muestra final

| Campo | 1. AA-76-T43-917053958-N-23-2026 (mantenimiento) | 2. AM-38-91Q-03891Q002-N-40-2026 (alimentacion) | 3. LA-50-GYR-050GYR028-T-90-2026 (papeleria) | 4. LA-72-034-913010944-N-47-2026 (papeleria, abierto) |
|---|---|---|---|---|
| Código expediente (`ref`) | E-2026-00119441 cuadra | E-2026-00116014 cuadra | E-2026-00089975 cuadra | E-2026-00123974 cuadra |
| Entidad | SERVICIOS DE SALUD DE MORELOS cuadra | INSTITUTO DE ECOLOGIA, A.C. cuadra | INSTITUTO MEXICANO DEL SEGURO SOCIAL cuadra | SECRETARÍA DE EDUCACIÓN PÚBLICA (HGO) cuadra |
| Objeto | cuadra | "ADQUISICION DE CAFE" cuadra | cuadra (el CSV pierde las comillas tipográficas) | cuadra |
| Región | MORELOS cuadra (derivada del ramo estatal) | VERACRUZ cuadra (inferida por unidad compradora) | MÉXICO cuadra (inferida por unidad compradora) | HIDALGO cuadra |
| Publicado | 2026-11-06 cuadra | 2026-10-29 cuadra | 2026-08-04 cuadra | 2026-11-19 cuadra |
| Partida | 35102 cuadra | 22104 cuadra | 21101 cuadra | 21101 cuadra |
| Modalidad | JSON "ADJUDICACIÓN DIRECTA POR MONTOS MÁXIMOS POR EXCEPCIÓN"; portal "ADJUDICACIÓN DIRECTA" (el CSV de contratos es más detallado) | JSON `null` (vacío en el CSV); portal "CONTRATO ESPECÍFICO DERIVADO DE ACUERDO MARCO" — NO cuadra, se deja null | LICITACIÓN PÚBLICA cuadra | LICITACIÓN PÚBLICA cuadra |
| Estado | JSON "CONTRATO PUBLICADO"; portal ADJUDICADO (equivalente, ver DATOS) | ídem | ídem | VIGENTE cuadra |
| Proveedor | RODRIGO JAVIER BENITEZ RANGEL cuadra | COMERCIALIZADORA AGROPECUARIA DEL ESTADO DE OAXACA SA DE CV cuadra | COSMOPAPEL SA DE CV cuadra | sin contrato, null cuadra |
| Monto | 676 323.11 cuadra (sin impuestos) | 6 720.00 cuadra | 4 211 760.00 cuadra con el importe MÍNIMO; el portal muestra además máximo 10 528 434.00 (contrato abierto) | null (el portal no publica presupuesto) |
| Duración / cierre | 13/11 a 29/11/2026 = 16 días cuadra | 10/11 a 30/11/2026 = 20 días cuadra | 21/08 a 31/12/2026 = 132 días cuadra | apertura 30/11/2026 12:00 = `cierre` 2026-11-30 cuadra |

Resultado: 4 de 4 cuadran en identificadores, entidad, objeto, región, fecha, proveedor y monto tras la corrección de la unidad. Diferencias que quedan documentadas: modalidad vacía en el CSV para contratos derivados de acuerdo marco, etiqueta de estado de los procesos que solo están en Contratos, y contratos abiertos reportados por su importe mínimo.
