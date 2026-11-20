# DATOS · de dónde sale cada número de CONVOCA

Snapshot real descargado el **19 de noviembre de 2026** (al construir la app). La app no consulta nada en vivo: todo va embebido como `DATA` dentro de `index.html`. No se inventó ningún proceso, entidad ni monto. Lo estimado va marcado como estimado.

Cada país lo descargó y limpió un subagente distinto, en paralelo. El detalle fino (consultas exactas, reglas de limpieza, exclusiones) está en `datos/<país>-DATOS.md` y la verificación en `datos/<país>-VERIFICACION.md`.

## Resumen

| País | Fuente oficial abierta (sin login ni llave) | Endpoint | Periodo real | Procesos | Monto | Tasa USD | US$ |
|---|---|---|---|---|---|---|---|
| Colombia | SECOP II · Procesos de Contratación (Colombia Compra Eficiente) | `https://www.datos.gov.co/resource/p6dx-8zbt.json` (Socrata) + Contratos Electrónicos `jbjy-vk9h` + DIVIPOLA (coordenadas) + TRM `32sa-8pi3` | 2025-11-19 a 2026-11-19 | 1.755.575 | 427,27 billones COP | 3.326,96 (TRM 19-nov-2026) | 128.428 M |
| Chile | ChileCompra · descarga masiva de licitaciones | `https://transparenciachc.blob.core.windows.net/lic-da/<AAAA>-<M>.zip` (13 archivos mensuales) | 2025-11-19 a 2026-11-13 | 91.206 | 16,32 billones CLP | 1.002,07 (dólar observado, mindicador.cl) | 16.282 M |
| Perú | OECE · Contrataciones Abiertas (OCDS, SEACE v3) | `https://contratacionesabiertas.oece.gob.pe` (13 descargas mensuales JSON) | 2025-11-19 a 2026-11-19 | 72.685 | S/ 87.079 M | 3,433 (BCRP, TC SBS venta 18-nov-2026) | 25.365 M |
| México | Compras MX · Datos Abiertos (expedientes y contratos 2025-2026) | `https://comprasmx.buengobierno.gob.mx/datos-abiertos` | 2025-11-19 a 2026-11-19 | 31.211 | 201.817 M MXN | 19,027 (FIX Banxico 19-nov-2026) | 10.607 M |
| Ecuador | SERCOP · datos abiertos OCDS | `datosabiertos.compraspublicas.gob.ec` | — | — | — | — | **Próximamente** |

**Total del snapshot: 1.950.677 procesos y US$ 180.682 millones** (es la cifra del hero).

Ecuador: los servidores de SERCOP no respondieron desde la red de trabajo en tres rondas de intentos (timeout). No se usó ningún espejo no oficial ni se inventó nada; queda como "próximamente" (`datos/ec-DATOS.md`).

## Qué mide el monto (léelo antes de citar la cifra)

- **Colombia, Chile y Perú:** suma del **presupuesto publicado** de cada proceso (precio base / monto estimado / valor referencial), no del valor finalmente contratado. Incluye todas las modalidades (en Colombia, también régimen especial y contratación directa, que son la mayoría de los procesos).
- **México:** la fuente no publica presupuesto; el monto es el **importe contratado** (sin impuestos) y solo cubre la plataforma federal Compras MX.
- **Chile:** solo licitaciones (no incluye Compra Ágil, convenio marco ni trato directo).
- **Perú:** solo procedimientos de selección (no incluye compras menores a 8 UIT ni catálogos de Perú Compras); 5.404 procesos tienen monto reservado y no suman.

## Limpieza y exclusiones principales

