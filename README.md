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
- Procesamiento: se descartaron los eventos ubicados en Perú, Bolivia y Argentina (quedaron 66 en Chile), se dejaron solo fecha, hora (UTC), latitud, longitud, profundidad, magnitud y lugar, y se acortaron los nombres de lugar. El resultado está en `data/terremotos_chile.csv`.
- La hora UTC se agregó después (15 de octubre de 2026) con una nueva consulta al USGS: los 66 eventos coinciden exactamente por fecha, coordenadas y magnitud. Sirve para ordenar los eventos del mismo día (por ejemplo, el 22 de mayo de 1960: Angol 10:30, Traiguén 18:56, Valdivia 19:11).
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

### V2 — primera iteración de diseño

- **Qué cambió:**
  - El tamaño ahora sigue una regla explícita: el área es proporcional a (energía)^(1/3), de modo que cada +1 de magnitud multiplica la energía por ~32 y el área por ~3,2 (antes el radio era lineal en la magnitud).
  - Se agregó una leyenda de tamaños (M7, M8, M9) con el círculo de referencia.
  - Los círculos pasaron a ser más transparentes y con borde del color de su categoría de profundidad, para que se vean los eventos que se superponen (por ejemplo, los de 1960).
- **Se mantiene igual:** datos, ejes, colores, filtros, rango de años y sonificación.

### Ajuste de sonido (posterior al tag v2)

- **Qué cambió:** el tono pasó de 180–45 Hz a 260–100 Hz según la magnitud (sigue siendo más grave = más fuerte), se agregó un armónico una octava arriba y se subió un poco el volumen base.
- **Motivo:** en una prueba de escucha en un notebook casi no se oía el sonido de los terremotos grandes (45 Hz está bajo lo que reproducen los parlantes pequeños).

### Ajuste de sonido 2 (posterior al tag v2)

- **Qué cambió:** el contraste entre magnitudes es mayor (tono 320–90 Hz, duración 0,4–4,15 s, y retumbo, brillo y temblor que crecen con la magnitud). La latitud ahora se oye en el estéreo (norte a la izquierda, sur a la derecha). En la reproducción cronológica los eventos casi simultáneos se escalonan 0,5 s, y se agregó un compresor para que no se tapen ni saturen.
- **Motivo:** al escuchar, los terremotos solapados no se distinguían y era difícil percibir la diferencia entre magnitudes grandes y pequeñas.

### Separación de círculos solapados (posterior al tag v2)

- **Qué cambió:** los círculos que se tapan se desplazan un poco en horizontal respetando el orden en el tiempo (el que ocurrió antes queda a la izquierda), usando la hora UTC. Un punto gris marca la posición exacta y el detalle muestra fecha y hora. Al acotar el rango de años el desplazamiento se reduce solo.
- **Motivo:** los eventos de 1960 y otros del mismo día se superponían y no se podían distinguir ni ordenar.
- **Limitación:** la posición horizontal de esos círculos es aproximada; la fecha y la hora exactas están en el panel de detalle.

### Ajuste de sonido 3, escala y separación (posterior al tag v2)

- **Sonido:** el tono ahora es siempre el mismo (220 Hz) y la duración y la sonoridad son fijas; la magnitud solo cambia el **timbre** (M7 agudo, brillante y delgado; M9,5 grave, oscuro y con retumbo y temblor). La profundidad se oye en el ataque y en la aspereza (ruido). La latitud se oye en el estéreo (norte a la izquierda, sur a la derecha).
- **Tamaño:** radios de ~5 px (M7) a ~25 px (M9,5); cada +1 de magnitud multiplica el área por ~3,6.
- **Separación:** los círculos que se tapan se alejan al menos 14 px entre sí, respetando el orden en el tiempo (más separación que antes); el gráfico es más alto y la etiqueta de los mayores va sobre el círculo.
- **Limitación:** con esta separación 32 de los 66 círculos quedan desplazados respecto de su fecha exacta (hasta ~96 px en la escala 1900–2026); el punto gris marca la posición exacta, y al acotar el rango de años el desplazamiento se reduce.

### Ajuste de sonido 4: timbre muy contrastado (posterior al tag v2)

