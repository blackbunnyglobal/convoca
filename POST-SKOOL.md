# POST PARA SKOOL (copiar desde el título)

**TÍTULO:**

[FABLE] CONVOCA: el Estado te está convocando y tu pyme no se enteró

---

🏛️ **EL PROYECTO**

Los gobiernos de LATAM compran de todo: comida, aseo, uniformes, mantenimiento, software, eventos, transporte. Las pymes podrían venderles, pero no se enteran. Los portales son un laberinto, los pliegos son PDFs eternos y terminan ganando los mismos proveedores de siempre.

CONVOCA lo da vuelta. La pyme dice qué vende, dónde está y cuánto factura, y en 4 toques ve:

- **Su radar:** procesos reales que calzan con lo que vende, ordenados por calce, plata y plazo.
- **El mapa del dinero:** qué entidades compran lo suyo, cuánto gastan y a quién le compran hoy.
- **¿Me alcanza?:** un semáforo verde, amarillo o rojo según el tamaño de su empresa.
- **El pliego en humano:** qué le van a pedir, sin jerga.
- **Un plan de 14 días** para presentarse, con botón para copiar el resumen.

La cifra del snapshot: **US$ 180.682 millones en presupuestos de compra pública publicados en 12 meses**, en 1.950.677 procesos reales de 4 países (Colombia, Chile, Perú y México). Todo sale de datos abiertos oficiales, sin inventar un solo proceso.

🔗 **EL RESULTADO**

- 👉 **App:** https://blackbunnyglobal.github.io/convoca/
- 🎥 **Video (34 s, con música y voz):** https://blackbunnyglobal.github.io/convoca/video/
- 📂 **Datos, verificación y código:** https://github.com/blackbunnyglobal/convoca

**Prueba esto:** entra, toca "Encuentra tu oportunidad" y elige Colombia → Aseo y limpieza → Bogotá → $ 40 M al mes. (O toca "Demo" arriba y míralo solo.)

Lo que le sale a esa pyme, con datos reales de SECOP II:

- En 12 meses el Estado abrió **10.758 procesos de aseo por $ 2,1 billones de pesos** (unos US$ 631 millones). La mitad vale menos de **$ 7,6 millones**: sí hay contratos de su tamaño.
- De 80 procesos de muestra, **45 le alcanzan (verde), 12 quedan justos (amarillo) y 23 le quedan grandes (rojo)**.
- El dato incómodo: los 5 procesos de aseo abiertos ese día en Bogotá eran contratos gigantes, todos en rojo. Lo de su tamaño en Bogotá son mínimas cuantías que salen y cierran en días. Por eso el radar le pone arriba procesos abiertos de su tamaño en otras ciudades (el nº 1: suministro de aseo en Tauramena por $ 150 M, índice 78/100) y le deja el filtro "Solo Bogotá".

🛠️ **CÓMO LO HICE**

Con Claude Fable 5.2 en Claude Code, en una sola sesión de más o menos una hora y un solo prompt largo (más tres mensajes cortos míos a mitad de camino). El costo no lo medí.

**Fases:**

1. **Plan y marca.** Logo propio (una C hecha de ondas de señal: el Estado llamando), paleta azul noche, violeta y esmeralda, nombres de cada sección.
2. **Datos en paralelo.** 4 subagentes, uno por país, bajando datos abiertos oficiales mientras se construía la app: SECOP II (datos.gov.co), Mercado Público (ChileCompra), SEACE (OECE, formato OCDS) y Compras MX. Ecuador quedó "próximamente" porque SERCOP no respondió, y no se inventó nada.
3. **La app.** Un solo archivo HTML con Liquid Glass, mapa 3D de LATAM en puntos (three.js) con una columna de luz por cada entidad compradora, y respaldo 2D si el 3D falla.
4. **Pruebas y autocrítica.** 10 pruebas con Node sobre las funciones del puntaje y del semáforo, capturas a 375 y 1440 px con Playwright y dos rondas de "qué se ve barato" con sus correcciones.
5. **Video.** Modo demo dentro de la app, grabado con Playwright, y un promo en HyperFrames con música sintetizada por código y voz de Google TTS.
6. **Publicación** en GitHub Pages.

**Qué se corrigió en la verificación** (3 procesos al azar por país contra la fuente oficial):

