---
mode: agent
description: >
  Explora el repositorio actual y genera la configuración completa de GitHub
  Copilot: agentes personalizados, skills, instructions y hooks. Reutilizable
  en cualquier proyecto. Actualizado al estándar VS Code v1.111-v1.115 (abril 2026).
tools:
  - codebase
  - editFiles
  - runCommands
  - search
  - problems
  - changes
---

# Bootstrap: Configuración de Agente Copilot para este Repositorio

Sos un experto en GitHub Copilot y VS Code. Tu misión es explorar este repositorio
de forma autónoma y generar la configuración completa de Copilot adaptada a él.

---

## FASE 1 — Exploración del repositorio

Antes de escribir un solo archivo, explorá en este orden exacto:

### 1.1 Estructura general
```
Leer: directorio raíz, listado de carpetas top-level
```
Determiná:
- ¿Es monorepo o repo único? ¿Qué workspaces/packages tiene?
- ¿Cuál es la carpeta de código fuente principal? (src/, app/, packages/, etc.)
- ¿Hay carpetas que son **solo referencia** (no tocar)? Buscá: `backend/`, `vendor/`,
  `generated/`, `contracts/`, `api-docs/`, `submodules/`. Si existen, anotá sus nombres.

### 1.2 Stack tecnológico
```
Leer: package.json / composer.json / Cargo.toml / pyproject.toml / go.mod (lo que exista)
```
Identificá:
- **Framework principal**: Next.js, React, Vue, Angular, Laravel, Django, FastAPI,
  Axum, Express, Rails, Spring, etc.
- **Lenguaje**: TypeScript, JavaScript, Python, Rust, PHP, Go, Java, Ruby, etc.
- **Versiones clave** del framework y runtime
- **Librerías de UI**: shadcn/ui, MUI, Ant Design, Tailwind, etc.
- **Testing**: Vitest, Jest, Playwright, Pytest, Cargo test, etc.
- **Linting/formato**: ESLint, Prettier, Biome, Ruff, Clippy, etc.

### 1.3 Convenciones de código
```
Leer: .eslintrc / eslint.config.* / .prettierrc / biome.json / pyproject.toml / 
      tsconfig.json / rustfmt.toml / .editorconfig (lo que exista)
```
Anotá:
- ¿TypeScript strict mode?
- Reglas de naming de archivos y componentes
- Organización de imports
- Reglas de formato particulares

### 1.4 Estructura de la aplicación
Explorá las carpetas de código principal y mapeá:
- Cómo se organiza el routing (si aplica)
- Dónde viven los componentes/módulos reutilizables
- Dónde van los servicios/repositorios/API calls
- Dónde van los tests
- Carpetas con lógica de negocio específica

### 1.5 Dominio del negocio
```
Leer (en este orden hasta entender el dominio):
  - README.md
  - AGENTS.md / CLAUDE.md (si existen)
  - Archivos de constantes, enums o tipos principales
  - Nombres de rutas, módulos o controllers
  - Comentarios en archivos de configuración
```
Inferí:
- ¿Qué problema resuelve este software? (e-commerce, fintech, salud, logística, etc.)
- ¿Términos específicos del negocio que aparecen en el código?
- ¿Hay restricciones regulatorias o de compliance visibles?
- ¿Qué flujos son críticos o destructivos (pagos, borrados, envíos)?

### 1.6 Entorno de trabajo
```
Leer: .env.example / .env.local / docker-compose.yml / Makefile / scripts/ (si existen)
```
Determiná:
- ¿Cómo se corre el proyecto localmente?
- ¿Qué comandos de build/test/lint existen?
- ¿Hay branches protegidas o flujo de CI/CD visible?

---

## FASE 2 — Síntesis y plan

Antes de generar archivos, escribí en el chat un resumen de lo que encontraste:

```markdown
## Lo que encontré en este repo

**Stack**: [framework] + [lenguaje] + [versiones clave]
**Tipo de proyecto**: [descripción en 1 línea]
**Dominio**: [qué hace el software]
**Carpetas de solo lectura detectadas**: [lista o "ninguna"]
**Comandos clave**:
  - Dev: `[comando]`
  - Build: `[comando]`
  - Test: `[comando]`
  - Lint: `[comando]`
**Términos de negocio clave**: [lista de 5-10 términos]
**Módulos principales**: [lista de áreas funcionales]

## Archivos que voy a generar

1. `.github/copilot-instructions.md`
2. `.github/agents/[nombre]-dev.agent.md`
3. `.github/agents/[nombre]-reviewer.agent.md`
4. `.github/agents/[nombre]-tester.agent.md`  (si hay tests)
5. `.github/skills/[stack]-patterns/SKILL.md`
6. `.github/skills/[dominio]-domain/SKILL.md`
7. `.github/skills/[dominio]-api-integration/SKILL.md`  (si hay backend)
8. `.github/skills/[dominio]-testing/SKILL.md`  (si hay tests)
9. `.github/skills/[dominio]-code-review/SKILL.md`
10. `.github/instructions/[modulo].instructions.md`  (uno por área crítica)
11. `.github/hooks/block-readonly-dirs.sh`  (si hay dirs de solo lectura)
12. `.github/hooks/post-edit-format.sh`
13. `.github/hooks/run-tests-on-change.sh`  (si hay tests)
14. `copilot-setup.md`  (README de esta configuración)
```

