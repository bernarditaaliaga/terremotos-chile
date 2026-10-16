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

### V3 — sismograma y mapa (rediseño tras la R1)

- **Feedback recibido en la R1 (resumen):** hacer la visualización más intuitiva y con menos texto; sonido y contexto visual más relacionados con terremotos; resumir el título; dejar los datos del sonido como etiqueta; mostrar la información del terremoto al pasar el cursor; los círculos no distinguen bien las magnitudes; la barra de controles superior repetía lo que ya hacía el scroll horizontal.
- **Qué cambió:**
  - La magnitud pasa de ser el tamaño de un círculo a la **altura de una barra** (sismograma): el eje horizontal es el tiempo y el vertical la magnitud.
  - Se agregó un **mapa de Chile** con todos los epicentros; el terremoto que se señala o suena se resalta con una onda que se expande.
  - El detalle del terremoto aparece **al pasar el cursor** (ya no hay panel lateral).
  - Título resumido; se quitaron los controles de años y de ancho; el leyenda y los filtros de profundidad son lo mismo (casillas con su color); los ajustes de sonido quedaron dentro de "Ajustes".
  - **Sonido:** un golpe seco y un retumbo de ruido filtrado, más crujido de roca al inicio. La magnitud cambia el tono (de ~200 Hz a ~40 Hz), la duración, el oscurecimiento del retumbo y el temblor; la profundidad, el ataque y la aspereza; la latitud, el estéreo.
- **Qué se descartó:** codificar la magnitud con una escala de color verde-rojo (el color no tiene orden y el rojo-verde es problemático para el daltonismo; el color queda para la profundidad).
- **Medición:** en una ventana ancha (gráfico de 1188 px) el desplazamiento medio de una barra respecto de su fecha exacta es de ~1,3 años (máx. ~5,7 en el grupo de 1960); con 728 px sube a ~4,4 años; al filtrar por magnitud casi no hay desplazamiento. En la reproducción completa: 66 sonidos, 0 inversiones de orden, separación mínima de ~490 ms. Tono dominante medido: ~199 Hz (M7) a ~41 Hz (M9,5).
- **Limitaciones:** la posición horizontal de algunas barras es aproximada (al pasar el cursor se ve la fecha exacta); el eje vertical parte en M6,6 para que los M7 tengan barra visible; el sonido no está validado con usuarios.

### V3 (completada): color, contexto de terremoto y más intuitividad

- **Color y forma:** el color pasa a codificar la magnitud con una escala continua verde → amarillo → naranjo → rojo oscuro (el mismo dato que la altura, así que quien no distinga los colores igual lo lee en la altura) y la profundidad se codifica con la forma del marcador (círculo, rombo, triángulo). Con esto se acoge la idea de una escala de colores "de semáforo", cuidando que no sea la única señal.
- **Contexto de terremoto:** portada con un sismograma dibujado con los datos reales (cada pico es un terremoto), tarjetas con cifras clave (66 terremotos, 12 de M8+, uno cada 1,9 años, el mayor), franja de "gran terremoto (M8+)", mapa con océano, países vecinos y un esquema de la placa de Nazca, onda que se expande en el mapa y una sacudida del gráfico proporcional a la magnitud mientras suena.
- **Intuitividad sin perder información:** al filtrar, los terremotos ocultos quedan como "fantasmas" tenues (no desaparecen ni se mueven); el detalle aparece al pasar el cursor e incluye cuántas veces más energía que un M7; un solo marcador resaltado y un solo sonido a la vez.
- **Limitaciones:** los datos de contexto del mapa son esquemáticos (la flecha de la placa de Nazca es un esquema, no una medición); la posición horizontal de algunas barras es aproximada (en una ventana ancha el desplazamiento medio es de ~1,3 años); el sonido y el color aún no están validados con usuarios.

### V3: ajustes tras la prueba con la versión de color (mapa y limpieza)

- **Qué cambió:** se quitó la franja naranja "Gran terremoto · M8+" y la "M" del eje (no se entendían y metían ruido visual); el mapa es más grande (columna de hasta 360 px) y se puede hacer zoom (rueda del mouse o botones +, − y ⟲), arrastrar y volver con doble clic; los puntos del mapa muestran el detalle al pasar el cursor y suenan al hacer clic, igual que las barras; el gráfico es más alto para equilibrar su altura con la del mapa.
- **Motivo:** retroalimentación de la propia autora al ver la versión: lo que no se entiende se borra (data-ink, cápsula 19), y para saber dónde ocurrió cada terremoto el mapa debe ser grande y explorable (mantra de Shneiderman: zoom y filtro).

### V3: zoom automático del mapa y vuelta a las proporciones anteriores