- **México:** un proceso mostraba $ 2.926.891 y el portal decía $ 134.394. Estaba sumando el contrato de otro procedimiento del mismo expediente. Se cambió la unidad de conteo y se regeneró todo.
- **Colombia:** el mismo proceso aparece una vez por fase y repite el presupuesto. Sin deduplicar, el total se inflaba en 67,8 billones de pesos.
- **Chile:** 119 licitaciones con montos imposibles (digitados en UF como si fueran pesos) sumaban 92 veces el total real. Fuera.
- **Perú:** 7.003 reconvocatorias duplicadas. Fuera.
- **La app:** una prueba detectó que escribía "$ 2,10 billones"; y antes de publicar se quitaron 6 correos personales que venían en la fuente como nombre de proveedor.

**Lo que no es perfecto (y la app lo dice):** el monto es presupuesto publicado, no plata ya gastada (salvo México, que es lo contratado). Chile no publica procesos abiertos en su descarga. Colombia dejó de publicar el código de categoría en septiembre, así que los abiertos van marcados "Rubro estimado". El índice y el semáforo son estimaciones para ordenar, no probabilidades.

**El prompt clave, resumido:**

```
Eres el equipo completo de producto: diseñador senior de Apple, front-end,
analista de datos públicos y editor. Construye CONVOCA: la pyme dice qué
vende y dónde está, y ve su radar de compras públicas.
Trabaja por fases SIN detenerte. Las dudas van a PREGUNTAS.md con la
decisión que tomaste.
DATOS REALES: usa subagentes en paralelo por país. Descarga datos abiertos
oficiales sin login (SECOP II, Mercado Público, SEACE, Compras MX) de los
últimos 12 meses. Anota cada fuente, endpoint, fecha y nº de registros.
Elige 3 procesos al azar por país, compáralos con la fuente y corrige.
Cero inventos: lo estimado va marcado como estimado.
DISEÑO: producto Apple 2026 con Liquid Glass. Fondo degradado vivo, paneles
de vidrio con brillo que sigue el puntero, mapa 3D de LATAM en puntos con
columnas de luz por entidad, tipografía SF, íconos SVG de trazo fino.
Saca capturas a 375 y 1440 px, lista 5 cosas que se vean baratas,
corrígelas y repite una vez.
TÉCNICA: un solo index.html, datos embebidos, cero fetch, cero errores de
consola. Funciones puras para el puntaje y el semáforo, probadas con Node.
Caso real: pyme de aseo en Bogotá que factura 40 M COP al mes.
VIDEO: modo demo de ~40 s dentro de la app, grabado con Playwright, y un
promo con música y voz.
LINKS: publica la app y pon la URL real en el post. Nunca entregues algo
que no hayas abierto y probado.
```

**¿Salió de una pasada?** El prompt fue uno, pero adentro hubo iteración de verdad: dos rondas de diseño, un mapa que no aparecía por un error en el primer cuadro de animación, una grabación vertical que salió a un cuarto de pantalla y hubo que repetir, y un instalador que falló dos veces antes de funcionar. Lo que cambió es que las correcciones las encontró y las hizo el modelo, no yo.

🐸 **LA MORALEJA**

La plata pública siempre estuvo publicada; lo que faltaba era alguien que la leyera por el que no tiene tiempo de leerla.

#FableImperial

---

# POST PARA X (273 caracteres)

Los gobiernos de LATAM publican miles de millones en compras y las pymes no se enteran.

Hice CONVOCA con Claude Fable 5.2: dices qué vendes y te muestra tu radar de procesos reales, quién compra y si te alcanza.

https://blackbunnyglobal.github.io/convoca/

#FableImperial

---

# CAPTURAS

- `capturas/hero.png`
- `capturas/radar.png`
- `capturas/mapa-del-dinero.png`
- (extra, móvil 375 px) `capturas/hero-movil-375.png`, `capturas/radar-movil-375.png`, `capturas/mapa-del-dinero-movil-375.png`

# VIDEOS (para subir directo a Skool o X si prefieres archivo en vez de link)

- `video/convoca-promo-vertical-1080x1920.mp4` (34 s, música y voz, formato Reels/TikTok)
- `video/convoca-promo-horizontal-1920x1080.mp4` (34 s, música y voz)
- `video/demo-1280x720.mp4` y `video/demo-1080x1920.mp4` (recorrido completo del modo demo, sin audio)
