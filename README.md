# Gimnasio API — Código base (Semana 5)

API REST en NestJS para el gimnasio: `Clases`, `Horarios`, `Miembros` e `Inscripciones`, cada
módulo con dominio, DTOs e infraestructura separados (patrón repositorio + inyección por token).
Los datos viven en memoria — ningún repositorio se conecta todavía a una base de datos real.

Este proyecto es el punto de partida de la Práctica 8 (Prisma) y la Práctica 9 (Blindar la API).

## Cómo correrlo

```bash
npm install
npm run start:dev
```

El servidor levanta en `http://localhost:3000`. En `peticiones.http` está la batería completa de
pruebas (requiere la extensión "REST Client" de VS Code).

## Estructura

```
src/
  clases/        CRUD de clases del gimnasio
  horarios/      CRUD de horarios (día, hora, cupo, entrenador)
  miembros/      CRUD de miembros del gimnasio
  inscripciones/ inscribir a un miembro a un horario, con reglas de cupo y duplicados
  datos/         datos de arranque (seed) que usan Horarios y Miembros
```

Cada módulo sigue la misma forma: `dominio/` (entidades + interfaz del repositorio), `dto/`,
`infra/` (repositorio en memoria) y el token de inyección en `<módulo>.tokens.ts`.





1. ¿Qué línea del Service o Controller cambió para hablar con MySQL?
   Ninguna. Solo cambio el useClass en cada *.module.ts, de *MemoriaRepository a
   *PrismaRepository. Ni el Service ni el Controller conocen la diferencia porque dependen
   de la interfaz, no de la implementacion.

2. ¿Por qué InscripcionesService no cambió ni una línea de las reglas de cupo y duplicados?
   Porque esas reglas estan escritas contra InscripcionRepository, no contra Prisma ni contra
   el arreglo en memoria. Mientras la nueva implementacion cumpla el mismo contrato, la logica
   de negocio no se entera de donde vienen los datos.

3. ¿Por qué una interfaz no puede validar nada en tiempo de ejecución?
   Porque las de TypeScript son solo tipos: se usan en tiempo de compilacion y se borran por
   completo al transpilar a JavaScript. En runtime no queda ningun rastro de la interfaz, asi
   que no hay nada que ValidationPipe pueda inspeccionar. Las clases con decoradores de
   class-validator, en cambio, si existen en el JS compilado.

4. ¿Qué código y qué cuerpo responde con tipo equivocado / campo que no existe?
   La opción indispensable para esto es whitelist: true sin ella, forbidNonWhitelisted
   no hace absolutamente nada aunque esté en true.

5. ¿Cuántas líneas quedó más corto el controlador?
   De 81 a 58 lineas: 23 lineas menos

6. CORS: si la respuesta llega en los dos casos, ¿quién bloquea y a quién protege?
   El servidor siempre procesa y reponde igual, sin importar el origen CORS no es una barrera
   del backend. Quien bloquea de verdad es el navegador.