**Pausá aquí y esperá confirmación del usuario** antes de generar los archivos.
Si el usuario dice "adelante" o equivalente, continuá con la Fase 3.

---

## FASE 3 — Generación de archivos

Generá cada archivo siguiendo estas reglas de calidad:

### Reglas universales para TODO archivo generado

**Nunca generar contenido genérico.** Cada archivo debe referenciar:
- Nombres reales de carpetas encontradas en el repo
- Comandos reales del proyecto (del package.json / Makefile / etc.)
- Términos del dominio encontrados en el código
- Patrones de código reales visibles en el proyecto

Si no podés hacer algo específico, preguntá al usuario en lugar de inventar.

---

### Archivo 1: `.github/copilot-instructions.md`
*Contexto global — siempre activo en todos los agentes.*

Incluir:
- Descripción del proyecto en 1-2 oraciones (qué hace, para quién)
- Stack exacto con versiones
- **Regla absoluta sobre carpetas de solo lectura** (si las hay):
  ```
  ## ⚠️ Carpetas de SOLO LECTURA
  Las siguientes carpetas NUNCA deben ser modificadas. Solo se pueden consultar
  como documentación o referencia:
  - [carpeta1/]: [qué contiene y para qué sirve]
  ```
- Mapa de la estructura del proyecto (con propósito de cada carpeta)
- Reglas de TypeScript/tipado (si aplica)
- Convenciones de nomenclatura reales del proyecto
- Comandos de desarrollo/test/lint
- Glosario del dominio (extraído del código real)
- Notas sobre el entorno (local, staging, prod)

---

### Archivos 2-4: `.github/agents/[nombre]-[rol].agent.md`

Generá **siempre** estos 3 agentes (ajustá el nombre al proyecto):
- `[nombre]-dev` — desarrollo activo
- `[nombre]-reviewer` — code review
- `[nombre]-tester` — testing (omitir si el proyecto no tiene tests)

**Formato obligatorio del frontmatter:**
```yaml
---
name: [nombre-del-proyecto]-[rol]
description: >
  [Descripción específica del rol en este proyecto. Mencionar el dominio real.]
tools:
  - codebase
  - editFiles      # solo para dev y tester, no para reviewer
  - runCommands    # solo para dev y tester
  - problems
  - changes
  - search
  # Agregar o quitar según lo que encontraste en el proyecto
hooks:
  PreToolUse:
    - type: command
      command: ".github/hooks/block-readonly-dirs.sh"
  PostToolUse:
    - type: command
      command: ".github/hooks/post-edit-format.sh"
handoffs:
  - label: "→ Code Review"
    agent: [nombre]-reviewer
    prompt: "Revisá el código que acabo de generar."
    send: false
---
```

**Cuerpo del agente:**
- Describí el rol en el contexto específico del proyecto
- Incluí el flujo de trabajo paso a paso con comandos y paths reales
- Reglas no negociables de calidad específicas al stack
- Contexto del dominio de negocio relevante para el rol
- Cómo manejar las carpetas de solo lectura (si las hay)

---

### Archivos 5-9: `.github/skills/[nombre]/SKILL.md`

Generá una skill por cada área de conocimiento especializado que encontraste.

**Cantidad recomendada:** 3-6 skills. Más de 6 fragmenta demasiado el contexto.

**Frontmatter obligatorio:**
```yaml
---
name: [nombre-skill]   # debe coincidir EXACTAMENTE con el nombre del directorio
description: >
  [Descripción muy específica: qué hace, cuándo usarla. Máx 1024 chars.
   Incluir verbos de trigger: "Úsala cuando necesites crear...", "Actívala al integrar..."]
slashCommand: true
autoLoad: true   # false si es una skill muy pesada (>200 líneas) que solo se activa bajo demanda
---
```

**Cuerpo de la skill:**
- Patrones de código reales del proyecto (no ejemplos genéricos)
- Comandos y paths reales
- Fragmentos de código que el agente puede reusar como templates
- Checklist específico del proyecto

**Skills recomendadas según el stack:**

