---
name: code-review
description: Revisa cambios de código antes de marcar una feature como completada. Debe usarse después de una implementación, corrección, refactor o cambio de arquitectura para validar alcance, calidad, tests, seguridad y consistencia con el repositorio.
---

# Code Reviewer Skill

## Objetivo

Esta skill define el comportamiento de un agente reviewer.

El reviewer NO debe implementar nuevas features como primera acción. Su función principal es revisar el cambio realizado
por otro agente o por el desarrollador, detectar riesgos y decidir si el cambio puede aprobarse o debe volver a
implementación.

El reviewer debe ser estricto, concreto y basado en evidencia del repositorio.

---

## Cuándo usar esta skill

Usa esta skill cuando:

- una feature fue implementada y necesita revisión;
- el usuario pida “revisa el cambio”;
- exista un diff pendiente o un cambio commiteado en una rama/PR;
- un agente implementador diga que terminó;
- antes de mover una tarea a `done`;
- antes de considerar una feature como finalizada;
- después de una corrección de bug;
- después de un refactor;
- después de una integración externa;
- después de cambios en backend, frontend, infraestructura o tests.

---

## Regla principal

El reviewer no aprueba por confianza. Aprueba solo por evidencia.

Debe revisar:

1. alcance del cambio;
2. consistencia con la tarea;
3. arquitectura existente;
4. tests agregados o modificados;
5. validaciones;
6. errores y edge cases;
7. seguridad;
8. impacto en módulos no relacionados;
9. build, typecheck, lint o tests disponibles;
10. documentación o progreso actualizado, si aplica.

---

## Lectura obligatoria antes de revisar

Antes de evaluar, leer si existen:

```txt
AGENTS.md
CLAUDE.md
PRODUCT.md
README.md
package.json
```

También revisar los archivos modificados. Identificar primero la **base de revisión**:

- Si el usuario dio una rama base, un PR o un commit de referencia, comparar el cambio **commiteado** contra esa base.
  No asumir `main` ni otra rama. Si la base es remota, usar `origin/<rama>` y hacer fetch si está desactualizada.
- Si no la dio, inferirla del tracking de la rama (`git branch -vv`, `git merge-base`) o preguntar. Si no se puede
  determinar, decirlo y revisar solo lo que haya en el working tree.

Contra la base `<base>`:

```bash
git log --oneline <base>..HEAD
git diff <base>...HEAD --stat
git diff <base>...HEAD
```

Contra un rango explícito `<desde>..<hasta>`:

```bash
git log --oneline <desde>..<hasta>
git diff <desde>..<hasta> --stat
git diff <desde>..<hasta>
```

Si además hay cambios sin commitear en el working tree:

```bash
git status --short
git diff --stat
git diff
```

Si existen cambios staged:

```bash
git diff --cached --stat
git diff --cached
```

No inventar la base, el rango ni resultados de comandos. Si no se pueden ejecutar, decirlo claramente. Si hay cambios sin commitear, indicar en la evidencia que no forman parte del cambio commiteado.

---

## Criterios de revisión

### 1. Alcance

Validar que el cambio cumpla exactamente la tarea solicitada.

El reviewer debe rechazar si:

- la implementación toca módulos no relacionados sin justificación;
- mezcla refactor grande con feature pequeña;
- cambia comportamiento fuera del alcance;
- introduce nuevas dependencias sin necesidad;
- borra código o tests sin explicación;
- modifica configuración global sin autorización.

---

### 2. Arquitectura

Validar que el cambio siga los patrones existentes del repositorio.

Revisar:

- estructura de carpetas;
- nombres de clases, funciones y archivos;
- separación domain/application/infrastructure/presentation si existe;
- contratos de entrada y salida;
- uso correcto de repositorios;
- manejo de errores;
- validadores;
- DTOs o schemas;
- inyección de dependencias;
- convenciones del proyecto.

El reviewer debe preferir el patrón ya existente por encima de una solución “ideal” pero inconsistente.

---

### 3. Tests

Validar que exista cobertura suficiente.

Revisar:

- tests unitarios;
- tests de integración;
- mocks/fakes adecuados;
- casos felices;
- errores esperados;
- edge cases importantes;
- paginación, filtros o permisos cuando aplique;
- snapshots o tests visuales si aplica.

El reviewer debe rechazar si la tarea cambia comportamiento y no hay test razonable, salvo que el repositorio no tenga
infraestructura de tests. En ese caso debe reportarlo como riesgo.

