# Evidencia visual de cada versión

Cada captura corresponde al código exacto de su versión, identificado por su tag y su commit (con la fecha real del commit). Las capturas de V2, V3 y V4 se obtuvieron renderizando ese código sin modificarlo (`git archive <tag>`).

| Versión | Tag | Commit | Fecha del commit | Captura |
|---|---|---|---|---|
| V1 | `v1` | `8756ee7` | 13-10-2026 10:26 | `v1.png` |
| V2 | `v2` | `8d4688a` | 13-10-2026 11:03 | `v2.png` |
| V3 | `v3` | `6bae955` | 16-10-2026 13:25 | `v3.png` |
| V4 | `v4` | `dc2c858` | 22-10-2026 15:43 | `v4.png` |

## Sonido de cada versión (no se puede capturar en una imagen; está en el video de cada una)

- **V1 y V2:** tono y duración según la magnitud (de 180 Hz y 0,5 s a 45 Hz y 3,75 s) y timbre según la profundidad (Web Audio).
- **V3:** tono, volumen, ritmo (de 1 a 7 golpes) y duración; más agudo y fuerte = mayor magnitud (Tone.js).
- **V4:** solo cambia el tono (de 220 a 1760 Hz); mismo timbre, duración y volumen percibido (Tone.js).