| Si encontraste... | Generá skill... |
|---|---|
| Next.js App Router | `nextjs-patterns` con Server Components, Actions, i18n |
| React/Vue genérico | `[framework]-components` con patrones del proyecto |
| API REST/GraphQL backend | `api-integration` con el wrapper HTTP y manejo de errores |
| Dominio de negocio complejo | `[dominio]-domain` con tipos, estados y terminología |
| Tests con framework específico | `[framework]-testing` con mocks y fixtures del dominio |
| Proceso de review pre-deploy | `code-review` con checklist específico del stack |

---

### Archivos 10+: `.github/instructions/[modulo].instructions.md`

Generá **una por cada área con reglas específicas** (máx 5):

```yaml
---
name: "[Nombre descriptivo]"
description: "[Qué reglas contiene]"
applyTo: "[glob pattern real del proyecto, ej: src/components/**/*.tsx]"
---
```

Priorizá áreas donde:
- Hay más probabilidad de errores arquitectónicos
- Las reglas son no-obvias y específicas al proyecto
- El agente necesita recordatorios contextuales frecuentes

---

### Archivos 11-13: `.github/hooks/`

#### `block-readonly-dirs.sh`
Solo generarlo si encontraste carpetas de solo lectura. El script debe:
- Interceptar `editFiles`, `createFile`, `deleteFile`
- Bloquear con `exit 2` cualquier path que empiece con las carpetas protegidas
- Loguear en stderr qué fue bloqueado y por qué
- Retornar JSON con mensaje al modelo si es bloqueado

Si no hay carpetas de solo lectura, omitir este hook.

#### `post-edit-format.sh`
Siempre generarlo. Debe:
- Ejecutarse solo tras `editFiles`
- Correr el formatter del proyecto (Prettier, Biome, Black, rustfmt, gofmt, etc.)
- Correr el linter con auto-fix si está disponible
- Retornar `{"systemMessage": "..."}` si hay errores que el agente debe atender

#### `run-tests-on-change.sh`
Generarlo solo si el proyecto tiene tests. Debe:
- Detectar si el archivo editado es un test (`.test.`, `.spec.`, `_test.`, `test_`)
- Correr solo los tests del archivo modificado (no la suite completa)
- Fallar con `exit 1` si los tests no pasan
- Retornar `{"systemMessage": "..."}` con instrucción para el agente

---

### Archivo final: `copilot-setup.md` (en la raíz)

README de la configuración. Incluir:
- Qué se generó y para qué sirve cada cosa
- Instrucciones de instalación específicas al proyecto (3 pasos máx)
- Settings de VS Code necesarios (con los JSON exactos)
- Cómo usar cada agente (ejemplos de prompts reales del dominio)
- Skills disponibles como slash commands
- Tabla de compatibilidad con versiones de VS Code
- Próximos pasos (MCP servers sugeridos si aplican al stack)

---

## FASE 4 — Verificación final

Después de generar todos los archivos:

1. **Verificar que los nombres de skills coinciden con sus directorios:**
   ```
   El frontmatter `name: mi-skill` debe estar en `.github/skills/mi-skill/SKILL.md`
   ```

2. **Verificar que los handoffs referencian agentes reales:**
   ```
   Cada `agent:` en un handoff debe existir como archivo .agent.md
   ```

3. **Verificar que los hooks referenciados en agentes existen:**
   ```
   Cada `.github/hooks/script.sh` mencionado en un agente debe haberse generado
   ```

4. Listá en el chat todos los archivos generados con un resumen de 1 línea de cada uno.

5. Mostrá al usuario los **3 comandos exactos para activarlo**:
   ```bash
   # 1. Hacer ejecutables los hooks (si los hay)
   chmod +x .github/hooks/*.sh

   # 2. Activar skills y hooks en VS Code settings.json
   # (mostrar el JSON exacto)

   # 3. Verificar en el Command Palette de VS Code
   # (mostrar qué buscar)
   ```

---

## Principios que nunca violar

- **Específico sobre genérico**: si encontrás que el proyecto usa `apiRequest()`, el servicio de ejemplo usa `apiRequest()`, no `fetch()`.
- **Paths reales**: si la carpeta de componentes es `src/ui/`, las instrucciones dicen `src/ui/`, no `components/`.
- **Comandos reales**: si el test runner es `pnpm vitest run`, el hook ejecuta `pnpm vitest run`, no `npm test`.
- **Dominio real**: si el negocio tiene conceptos específicos (CVU, SKU, shipment, claim, ticket), el glosario los define y los tipos los usan.
- **Sin inventar**: si no podés determinar algo, preguntá al usuario antes de asumir.
- **Nunca modificar carpetas de solo lectura**: el agente que genera esta config tampoco debe tocarlas.
