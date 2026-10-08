# Feature Specification: Reservas de sala

**Feature Branch**: `001-reservas-sala`
**Created**: 2026-10-01
**Status**: Draft

## User Scenarios & Testing

### User Story 1 — Reservar una sala (Priority: P1)

Como estudiante autenticado, quiero reservar una sala de estudio por un intervalo de tiempo
para tener dónde trabajar con mi grupo.

**Why this priority**: sin reservas, la app no tiene propósito.

**Independent Test**: se puede probar creando una reserva y comprobando que queda registrada.

**Acceptance Scenarios**:

1. **Dado** que la sala A está libre, **Cuando** la reservo de 09:00 a 10:00, **Entonces** la
   reserva queda registrada a mi nombre.
2. **Dado** que elijo como inicio las 10:00 y como fin las 09:00, **Cuando** intento reservar,
   **Entonces** la reserva se rechaza con el mensaje "La hora de fin debe ser posterior a la de inicio".
3. **Dado** que la sala A tiene una reserva de 09:00 a 10:00, **Cuando** intento reservar la
   misma sala de 09:30 a 10:30, **Entonces** la nueva solicitud se rechaza con el mensaje
   "La sala ya está reservada en ese horario" y no se registra.
4. **Dado** que la sala A tiene una reserva de 09:00 a 11:00, **Cuando** intento reservar la
   misma sala de 09:30 a 10:00, **Entonces** la nueva solicitud se rechaza con el mensaje
   "La sala ya está reservada en ese horario" y no se registra.
5. **Dado** que la sala A tiene una reserva de 09:30 a 10:00, **Cuando** intento reservar la
   misma sala de 09:00 a 11:00, **Entonces** la nueva solicitud se rechaza con el mensaje
   "La sala ya está reservada en ese horario" y no se registra.
6. **Dado** que la sala A tiene una reserva de 09:00 a 10:00, **Cuando** intento reservar la
   misma sala de 10:00 a 11:00, **Entonces** la nueva solicitud se rechaza con el mensaje
   "La sala ya está reservada en ese horario" y no se registra.
7. **Dado** que la sala A tiene una reserva de 10:00 a 11:00, **Cuando** intento reservar la
   misma sala de 09:00 a 10:00, **Entonces** la nueva solicitud se rechaza con el mensaje
   "La sala ya está reservada en ese horario" y no se registra.
8. **Dado** que la sala A tiene una reserva de 09:00 a 10:00, **Cuando** intento reservar la
   sala B de 09:00 a 10:00, **Entonces** la nueva reserva queda registrada a mi nombre.

## Requirements

### Functional Requirements

- **FR-001**: El estudiante elige una sala, una hora de inicio y una hora de fin.
- **FR-002**: Una reserva aceptada queda registrada a nombre del estudiante para la sala y el
  intervalo elegidos.
- **FR-003**: Dos reservas de la misma sala no pueden solaparse. Se considera solapamiento
  compartir cualquier instante del intervalo, incluidos sus extremos.
- **FR-004**: Si una reserva existente de la misma sala se solapa con la solicitud, esta se
  rechaza con el mensaje exacto "La sala ya está reservada en ese horario" y no se registra.
- **FR-005**: La hora de fin debe ser posterior a la hora de inicio.
- **FR-006**: Una reserva de otra sala no genera conflicto, aunque tenga el mismo intervalo.

### Key Entities

- **Reserva**: sala, estudiante, inicio y fin.
- **Sala**: identificada por su nombre (Sala A, Sala B, Sala C).

## Success Criteria

- **SC-001**: Un estudiante completa una reserva en menos de 30 segundos.
