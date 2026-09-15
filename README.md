# Spec-Driven Development con GitHub Spec Kit

### Taller práctico · Extremadura Digital Day (EDD)

<img src="https://extremaduradigitalday.com/storage/logo-rectangular-edd25.webp" alt="Extremadura Digital Day" width="250">

<img src="https://intia.unex.es/media/logo_hu_ad5da9a51aea4e65.png" alt="Extremadura Digital Day" width="120">


**Del enunciado ambiguo a la especificación ejecutable.** 90 minutos, con las manos en el teclado.

Aquí tienes las **transparencias** y todos los **prompts y órdenes** que usamos durante la sesión,
para que los copies en lugar de teclearlos. En un prompt de diez líneas las erratas son
sorprendentemente fáciles y sorprendentemente caras.

Emilio Delgado Muñoz — Institutos Universitarios de Tecnologías Informáticas Aplicadas,
Universidad de Extremadura — `edm@unex.es`

---

## 🚨 Antes del taller

No hay tiempo para instalar nada en la sala: el primer bloque son **cinco minutos de comprobación**
y ya. Sigue la guía de instalación que recibiste con la convocatoria (unos 15 minutos) y llega con
esto funcionando:

```
python3 --version     # 3.11 o superior
git --version
uv --version
claude --version      # y `claude doctor` sin errores
specify --help
```

Y, dentro de `claude`, que `/status` muestre `ANTHROPIC_AUTH_TOKEN` y la URL de OpenRouter.

Si algo falla, escríbeme **antes** del taller a `edm@unex.es`. Resolverlo por correo cuesta cinco
minutos; resolverlo en la sala cuesta media sesión.

---

## Contenido

```
slides/     Las transparencias (ES y EN)
prompts/    Los prompts y órdenes del taller, listos para copiar
```

### Transparencias

| Fichero                                                        |         |
| -------------------------------------------------------------- | ------- |
| [`slides/slides-sdd-edd.es.pdf`](slides/slides-sdd-edd.es.pdf) | Español |
| [`slides/slides-sdd-edd.en.pdf`](slides/slides-sdd-edd.en.pdf) | English |

### Prompts y órdenes

Están en texto plano y **sin acentos**, a propósito: así se copian y pegan en cualquier terminal
sin sorpresas de codificación.

| Fichero                                                                                                 | Cuándo se usa                                                                  |
| ------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| [`prompts/bashrc-openrouter.txt`](prompts/bashrc-openrouter.txt)                                        | Antes del taller: las cuatro variables que conectan Claude Code con OpenRouter |
| [`prompts/install.txt`](prompts/install.txt)                                                            | Antes del taller: instalar la CLI de Spec Kit con la versión fijada            |
| [`prompts/init.txt`](prompts/init.txt)                                                                  | Bloque A: `specify init`, `git init` y primer commit                           |
| [`prompts/constitution.es.txt`](prompts/constitution.es.txt) · [`.en.txt`](prompts/constitution.en.txt) | Bloque A: la constitución del proyecto                                         |
| [`prompts/specify.es.txt`](prompts/specify.es.txt) · [`.en.txt`](prompts/specify.en.txt)                | Bloque A: el enunciado de la biblioteca                                        |
| [`prompts/plan.es.txt`](prompts/plan.es.txt) · [`.en.txt`](prompts/plan.en.txt)                         | Bloque B: el plan técnico                                                      |
| [`prompts/implement.es.txt`](prompts/implement.es.txt) · [`.en.txt`](prompts/implement.en.txt)          | Bloque B: implementación acotada al módulo de cálculo                          |
| [`prompts/specify2.es.txt`](prompts/specify2.es.txt) · [`.en.txt`](prompts/specify2.en.txt)             | Bloque C: el cambio de requisito                                               |

Da igual en qué idioma escribas al agente. Lo que **no** debe cambiar dentro del grupo es el
contenido: si cada pareja parte de un enunciado distinto, no podremos comparar resultados.

---

## Agenda de los 90 minutos

