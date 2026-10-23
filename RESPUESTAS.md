# Respuestas del parcial

Estudiante: Libardo Morales Gutierrez

1. ¿Qué error encontraste en la validación de `stock`?

En `schemas/game.py` el campo `stock` tenía `ge=0`, por eso aceptaba 0. El enunciado pide de 1 a 50, así que lo cambié a `ge=1`. Ahora el JSON con `stock: 0` responde 422.

2. ¿Qué hacía que un juego pareciera creado pero no quedara persistido?

En `create_game` (services) se hacía `session.add(game)` y luego `session.refresh(game)`, pero faltaba `session.commit()`. Sin commit los cambios no se guardan en SQLite. Agregué el commit entre `add` y `refresh`.

3. ¿Qué problema tenía el endpoint que lista juegos?

En `routers/games.py` el `response_model` era `GameResponse` (un solo objeto), pero el endpoint devuelve varios registros. Lo cambié a `list[GameResponse]` y ahora responde una lista con 200.

4. ¿Cómo solucionaste la consulta de un ID inexistente?

`get_game` solo hacía `return session.get(Game, game_id)`, y cuando no existía devolvía `None`. Agregué un `if not game` que lanza `HTTPException(status_code=404, detail="Game not found")`, igual que ya hacía `delete_game`.

5. ¿Por qué DELETE podía responder correctamente y aun así no eliminar el registro de SQLite?

En `delete_game` se hacía `session.delete(game)` pero sin `session.commit()`. El delete solo queda marcado en la sesión y la respuesta "Game deleted" se devuelve igual, pero la base de datos no cambia. Al agregar el commit el registro se elimina de verdad y no reaparece al reiniciar.
