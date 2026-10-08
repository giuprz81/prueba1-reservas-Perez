## Escenarios que elegí y por qué

Elegí seis escenarios para precisar los cruces de horarios, los extremos y el alcance por sala:

- Solapamiento parcial: `specs/001-reservas-sala/spec.md:24` describe intervalos que se cruzan parcialmente; `test/crear_reserva_test.dart:43` define la prueba correspondiente.
- Nueva reserva dentro de otra: `specs/001-reservas-sala/spec.md:27` exige rechazar la inclusión completa; `test/crear_reserva_test.dart:63` la prueba.
- Nueva reserva que contiene otra: `specs/001-reservas-sala/spec.md:30` cubre la inclusión inversa; `test/crear_reserva_test.dart:83` la prueba.
- La nueva empieza cuando termina la existente: `specs/001-reservas-sala/spec.md:33` declara conflictivo el extremo compartido; `test/crear_reserva_test.dart:103` lo prueba.
- La nueva termina cuando empieza la existente: `specs/001-reservas-sala/spec.md:36` cubre el orden inverso del extremo compartido; `test/crear_reserva_test.dart:123` lo prueba.
- Mismo horario en otra sala: `specs/001-reservas-sala/spec.md:39` exige aceptar la reserva de sala B; `test/crear_reserva_test.dart:143` verifica ese caso.

`specs/001-reservas-sala/spec.md:52` fija el mensaje exacto de rechazo; `test/crear_reserva_test.dart:58` comprueba ese texto y `test/crear_reserva_test.dart:60` comprueba que el conflicto no se guarde.

## Riesgo más grave del repositorio

La pantalla omite el caso de uso al reservar. `lib/main.dart:13` crea el repositorio de Supabase y `lib/main.dart:14` construye `CrearReserva`; `lib/presentation/reserva_page.dart:103` conecta el botón con `_reservar`, mientras `lib/presentation/reserva_page.dart:60` inserta directamente en Supabase. Ese flujo evita la validación de solapamiento del dominio.

## ¿La regla protege la app real?

Sí protege las llamadas a `CrearReserva`: `lib/domain/reservas_repository.dart:5` define la consulta por sala; `test/support/reservas_en_memoria.dart:10` la filtra sin red y `lib/data/supabase_reservas_repository.dart:13` filtra por sala en Supabase. `lib/domain/crear_reserva.dart:17` consulta esas reservas, `lib/domain/crear_reserva.dart:19` y `lib/domain/crear_reserva.dart:20` comparan los intervalos incluyendo los extremos, `lib/domain/crear_reserva.dart:24` devuelve el mensaje acordado y `lib/domain/crear_reserva.dart:28` guarda solo si no hubo conflicto.

El flujo actual de la pantalla no queda protegido: `lib/presentation/reserva_page.dart:103` activa `_reservar` y `lib/presentation/reserva_page.dart:60` muestra su inserción directa.

Como inferencia del diseño, dos solicitudes simultáneas podrían consultar una sala libre y luego guardarse ambas: `lib/domain/crear_reserva.dart:17` muestra la consulta y `lib/domain/crear_reserva.dart:28` el guardado posterior; `lib/data/supabase_reservas_repository.dart:13` y `lib/data/supabase_reservas_repository.dart:21` muestran operaciones separadas en Supabase. `supabase/migracion.sql:11` declara la restricción de fin posterior al inicio y `supabase/migracion.sql:12` cierra la definición de la tabla sin una restricción de solapamiento. Se trata de una posibilidad inferida, no de un fallo observado.