| Min   | Bloque   | Contenido                              |
| ----- | -------- | -------------------------------------- |
| 0–5   | Arranque | Comprobación del entorno               |
| 5–12  | Marco    | SDD en cinco minutos y el caso PenaLib |
| 12–25 | A        | `init` y constitución                  |
| 25–45 | A        | `specify`, lectura crítica y `clarify` |
| 45–58 | B        | `plan`, `tasks` y `analyze`            |
| 58–72 | B        | `implement` de la primera versión      |
| 72–85 | C        | El cambio de requisito                 |
| 85–90 | Cierre   | Puesta en común, límites y críticas    |

---

## El caso: PenaLib

La biblioteca de un centro presta libros a sus socios y quiere una herramienta que calcule las
penalizaciones por devolución tardía. El encargo, tal y como llega, son cuatro líneas
([`prompts/specify.es.txt`](prompts/specify.es.txt)) que dejan sin resolver **al menos diez
decisiones de negocio**: ¿días naturales o hábiles?, ¿cuánto se penaliza?, ¿hay tope?, ¿qué
significa «pendiente»?, ¿todos los socios son iguales?…

Un agente sin especificación responderá a las diez por su cuenta, en silencio y de forma plausible.
Y te enterarás cuando el sistema esté en producción. De eso va el taller.

---

## Chuleta rápida

### Dentro del agente

```
/speckit-constitution   principios del proyecto
/speckit-specify        que construir y por que
/speckit-clarify        resolver ambiguedades   (antes de plan)
/speckit-plan           como construirlo
/speckit-checklist      calidad de los requisitos
/speckit-tasks          en que orden
/speckit-analyze        coherencia              (antes de implement)
/speckit-implement      construir
/speckit-converge       reconciliar codigo y artefactos

/speckit-git-commit     confirmar; se ofrece antes de implement
/speckit-git-feature    crear la rama de la funcionalidad
/speckit-git-initialize inicializar el repositorio
```

> ⚠️ En Claude Code se escriben **con guion** (`/speckit-specify`), no con punto. La documentación
> oficial usa el punto y algunos mensajes de error de Spec Kit también: tradúcelo mentalmente.

### Claude Code

```
claude              # abrir sesion en el directorio actual
claude --version
claude doctor       # diagnostico de instalacion

/status     a que endpoint y con que token se conecta
/help       comandos disponibles
/clear      vaciar el contexto
/exit       salir
Esc         interrumpir al agente
Shift+Tab   cambiar de modo de permisos
```

---

## Reglas de oro

1. La especificación describe **comportamiento observable**; el plan describe **tecnología**. No los
   mezcles.
2. Una funcionalidad, una carpeta en `specs/`.
3. `clarify` antes de `plan`; `analyze` antes de `implement`.
4. **Si un requisito no tiene número, todavía no es un requisito.**
5. Ante un cambio, edita el artefacto de más arriba que esté afectado y regenera hacia abajo.
6. Commit de los artefactos por separado del código.
7. Modo manual mientras generas artefactos; aceptar ediciones mientras implementas.

---

## ⚠️ Antes de subir tu proyecto a ningún sitio

Comprueba que tu clave de OpenRouter no viaja dentro del repositorio:

```
git log -p | grep sk-or-
```

Si aparece, revócala desde el panel de OpenRouter y crea otra. Tu clave es dinero y es personal.

---

## Para seguir

- Repositorio de Spec Kit: <https://github.com/github/spec-kit>
- Documentación: <https://github.github.io/spec-kit>
- Metodología completa: el fichero `spec-driven.md` del repositorio
- Documentación de Claude Code: <https://code.claude.com/docs>

> Casi todo el material publicado antes de mediados de 2026 usa la sintaxis antigua (`--ai`,
> `/specify` sin prefijo) y da por hecho que `specify init` crea el repositorio de git. Si un
> tutorial no menciona `--integration`, está desactualizado.

**Versión de referencia: Spec Kit v1.0.2.** Está fijada a propósito: el proyecto rompe su interfaz
cada pocas versiones.

---

## Créditos

Material adaptado del taller de Spec-Driven Development de **Gregorio Robles** (Universidad Rey
Juan Carlos), publicado en <https://gsyc.urjc.es/grex/sdd/> y preparado originalmente para la
escuela de verano LLMA4SE 2026. El caso de estudio, los prompts y la estructura de la sesión son
suyos; esta versión los reajusta a los 90 minutos del Extremadura Digital Day.

Spec Kit es un proyecto de código abierto de GitHub distribuido bajo licencia MIT.
