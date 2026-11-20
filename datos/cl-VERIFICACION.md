# CL · verificación de 3 procesos al azar

## Selección
Sobre los 640 procesos de la muestra de `cl.json` (8 rubros × 80), con `node` y un generador mulberry32 de semilla fija **20261119**:

```js
let a=20261119;const rnd=()=>{a|=0;a=a+0x6D2B79F5|0;let t=Math.imul(a^a>>>15,1|a);t=t+Math.imul(t^t>>>7,61|t)^t;return((t^t>>>14)>>>0)/4294967296};
const all=Object.entries(d.rubros).flatMap(([k,v])=>v.procesos.map(p=>({rubro:k,...p})));
const pick=[];while(pick.length<3){const p=all[Math.floor(rnd()*all.length)];if(!pick.includes(p))pick.push(p)}
```
Salieron: `1079967-24-LE26` (papeleria), `5194-3-LE26` (dotacion), `5690-16-L126` (dotacion).

## Contra qué se comparó
- **Portal público: no fue posible.** Las tres URL `DetailsAcquisition.aspx?idlicitacion=…` respondieron **HTTP 403** (134 bytes) a `curl` con User-Agent genérico. No se intentó evadir el bloqueo.
- **Segundo dataset oficial (órdenes de compra, `oc-da/2026-11.zip` y `2026-10.zip`): no fue posible.** La descarga no terminó en el tiempo disponible (5 MB de 70 MB en 170 s); no hay resultado.
- **Lo que sí se hizo:** relectura del registro completo por id en el CSV crudo oficial con un parser distinto (`scripts/cl_3_verificar.pl`, Perl, sin compartir código con el pipeline Python). Esto comprueba que el pipeline no alteró ni mezcló datos; **no** comprueba que el dato abierto coincida con lo que muestra hoy el portal.

## Resultado campo por campo

| Campo | 1079967-24-LE26 | 5194-3-LE26 | 5690-16-L126 |
|---|---|---|---|
| entidad | DIRECCION DE LOGISTICA DE CARABINEROS · cuadra | DIRECCION GENERAL DE GENDARMERIA DE CHIL · cuadra | SERVICIO AGRICOLA Y GANADERO · cuadra |
| objeto | ADQUISICIÓN DE TÓNER PARA LA ZONA TARAPACA · cuadra | Adquisicion de Equipos Deportivos · cuadra | Adquisición de calzado de seguridad (39 pares de botines) · cuadra |
| ciudad / region | Iquique / Región de Tarapacá · cuadra | Talca / Región Metropolitana de Santiago · cuadra con el crudo (ver nota) | Pica / Región de Tarapacá · cuadra |
| modalidad | LE, entre 100 y 1000 UTM · cuadra | LE, entre 100 y 1000 UTM · cuadra | L1, menor a 100 UTM · cuadra |
| estado | Adjudicada · cuadra | Adjudicada · cuadra | Adjudicada · cuadra |
| monto (CLP) | 20.401.587 · cuadra | 25.000.000 (crudo `2,5e+07`) · cuadra | 4.290.000 · cuadra |
| publicado | 2026-09-10 · cuadra | 2026-08-03 · cuadra | 2026-10-19 · cuadra |
| cierre | 2026-09-22 · cuadra | 2026-08-31 · cuadra | 2026-10-27 · cuadra |
| duracion_dias | null (crudo 0, unidad 1) · cuadra | null (crudo 0, unidad 1) · cuadra | null (crudo 0, unidad 0) · cuadra |
| proveedor | IMPORTADORA INTERNATIONAL TECHNICS GRAPHICS LIMITADA · cuadra | ASIS SPA (12.807.000 de 14.807.000; el resto, COMERCIAL 2T LIMITADA) · cuadra | COMERCIALIZADORA DE ART DE PROTECCION Y SEGURIDAD INDUSTRIAL MANQUEHUE · cuadra |
| monto_adjudicado | 14.514.800 (1 línea) · cuadra | 14.807.000 (4 líneas) · cuadra | 2.791.230 (1 línea) · cuadra |
| rubro (UNSPSC) | 44103103, prefijo 4410 → papeleria · cuadra | 53102301 ×3 y 53102401, prefijo 53 → dotacion · cuadra | 53111601, prefijo 53 → dotacion · cuadra |

**36 de 36 campos cuadran con el crudo; no hubo que corregir el JSON.**

Nota sobre `5194-3-LE26`: la fuente trae comuna "Talca" con región "Región Metropolitana de Santiago". Es una inconsistencia de origen (afecta al 1,6 % de las licitaciones) y se dejó como viene; quedó documentada en `cl-DATOS.md`.

## Correcciones hechas durante la revisión (antes de la muestra)
- Se añadió la regla de tramo legal tras ver en el top 20 una licitación L1 por 7,0 × 10^10 CLP que el tope de 10^11 no atrapaba.
- La muestra ordenaba por `FechaAdjudicacion` y había fechas futuras (hasta 2029-05-13); ahora esas caen a la fecha de publicación.
- `ciudad` y `region` de una entidad se elegían por separado y podían no corresponder entre sí; ahora la comuna se elige dentro de la región más frecuente.