| País | Qué se excluyó o corrigió |
|---|---|
| Colombia | Un proceso = un `id_del_portafolio`: se descontaron 31.388 filas de fases repetidas (67,8 billones COP duplicados). Fuera: estados Cancelado y Borrador, "Solicitud de información a proveedores", subastas de prueba y 4 registros con precio base ≥ 10 billones COP (outliers). En los rubros se excluye la contratación directa de servicios personales. |
| Chile | 119 licitaciones con montos absurdos (sumaban 1,5 × 10^15 CLP, 92 veces el total válido), 5.300 fuera de periodo y 849 filas mal formadas. |
| Perú | 7.003 reconvocatorias (el mismo procedimiento reemitido con otro id) y 3.753 registros fuera de periodo. |
| México | 570 proyectos de convocatoria, 151 contratos no formalizados, 115 contratos en otra moneda (no suman), duplicados y filas sin fecha. |
| Todos | Antes de publicar se quitaron 6 correos personales que venían en la fuente escritos como "nombre de proveedor". |

## Clasificación por rubro

- **UNSPSC** (Colombia, Chile, Perú): cada proceso va a un solo rubro por su código principal.
- **Colombia, advertencia:** desde mediados de septiembre de 2026 SECOP II publica el código como `UNSPECIFIED`. Esos procesos se clasificaron con el UNSPSC de su contrato (7.334) o, si aún no tienen contrato, por palabras clave (20.239, **estimado**). Todos los procesos abiertos hoy caen en este último grupo y la app los marca "Rubro estimado".
- **México:** por partida presupuestaria del COG con un mapeo propio a rubros (**estimado**).

## Muestra embebida

Por país y rubro: totales, mediana, percentiles 25 y 75, top 25 entidades compradoras (con coordenadas), top 15 proveedores adjudicados, modalidades, distribución por región y hasta 80 procesos de ejemplo (primero los abiertos, luego los adjudicados más recientes, con diversidad regional y al menos 12 de la capital). 2.560 procesos de ejemplo en total, todos con enlace al portal oficial.

Coordenadas: Colombia a nivel municipio (DIVIPOLA oficial). Chile, Perú y México a nivel de capital regional/departamental/estatal (marcadas `geo: "region"`), por eso varias columnas de luz salen juntas.

## Verificación (3 procesos al azar por país, semilla 20261119)

| País | Contra qué se comparó | Resultado |
|---|---|---|
| Colombia | El portal público pide reCAPTCHA (no se resolvió). Se comparó contra el registro completo por id y contra el dataset independiente de Contratos Electrónicos. | Los 3 cuadran en todos los campos. `total.n` coincide con un `count(distinct)` hecho aparte en el servidor. |
| Chile | El portal respondió 403 a clientes automáticos. Relectura independiente de los 3 registros en el CSV crudo. | 36 de 36 campos cuadran. |
| Perú | Registro completo por id en la API OCDS y una ficha abierta en el navegador. | Los 3 cuadran en 12 campos. |
| México | Ficha pública de cada procedimiento en el portal. | **Se encontró y corrigió un error:** al agrupar por expediente un proceso sumaba el contrato de otro (2.926.891 frente a 134.394 en el portal). Se cambió la unidad a número de procedimiento y se regeneró todo. |

## Lo que es estimado en la app

- El **índice de oportunidad** (0 a 100) y el **semáforo «¿Me alcanza?»**: fórmulas de CONVOCA para ordenar, no probabilidades.
- La **caja a financiar** y la **garantía típica**: reglas generales por país; cada pliego puede pedir otra cosa.
- **«Qué te piden»**: estructura típica del tipo de proceso resumida por Fable, no el pliego de un proceso concreto.
- El **rubro** de los procesos marcados "Rubro estimado" y el estado **abierto** en Perú (la fuente no publica la fecha límite).

## Cómo reproducirlo

Los scripts de descarga y limpieza están en `datos/scripts/` (uno o varios por país), el armado del HTML en `build/build.mjs` y las pruebas en `build/test.mjs`. Esas dos carpetas viven en la copia local del proyecto; no se subieron al repositorio público porque contienen rutas del equipo.