- **Qué cambió:** el mapa y el gráfico vuelven a las proporciones anteriores (al agrandarlos quedaba como si se hubiera acercado toda la página). El mapa se explora con la rueda del mouse (zoom), arrastrando (desplazamiento) y con los botones +, − y ⟲. Al pasar el cursor por una barra, el mapa se acerca solo al epicentro (4×) y vuelve a mostrar todo Chile al salir. Durante "Escuchar la historia" el mapa sigue cada epicentro con una animación suave; por eso el ritmo por defecto pasa a 2,5 s por terremoto (ajustable de 1 a 6 s).
- **Motivo:** se pidió un mapa navegable y que al señalar un punto se acercara a él; para que el movimiento alcance a verse la reproducción debe ser más lenta.
- **Medición:** al pasar por una barra, el mapa queda a 4× con el epicentro centrado; al soltar, vuelve a la vista completa; en la reproducción el epicentro de cada evento queda visible a 4×.

### V3: rediseño del sonido (tono + ritmo + duración)

- **Qué cambió:** el sonido deja de depender del color del ruido y usa las señales que el oído distingue mejor (cápsula 27): **tono** (de 880 Hz, muy agudo, en M7 a 55 Hz, muy grave, en M9,5), **ritmo** (de 1 golpe en M7 a 7 golpes encadenados en M9,5, como una réplica) y **duración** (cada golpe dura más cuanto mayor es el terremoto). Se mantienen el retumbo, el crujido de roca al inicio, el temblor en los grandes, la profundidad en el ataque y la aspereza, y la latitud en el estéreo. La barra de colores de la leyenda es clicable: al hacer clic se oye esa magnitud, para comparar suave contra fuerte al instante.
- **Motivo:** al escuchar, no se distinguía el sonido de un terremoto de baja magnitud del de uno de alta; el diseño anterior dependía sobre todo del timbre del ruido.
- **Medición (señal analizada en el navegador):** tono dominante 879 / 504 / 287 / 170 / 94 / 53 Hz para M7 / 7,5 / 8 / 8,5 / 9 / 9,5; golpes detectados 1, 2, 3, 5, 7, 7; duración audible de 0,3 s a 1,8 s.
- **Limitación:** el volumen sube con la magnitud (de 0,03 a 0,10 de nivel eficaz) para compensar que los graves se oyen menos; el mapeo no está validado con usuarios (¿se interpreta "más grave y con más golpes = más fuerte"?).

### V3: tono invertible para comparar qué se entiende mejor

- **Qué cambió:** en "⚙ Ajustes" hay un selector "Tono del sonido": *grave = más fuerte* (por defecto) o *agudo = más fuerte*. En ambos casos el ritmo (más golpes) y la duración crecen con la magnitud, y el volumen siempre sube con ella. El texto de ayuda cambia según el modo elegido.
- **Motivo:** no hay una respuesta teórica segura sobre qué polaridad se entiende mejor (para "tamaño" muchas personas asocian lo grande con lo grave; para "intensidad" muchas asocian "más" con "más agudo"), así que se deja elegir para probarlo con personas.
- **Medición:** tono dominante 879 → 287 → 53 Hz (modo grave) y 53 → 164 → 855 Hz (modo agudo) para M7 → M8 → M9,5; el volumen eficaz sube con la magnitud en ambos modos (0,031 → 0,133 y 0,046 → 0,084).
- **Pendiente:** definir la polaridad final según una prueba con usuarios (porcentaje de aciertos al decidir cuál de dos terremotos es el más fuerte, sin explicación previa).

### V3: decisión del sonido (agudo y fuerte = más intenso)

- **Decisión:** la magnitud se codifica con un tono que sube (de ~53 Hz, grave, en M7 a ~855 Hz, agudo, en M9,5), y con un volumen que también sube (rango de ~17 a 20 dB), además del ritmo (de 1 a 7 golpes) y la duración. Los tonos agudos y fuertes representan los terremotos mayores y los graves y suaves, los leves. Se mantiene el selector en "⚙ Ajustes" para poder comparar con la polaridad contraria.
- **Motivo (rationale):** lo agudo y fuerte se percibe como más molesto y urgente, como en las alarmas auditivas (cápsula 27); eso calza con un terremoto mayor. Se combinan varias señales (tono, volumen, ritmo y duración) para que la diferencia no dependa de una sola.
- **Medición:** tono dominante 53 / 94 / 164 / 287 / 498 / 855 Hz para M7 / 7,5 / 8 / 8,5 / 9 / 9,5; volumen eficaz de 0,012 a 0,083 (razón de ~7 veces); pico máximo 0,38.
- **Limitación:** un M7 suena bajo y grave y puede costar oírlo en parlantes pequeños; no está validado con usuarios; el volumen depende del volumen del dispositivo.