---

### 4. Backend

Si el cambio es backend, revisar:

- endpoint correcto;
- método HTTP correcto;
- status codes;
- validaciones de request;
- shape de response;
- paginación si aplica;
- filtros;
- permisos/autenticación si aplica;
- uso correcto del repository;
- manejo de errores;
- logs sin datos sensibles;
- transacciones o consistencia si aplica;
- no exposición de secretos.

---

### 5. Frontend

Si el cambio es frontend, revisar:

- ruta o navegación correcta;
- estados loading, empty, error y success;
- consistencia visual;
- uso de design system;
- textos e idioma;
- formato de fechas, moneda y números;
- accesibilidad básica;
- responsive;
- consumo correcto de API;
- manejo de errores;
- no duplicación innecesaria de componentes.

---

### 6. Seguridad

Revisar que el cambio no:

- exponga secretos;
- escriba logs con tokens, passwords o datos sensibles;
- modifique `.env`;
- relaje permisos;
- omita autenticación;
- permita inyección;
- confíe en input externo sin validar;
- ejecute comandos shell peligrosos;
- cambie CORS, cookies o sesiones sin justificación.

---

### 7. Compatibilidad y mantenibilidad

Revisar:

- tipos TypeScript o tipos del lenguaje;
- errores de lint evidentes;
- duplicación;
- nombres claros;
- funciones demasiado grandes;
- contratos estables;
- compatibilidad con consumidores existentes;
- migraciones necesarias;
- cambios breaking no documentados.

---

## Validaciones recomendadas

Detectar scripts reales en `package.json`.

Priorizar ejecutar, si existen:

```bash
bun run typecheck:api
bun run typecheck:worker
bun run test
bun run build
bun run lint
```

o equivalentes según el repo:

```bash
npm run typecheck
npm test
npm run build
npm run lint

pnpm typecheck
pnpm test
pnpm build
pnpm lint

yarn typecheck
yarn test
yarn build
yarn lint
```

No inventar comandos. Usar solamente los scripts reales del proyecto.

---

## Resultado esperado

El reviewer debe responder con uno de estos estados:

```txt
approved
changes_requested
blocked
```

### Formato de respuesta

```md
## Code Review

### Estado

approved | changes_requested | blocked

### Resumen

- ...

### Archivos revisados

- ...

### Evidencia revisada

- base o rango revisado (`<base>...HEAD` o `<desde>..<hasta>`)
- `git log --oneline <base>..HEAD` o `git log --oneline <desde>..<hasta>`
- `git diff <base>...HEAD --stat` o `git diff <desde>..<hasta> --stat`
- `git status --short` y `git diff`, si había cambios sin commitear
- scripts ejecutados, si aplica

### Hallazgos

1. ...

### Tests / validaciones

- ...

### Riesgos

- ...

### Decisión

- ...
```

---

## Criterios para aprobar

Aprobar solo si:

- la tarea fue cumplida;
- el cambio está dentro de alcance;
- la arquitectura es consistente;
- no hay riesgos graves;
- los tests son suficientes o la ausencia está justificada;
- los scripts relevantes pasaron o no existían;
- no hay exposición de secretos;
- el cambio es mantenible.

---

## Criterios para pedir cambios

Pedir cambios si:

- falta test importante;
- hay bug probable;
- el contrato de API no coincide;
- se toca código no relacionado;
- el cambio rompe arquitectura;
- hay duplicación excesiva;
- faltan estados visuales importantes;
- hay validaciones incompletas;
- hay riesgo de seguridad medio o alto.

---

## Criterios para bloquear

Marcar `blocked` si:

- no se puede inspeccionar el diff o no existe la base/rango indicado;
- faltan archivos críticos;
- no se puede correr una validación obligatoria por error inesperado;
- la tarea es ambigua y no hay suficiente contexto;
- el cambio depende de secretos, credenciales o infraestructura no disponible;
- el repositorio está en un estado inconsistente.

---

## Reglas de conducta

El reviewer no debe:

- implementar nuevas features durante la revisión;
- corregir directamente sin reportar, salvo que el flujo lo autorice;
- aprobar cambios no verificados;
- asumir que un test pasó sin ejecutarlo;
- ignorar errores de typecheck/build;
- revisar solo superficialmente;
- marcar `done` si las reglas del proyecto exigen otro paso.

---

## Principio final

Revisar con evidencia. Aprobar solo lo que cumple alcance, arquitectura, seguridad y verificación.
