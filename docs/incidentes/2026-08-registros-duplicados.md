# Incidente: registros de atención duplicados (julio–agosto 2026)

## Qué pasó
Entre el 15 de julio y el 26 de agosto de 2026, varias atenciones quedaron guardadas más de una vez en la planilla.

- 14 casos de duplicados exactos (misma clienta, monto y forma de pago), con entre 2 y 4 copias cada uno.
- Si todos eran duplicados: **19 filas de sobra y $501.000 de más** en los totales.
- Otros 2 casos con el nombre tipeado distinto el mismo día, mismo monto y forma de pago (probable misma visita).
- 4 casos ambiguos: registros de prueba del formulario, una posible corrección que quedó duplicada, y una atención que se marcó "Pendiente" y al "Cobrar" creó una fila nueva en vez de actualizar la existente.

El 31 de agosto de 2026 se le envió a Fernanda un PDF con la lista para que confirmara cuáles borrar antes de tocar nada. Ese PDF tiene datos de clientas y **no se versiona** (este repo es público): está solo en local, en `docs/incidentes/privado/`.

## Causa
La sincronización entre los datos locales del teléfono y el servidor (`fetchData`):
- `0383def` — había una condición de carrera que reenviaba el mismo registro dos veces.
- `e29fc35` — la "reconciliación" automática volvía a subir registros locales que ya se habían borrado en el servidor, y así resucitaba duplicados.

## Lecciones (para no repetirlo)
1. **Cada registro necesita un ID único generado al crearlo**, y el servidor debe rechazar (o ignorar) un ID que ya existe. Los reintentos de red no pueden crear filas nuevas.
2. **Nunca re-subir datos locales automáticamente** sin comparar con lo que ya está en el servidor.
3. **"Cobrar" un pendiente actualiza la fila**, no crea otra.
4. **No borrar datos de producción sin confirmación** de quien usa la app: primero se prepara la lista y se valida con Fernanda.
5. Probar la sincronización con mala señal (o en modo avión y volviendo) antes de publicar cambios.
6. Los registros de prueba no se hacen en producción.
