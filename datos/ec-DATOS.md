# Ecuador (ec) — sin datos abiertos alcanzables

Estado: **sin datos abiertos** desde este equipo. No se generó `ec.json` ni verificación.

## Qué se probó (2026-11-20, 02:45 a 02:56 UTC, `curl -A curl`)

| Intento | Resultado |
|---|---|
| `https://datosabiertos.compraspublicas.gob.ec/PLATAFORMA/api/search_ocds?year=2026&search=limpieza&page=1` | timeout de conexión (40 s y 60 s, tres intentos) |
| `https://datosabiertos.compraspublicas.gob.ec/PLATAFORMA/datos-abiertos` (https y http) | timeout de conexión |
| Mismo host forzando cada IP del DNS (157.100.62.6 y 190.152.44.111) | sin conexión |
| `https://www.compraspublicas.gob.ec/` y `https://portal.compraspublicas.gob.ec/` | timeout de conexión |
| Navegador integrado hacia `datosabiertos.compraspublicas.gob.ec` | navegación denegada/fallida |
| `https://www.datosabiertos.gob.ec/dataset/?q=sercop` | HTTP 403 |
| Espejo OCDS de Open Contracting Partnership, `https://data.open-contracting.org/en/publication/42` | responde, pero no es fuente oficial, su última recolección es del 2026-01-12 y solo ofrece archivos anuales hasta 2024: no cubre el periodo 2025-11-19 a 2026-11-19. Descartado. |

El DNS resuelve, pero los servidores de SERCOP no aceptan conexiones desde esta red (posible bloqueo geográfico o caída). El resto de internet respondía con normalidad en el mismo momento.

## Para reintentar
Desde una red que alcance SERCOP (p. ej. una IP de Ecuador): la búsqueda `search_ocds` y `record?ocid=` indicadas arriba, o las descargas masivas por año/mes de la página de datos abiertos. Moneda USD (tasa 1), clasificación por palabras clave (Ecuador usa CPC, no UNSPSC).
