# PREGUNTAS · dudas que aparecieron y la decisión que tomé para no frenar

Ninguna bloquea el concurso. Las dejo por si quieres cambiar algo después.

1. **Dónde publicar.** `surge`, Netlify y Vercel no tienen sesión en este equipo y los tres piden crear cuenta o iniciar sesión, así que no los usé. **Decisión:** GitHub Pages con la cuenta `blackbunnyglobal` que ya estaba autenticada en `gh`. Repositorio público `blackbunnyglobal/convoca`. No subí scripts con rutas locales ni tu correo.
2. **La cifra del hero (US$ 180.682 millones).** Es la suma real del snapshot, pero en Colombia, Chile y Perú es presupuesto publicado, no plata efectivamente contratada, y Colombia pesa el 71 %. **Decisión:** usarla tal cual, con la aclaración "presupuestos publicados" en el hero, en Datos y en el post. Si prefieres una cifra más conservadora, se puede cambiar a valor contratado.
3. **Ecuador.** Los servidores de SERCOP no respondieron. **Decisión:** "Próximamente", sin inventar. Se reintenta con el mismo script desde otra red.
4. **Chile no trae procesos abiertos** en su descarga abierta (solo cerrados y adjudicados). **Decisión:** se muestra igual, con aviso: sirve para ver quién compra y anticipar la próxima convocatoria.
5. **Colombia dejó de publicar el código UNSPSC en septiembre de 2026.** **Decisión:** los abiertos se clasificaron por palabras clave y la app los marca "Rubro estimado".
6. **Verificación contra el portal.** SECOP II pide reCAPTCHA y Mercado Público devuelve 403 a clientes automáticos; no los rodeé. **Decisión:** verificar contra el registro oficial completo por id y un segundo dataset oficial. México y Perú sí se compararon con la ficha pública.
7. **Bogotá, caso de prueba.** Los 5 procesos de aseo abiertos hoy en Bogotá son contratos gigantes (rojo para una pyme de 40 M al mes). **Decisión:** no maquillarlo. El radar pone arriba procesos abiertos de su tamaño en otras ciudades y agregué el filtro "Solo Bogotá".
8. **Videos de TikTok de referencia.** El sistema de permisos bloqueó la descarga con `yt-dlp` y no lo rodeé. Solo usé portadas y descripciones públicas para tomar el estilo (gancho enorme, tipografía grotesca, producto en mockup, cierre con logo). Si quieres que los analice a fondo, apruébame ese comando.
9. **"earshot".** No encontré ninguna herramienta con ese nombre en las skills instaladas ni en el CLI de HyperFrames. **Decisión:** medí el audio final con `ffmpeg`. Si es una herramienta que tienes en otra parte, dime dónde.
10. **Voz.** Google TTS vía `gTTS` (voz en español de México, sin llave). Suena a narrador automático; si tienes una voz propia o de HeyGen, se reemplazan 8 archivos WAV.
11. **Música.** Original, sintetizada por código (`build/musica.mjs`), sin samples ni licencias de terceros.
12. **Video vertical del demo.** Playwright graba en píxeles CSS, así que el demo vertical se grabó a 540×960 y se escaló a 1080×1920. El promo vertical sí es 1080×1920 nativo.
13. **Skills de HyperFrames.** `npx skills add heygen-com/hyperframes` falló dos veces por tiempo de espera del clon y funcionó al tercer intento (15 min de margen). Quedaron instaladas globalmente.
14. **Publicar el post en Skool y en X.** Requiere tus cuentas; no entro a ellas. Los textos están listos en `POST-SKOOL.md` con los enlaces reales.