- **Qué cambió:** el tono sigue siendo siempre el mismo (220 Hz). La magnitud cambia varias capas del timbre a la vez: filtro de agudos (de 12.000 Hz en M7 a 250 Hz en M9,5), filtro de graves, realce de agudos en los chicos y de graves en los grandes, un chasquido agudo al inicio solo en los chicos, ruido (casi nada en los chicos, mucho retumbo grave en los grandes) y aspereza por temblor rápido en los grandes.
- **Medición:** el centroide espectral baja de ~5.800 Hz (M7, 62 % de la energía sobre 1.500 Hz) a ~250 Hz (M9,5, 0 %); el pico espectral queda en ~223 Hz en todas las magnitudes. Los picos de volumen no superan 0,92.
- **Motivo:** al escuchar, la diferencia entre magnitudes no se percibía.

### Sonido con Tone.js, escala ajustable y reproducción controlable (posterior al tag v2)

- **Sonido:** se usa Tone.js (la librería del curso). La magnitud se codifica con el tono, muy agudo en M7 (≈2000 Hz) y muy grave en M9,5 (≈45 Hz), y con la duración (0,35 s a 2,75 s); los grandes suman una onda con armónicos, un golpe grave y ruido de retumbo. La profundidad se oye en el ataque y en la aspereza; la latitud, en el estéreo. Respaldo: si Tone.js no carga, se usa el motor Web Audio anterior.
- **Reproducción:** un terremoto por vez, con control de velocidad (0,5 a 4 s por terremoto, ajustable en vivo) y control de sincronía sonido-gráfico para compensar la latencia de la salida de audio (por ejemplo, audífonos Bluetooth).
- **Escala:** control de ancho del gráfico (1× a 4×, por defecto 2×); los círculos mantienen su tamaño y se separan más; los nombres de las ciudades quedan fijos a la izquierda.
- **Separación:** el orden de izquierda a derecha es siempre el orden en el tiempo (la línea de reproducción nunca retrocede); separación mínima de 16 px entre círculos; las etiquetas buscan un lugar donde no tapen círculos; la posición exacta de los círculos desplazados se muestra al pasar el cursor.
- **Motivo:** al probarlo, el sonido no era representativo, iba demasiado rápido, los círculos seguían solapándose y la línea de reproducción se devolvía.

### Reproducción estrictamente ordenada (posterior al tag v2)

- **Qué cambió:** como máximo un terremoto por cuadro y un tiempo mínimo entre sonidos (si el navegador se atrasa, el tiempo se detiene en vez de disparar varios de golpe); cada sonido desvanece al anterior; escala del gráfico por defecto 3× (hasta 6×) y separación mínima de 20 px entre círculos.
- **Motivo:** en una prueba el segundo y el tercer sonido salieron con 21 ms de diferencia (se oían encimados o en desorden). Tras la corrección, en una reproducción completa la separación mínima fue de ~500 ms con 0 inversiones de orden.

### Separación horizontal entre terremotos consecutivos (posterior al tag v2)

- **Qué cambió:** dos terremotos consecutivos en el tiempo quedan siempre al menos 24 px separados en horizontal, aunque estén lejos en latitud, para que el orden cronológico se lea de izquierda a derecha y la marca de reproducción no parezca devolverse; escala por defecto 4× (hasta 8×).
- **Medición:** en los anchos 3×, 4×, 6× y 8×: 0 retrocesos de orden, 0 círculos solapados y paso mínimo de 24 px. Con 4×, el mayor desplazamiento respecto de la fecha exacta es de ~3,8 años; con 8×, ~1,7 años.
- **Motivo:** en la reproducción, dos terremotos seguidos podían quedar a 1 px en horizontal (y muy lejos en vertical), y se veía como si la marca saltara hacia atrás.

### Ejes legibles (posterior al tag v2)

- **Qué cambió:** las etiquetas de los años son más grandes y en negrita, la primera ya no se corta, y al ensanchar el gráfico se rotulan más años (cada 5 años a 4×, cada año a 8×); el ancho mínimo del gráfico pasa a 2× (con menos no caben 66 terremotos separados 24 px).
- **Motivo:** al ensanchar el gráfico los años quedaron diminutos y el primero aparecía cortado ("900").
