# Terremotos de Chile desde 1900

Visualización interactiva y sonora de los terremotos de magnitud 7 o más ocurridos en Chile entre 1900 y 2026.
Proyecto del curso Visualización de Información (IIC2026).

## Qué muestra

- **Horizontal:** año. **Vertical:** latitud (norte arriba, con ciudades de referencia). 
- **Tamaño:** magnitud. **Color:** profundidad (superficial, intermedio, profundo).
- **Interacción:** filtro de magnitud mínima, rango de años (sirve de zoom), filtro por profundidad, detalle al pasar el cursor y sonido al hacer clic.
- **Sonido:** botón "Escuchar la historia" reproduce los terremotos en orden cronológico (126 años en ~45 segundos).
  - Magnitud → tono (más grave = más fuerte) y duración (más largo = más fuerte).
  - Profundidad → timbre (áspero = superficial, liso = intermedio, sordo y lejano = profundo).

## Datos

- Fuente: catálogo de terremotos del USGS (Servicio Geológico de EE. UU.), consulta por API: magnitud ≥ 7, años 1900–2026, latitud −56 a −17 y longitud −76 a −66.
- Procesamiento: se descartaron los eventos ubicados en Perú, Bolivia y Argentina (quedaron 66 en Chile), se dejaron solo fecha, latitud, longitud, profundidad, magnitud y lugar, y se acortaron los nombres de lugar. El resultado está en `data/terremotos_chile.csv`.
- `data.js` es el mismo CSV convertido a JavaScript para que la página funcione sin servidor.
- Limitación: los catálogos antiguos tienen más incertidumbre en ubicación y profundidad que los recientes.

## Cómo verlo en tu computador

```bash
cd terremotos-chile
python3 -m http.server 8000
```

Abre http://localhost:8000 en el navegador. El sonido parte solo después de un clic (regla del navegador).

## Cómo publicarlo en GitHub Pages

1. En github.com: **New repository**, nombre `terremotos-chile`, público, sin README (ya tienes uno).
2. En la terminal, dentro de la carpeta del proyecto:

```bash
git init
git add .
git commit -m "V1: visualización interactiva y sonora de terremotos de Chile"
git tag v1
git branch -M main
git remote add origin https://github.com/TU-USUARIO/terremotos-chile.git
git push -u origin main --tags
```

3. En el repositorio: **Settings → Pages → Build and deployment → Deploy from a branch → main / (root) → Save**.
4. Espera 1–2 minutos. El link queda en `https://TU-USUARIO.github.io/terremotos-chile/`.

## Registro de versiones

### V1 — la idea completa

- **Mensaje:** Chile ha sufrido 66 terremotos de magnitud 7 o más desde 1900; el de 1960 es el mayor registrado en el mundo y la mayoría se concentra entre Arica y Concepción.
- **Decisiones iniciales:** puntos con año en horizontal y latitud en vertical (la latitud hace que el gráfico se lea como el país); tamaño por magnitud; color por profundidad; etiquetas solo para M ≥ 8,5; filtros y rango de años; detalle bajo demanda; sonificación con tono y duración por magnitud y timbre por profundidad.
- **Problemas conocidos para discutir en la revisión:**
  - Los círculos de 1960 se superponen y esconden eventos.
  - El tamaño es lineal en el radio, pero la energía crece mucho más rápido que la magnitud.
  - No hay forma de comparar zonas (norte, centro, sur) de un vistazo.
  - Con la reproducción cronológica, los eventos del mismo día suenan juntos y se confunden.
