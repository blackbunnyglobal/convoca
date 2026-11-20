# Brief de datos CONVOCA (para subagentes)

CONVOCA es una app de un solo HTML que le muestra a una pyme de LATAM qué compras públicas calzan con lo que vende.
Tu trabajo: descargar un snapshot REAL de datos abiertos oficiales de UN país, limpiarlo y dejar un JSON con el esquema de abajo.
Hoy es 2026-11-19. Periodo objetivo: procesos publicados entre 2025-11-19 y 2026-11-19 (últimos 12 meses).

## Reglas duras
- CERO inventos. Nunca inventes procesos, entidades, montos, coordenadas ni proveedores. Si un campo no existe en la fuente, va `null`.
- Solo fuentes oficiales abiertas, SIN login, SIN registro, SIN crear cuentas, SIN llaves que haya que pedir. Si la fuente lo exige, para y repórtalo.
- No mandes datos personales del usuario a ningún servicio. User-Agent genérico (`curl`).
- Si no alcanzas 12 meses, usa el periodo que sí alcances y decláralo en `periodo` (NO anualices ni extrapoles).
- Valores absurdos (errores de digitación tipo 10^14): detéctalos mirando el top 20 de montos, exclúyelos con una regla explícita y documenta la regla y cuántos registros salieron.
- Trabaja rápido: objetivo ~12 minutos. Prefiere agregaciones del lado del servidor (SoQL `$select/$group/$where`) antes que bajar todo.
- Scripts en `convoca/datos/scripts/<cc>_*.mjs|sh|py` (reproducibles). Crudos temporales en el scratchpad, no en el proyecto.
- No toques nada fuera de `convoca/datos/`.

## Rubros (ids fijos) y clasificación UNSPSC sugerida (prefijos de código)
- `alimentacion`: 50 (alimentos y bebidas), 9010 (restaurantes y catering)
- `aseo`: 7611 (servicios de aseo y limpieza), 4713 (suministros de aseo y limpieza)
- `dotacion`: 53 (ropa, calzado, uniformes), 4618 (seguridad y protección personal)
- `mantenimiento`: 7210 y 7215 (mantenimiento y reparación de instalaciones), 31 (ferretería), 27 (herramientas), 30 (materiales de construcción)
- `papeleria`: 4412 (suministros de oficina), 1411 (productos de papel), 4410 (máquinas de oficina)
- `eventos`: 8014 16 y 8014 19 (eventos, ferias), 9011 16 (salones y facilidades para encuentros), 9015 (entretenimiento), 8011 16? NO; usa solo los de eventos/logística de eventos
- `software`: 4323 (software), 8111 (servicios informáticos), 8116 (entrega de servicios de TI), 4321 (equipo informático)
- `transporte`: 7811 (transporte de pasajeros), 7810 (transporte de carga y correo)
Si el país no usa UNSPSC, clasifica por palabras clave en el objeto y declara `"clasificacion":"palabras clave (estimado)"`.

## Salida 1: `convoca/datos/<cc>.json` (cc = co, cl, pe, mx, ec)
```json
{
  "pais": "CO", "nombre": "Colombia", "moneda": "COP",
  "usd_rate": 0, "usd_rate_fuente": "fuente oficial y fecha de la tasa (moneda local por 1 USD)",
  "periodo": {"desde": "2025-11-19", "hasta": "2026-11-19", "nota": ""},
  "fuente": {"nombre": "", "portal": "", "endpoint": "", "descargado": "ISO-8601"},
  "clasificacion": "UNSPSC",
  "total": {"n": 0, "monto": 0, "nota": "cómo se calculó y qué se excluyó. TODO el país y TODOS los rubros, no solo los 8"},
  "entidades_top": [{"nombre": "", "ciudad": "", "region": "", "lat": 0, "lon": 0, "n": 0, "monto": 0}],
  "regiones": ["lista de regiones/departamentos con procesos, orden alfabético"],
  "rubros": {
    "aseo": {
      "n": 0, "monto": 0, "mediana": 0, "p25": 0, "p75": 0, "abiertos": 0,
      "entidades": [{"nombre": "", "ciudad": "", "region": "", "lat": 0, "lon": 0, "n": 0, "monto": 0, "top_proveedor": null, "top_proveedor_share": null}],
      "proveedores": [{"nombre": "", "n": 0, "monto": 0}],
      "modalidades": [{"nombre": "", "n": 0, "monto": 0}],
      "por_region": [{"region": "", "n": 0, "monto": 0}],
      "procesos": [{"id": "", "ref": "", "entidad": "", "ciudad": "", "region": "", "objeto": "", "monto": 0, "modalidad": "", "estado": "", "abierto": false, "publicado": "YYYY-MM-DD", "cierre": null, "duracion_dias": null, "proveedor": null, "monto_adjudicado": null, "url": ""}]
    }
  }
}
```
Detalles:
- Montos en moneda local, números (no strings). `monto` de un proceso = presupuesto/precio base publicado.
- `entidades_top`: top 40 del país por monto total (todos los rubros), con lat/lon REALES del municipio/ciudad (de un dataset oficial de coordenadas; si no hay municipio, centroide de la región y márcalo con `"geo":"region"`). Sin coordenada real → `lat:null`.
- Por rubro: `entidades` top 25 por monto; `proveedores` top 15 adjudicados por monto; `top_proveedor_share` = fracción (0–1) del monto adjudicado de esa entidad en ese rubro que se llevó su proveedor nº 1 (null si no hay adjudicados).
- `procesos`: hasta 80 por rubro. Primero TODOS los abiertos hoy (recibiendo ofertas, cierre >= 2026-11-19) hasta 45, luego los adjudicados más recientes. Garantiza diversidad: al menos 12 de la capital del país si existen y al menos 2 de cada una de las 8 regiones con más procesos. `objeto` recortado a 170 caracteres, texto limpio (sin saltos de línea). `url` = enlace público oficial al proceso.
- `abiertos` = conteo real de procesos abiertos hoy en el rubro (no solo los de la muestra).
- Nombres de entidades y proveedores tal como vienen (solo trim y colapsar espacios). No incluyas proveedores que sean personas naturales con documento: solo el nombre.
- JSON válido, minificado, UTF-8. Valídalo con `node -e "JSON.parse(require('fs').readFileSync(...))"`.

## Salida 2: `convoca/datos/<cc>-DATOS.md`
Tabla con cada fuente, endpoint/URL exacta, consulta usada, fecha-hora de descarga y nº de registros; reglas de limpieza; exclusiones; limitaciones.

## Salida 3: verificación — `convoca/datos/<cc>-VERIFICACION.md`
Elige 3 procesos AL AZAR de tu muestra (usa `Math.random`/`shuf`, deja la semilla o el comando) y compáralos contra el portal público oficial (la `url` del proceso) o, si el portal bloquea bots/captcha, contra una segunda consulta independiente de la fuente oficial (otro dataset o el registro completo por id). Anota campo por campo qué cuadró y qué no, y corrige el JSON si algo no cuadra.

## Tu respuesta final (breve)
Estado (ok / parcial / sin datos abiertos), periodo real, `total.n` y `total.monto`, n por rubro, tasa USD usada, qué excluiste, resultado de la verificación y cualquier advertencia que la app deba mostrar.
