> Detalla en esta sección los prompts principales utilizados durante la creación del proyecto, que justifiquen el uso de asistentes de código en todas las fases del ciclo de vida del desarrollo. Esperamos un máximo de 3 por sección, principalmente los de creación inicial o  los de corrección o adición de funcionalidades que consideres más relevantes.
Puedes añadir adicionalmente la conversación completa como link o archivo adjunto si así lo consideras

**Herramientas utilizadas en la Entrega 1:**

- **Gemini Pro:** borrador inicial de la documentación a partir de la idea del proyecto.
- **Claude Code** (aplicación de escritorio) con el modelo Claude Opus 5.5, en el modo de exploración de OpenSpec (`/opsx:explore`). En este modo el asistente analiza el repositorio y discute alternativas antes de generar ningún artefacto.

**Cómo se generó la documentación.** Primero, Gemini Pro produjo un borrador a partir de la idea del proyecto (Prompt 1 de §1). Después, ese borrador se revisó en una sesión de Claude Code: tres prompts dieron lugar a la primera versión de `readme.md` (secciones 0 a 6), y en la misma sesión se hicieron ajustes posteriores (Prompt 1 de §4). Por último, los tickets del MVP se desglosaron en 18 cambios de OpenSpec (§6), cuyos artefactos están en inglés y se enlazan desde `readme.md`. Salvo el Prompt 1 de §1, todos los prompts pertenecen a esa sesión. Cada prompt se documenta en la sección donde tuvo más impacto y se reproduce literalmente, en el idioma en que se escribió (inglés).

## Índice

1. [Descripción general del producto](#1-descripción-general-del-producto)
2. [Arquitectura del sistema](#2-arquitectura-del-sistema)
3. [Modelo de datos](#3-modelo-de-datos)
4. [Especificación de la API](#4-especificación-de-la-api)
5. [Historias de usuario](#5-historias-de-usuario)
6. [Tickets de trabajo](#6-tickets-de-trabajo)
7. [Pull requests](#7-pull-requests)

---

## 1. Descripción general del producto

**Prompt 1:**

*Herramienta:* Gemini Pro.

```
I need to create a system that has a access to a wordpress system securely via api do some analysis of blog posts with AI to identify the topics, the tone and style. The system researches similar topics or trendy related topics and suggest blog posts or post them on a schedule.

There has to be a mode with human intervention where topics are suggested, user approves, and reads a blog post and approves it for posting or ask for correction, or manually make changes and then post.

The system should have:

- Intelligence service - only LLM API internal accessible from backend service. Python 3.14, FastAPI, JInja, uv, langchain, littleLLM

- Rails api ruby backend

- React, NextJS, Tailwind frontend

- Postgres DB business

- Postgres vector DB to store ingested documents ingestion

- Redis
```

*Resultado:* Gemini Pro generó el borrador de partida de la documentación:

- ficha del proyecto (WP AutoPilot AI) con los modos Copilot y Autopilot;
- arquitectura de microservicios: Next.js, API en Rails con Redis + Sidekiq, y servicio de IA en FastAPI con LiteLLM y LangChain;
- dos bases de datos Postgres (negocio y pgvector);
- modelo de datos (Workspaces, Topics, Posts, Schedules, Ingested_Documents);
- 5 historias de usuario y tickets para las entregas 1 y 2.

Ese borrador fue la entrada del Prompt 2. Varias de sus decisiones se revisaron después: la arquitectura pasó a un monolito modular (§2.1), el modelo de datos pasó a ser multisitio con una única base de datos (§3) y las historias se reescribieron con criterios de aceptación (§5).

<details>
<summary>Respuesta completa de Gemini Pro</summary>

```text
* Operational Modes:

   * Human-in-the-Loop (Copilot): AI suggests topics -> User approves -> AI drafts -> User reviews/edits -> System publishes.

   * Automated (Autopilot): AI autonomously researches, drafts, and schedules based on predefined guardrails.

2. System Architecture
Your architecture follows a microservices pattern, separating the business layer from the heavy AI processing.

* Frontend (NextJS, React, Tailwind): Provides the UI for users to approve topics, edit generated drafts, and configure the WP connection.

* Backend API (Ruby on Rails): Acts as the central orchestrator. It handles user authentication, business logic, interactions with the WordPress REST API, and job queuing (using Redis + Sidekiq) for asynchronous posting and scheduling.

* Intelligence Service (FastAPI, Python 3.14): A dedicated internal microservice handling all AI workloads.

   * Uses LiteLLM to route requests to the best/cheapest LLMs.

   * Uses LangChain for RAG workflows and web search integration (researching trendy topics).

   * Uses uv for fast dependency management.

* Databases:

   * Postgres (Business): Stores users, WP credentials, post metadata, statuses, and schedules.

   * Postgres (pgvector): Stores vector embeddings of ingested WordPress posts to retrieve historical context, maintain tone, and prevent topic duplication.

3. Data Model (Core Entities)
Business DB (Postgres)

* `Workspaces`: id, name, wp_api_url, wp_auth_token, auto_mode (boolean)

* `Topics`: id, workspace_id, keyword, trend_score, status (suggested, approved, rejected)

* `Posts`: id, workspace_id, topic_id, title, content_draft, status (draft, pending_review, scheduled, published), scheduled_for

* `Schedules`: id, workspace_id, frequency (daily, weekly), preferred_time

Vector DB (pgvector)

* `Ingested_Documents`: id, workspace_id, original_post_url, text_chunk, embedding (vector), tone_metadata (jsonb)

4. User Stories (MVP Scope)
To satisfy the Master's requirement of 3-5 "must-have" user stories:

1. WP Integration: As an admin, I want to connect my WordPress site via URL and Application Password so the system can read historical posts and publish new ones.

2. Context Ingestion: As a system, I want to ingest the last 50 blog posts from a connected WP site, analyze their tone, and store them in the vector database to use as style references.

3. Topic Ideation: As a user, I want the system to research trending topics in my niche and present a list of ideas so I can approve the best ones for drafting.

4. Draft & Review: As an editor, I want the AI to generate a full blog post based on an approved topic and my site's historical tone, allowing me to manually edit the text before clicking "Publish".

5. Publishing: As the system, I want to securely push approved content to the WordPress API and return the live URL to the user dashboard.

5. Work Tickets (Delivery 1 & 2 Roadmap)
Sprint 1: Foundation & Delivery 1 Setup (Due Today)

* [ ] `DOC-01`: Create Fork, branch `feature/entrega-1-INITIALS`, and fill `README.md`.

* [ ] `DOC-02`: Submit Typeform for Delivery 1.

Sprint 2: Infrastructure & Connections

* [ ] `INFRA-01`: Initialize Rails API with Postgres and Redis.

* [ ] `INFRA-02`: Initialize FastAPI service with uv, LangChain, LiteLLM, and pgvector.

* [ ] `INFRA-03`: Initialize NextJS frontend with Tailwind.

Sprint 3: Core Features (Targeting Oct 23 - Delivery 2)

* [ ] `FEAT-01`: Build WP API connection module in Rails (fetch posts, create posts).

* [ ] `FEAT-02`: Create FastAPI endpoint for content ingestion (chunking and vectorizing WP posts).

* [ ] `FEAT-03`: Create FastAPI endpoint for topic research (using LangChain + search tools).

* [ ] `FEAT-04`: Create FastAPI endpoint for drafting content (RAG against pgvector for tone).

* [ ] `FEAT-05`: Build NextJS UI views: Topic Approval Dashboard and Markdown/WYSIWYG Editor.
```

</details>

**Prompt 2:**

*Herramienta:* Claude Code (Claude Opus 5.5), modo `/opsx:explore`.

```
/opsx:explore working on the following for this repo:

[Respuesta de Gemini Pro al Prompt 1, pegada tal cual, con anotaciones sobre qué
pasos de sus «Next Steps» estaban hechos y en cuál se necesitaba ayuda]

This is the initial idea for the project so help me define how this multi-repo should be setup as a greenfield project.
```

*Resultado:* el asistente inspeccionó el repositorio. Con eso confirmó que la Entrega 1 es una PR con `readme.md` y `prompts.md`, y detectó que el hook `pre-push` de git-trello rechazaría la rama de entrega. Comparó el borrador con la plantilla y señaló qué secciones quedaban sin cubrir: UX, instalación, estructura, infraestructura, seguridad, tests y API. También encontró huecos en el modelo de datos (sin tabla de usuarios, credenciales de WordPress mal modeladas, sin URL publicada ni zona horaria) y en las historias ("Como sistema…" no es una historia de usuario). Los hallazgos se trasladaron a la sección 1 y al resto del documento.

**Prompt 3:**

---

## 2. Arquitectura del Sistema

### **2.1. Diagrama de arquitectura:**

**Prompt 1:**

```
1. Spanish
2. one repo with several services, but I would like to explore a Ruby on Rails monolith for the MVP too and to simplify things, and just one postgres DB
3. Yes, it is part of the MVP
4. Solid Queue
5. real roles
```

*Contexto:* respuestas a cinco preguntas del asistente: idioma de la documentación, organización del repositorio, si Autopilot entra en el MVP, cola de trabajos (Solid Queue frente a Sidekiq + Redis) y modelo de roles.

*Resultado:* se compararon tres arquitecturas: A, monolito modular Rails; B, Rails + FastAPI; C, el borrador original de tres servicios. Se diseñó la costura `Intelligence` (ingest, research, draft, evaluate) para poder extraer la IA más adelante. También se definieron los modos de Autopilot (off, review, auto) con sus guardrails y la matriz de roles admin/editor. Además, se detectaron dos riesgos: que `rails new` en la raíz sobrescribiera `readme.md`, y que Rails 8 crea por defecto bases de datos separadas para Solid Queue.

**Prompt 2:**

```
1. A
2. Inertia with React and Tailwind

Know that I should be able to register multiple WP sites if necessary so there should not be a limitation to be linked to a single wordpress site
```

*Contexto:* elección entre las arquitecturas A, B y C, y entre Hotwire e Inertia + React para la interfaz.

*Resultado:* arquitectura final documentada en §2.1 y §2.2: monolito modular Rails 8 con interfaz React + Inertia + Tailwind, Solid Queue y una única base de datos PostgreSQL con pgvector. Incluye la justificación, los beneficios, los sacrificios, las alternativas descartadas y la evolución prevista hacia un servicio FastAPI.

**Prompt 3:**

```
simplify the §2.1 diagram labels (fewer <br/> / special chars)
```

*Contexto:* fragmento del Prompt 1 de §4.

*Resultado:* el diagrama de §2.1 se reescribió con etiquetas cortas de una línea, sin `<br/>`, sin el separador `·` y sin flechas bidireccionales. El diagrama de §2.4 se simplificó por el mismo motivo. Los detalles que salieron de los diagramas ya estaban en el texto y en la tabla de componentes (§2.2).

### **2.2. Descripción de componentes principales:**

**Prompt 1:**

**Prompt 2:**

**Prompt 3:**

### **2.3. Descripción de alto nivel del proyecto y estructura de ficheros**

**Prompt 1:**

```
This is the initial idea for the project so help me define how this multi-repo should be setup as a greenfield project.
```

*Contexto:* fragmento final del Prompt 2 de la sección 1.

*Resultado:* se recomendó un monorepo frente a varios repositorios. Las entregas son PRs desde ramas de este fork, y cada funcionalidad afecta a la vez a interfaz, lógica e IA. El resultado es la estructura de §2.3: la aplicación en `apps/platform`, con espacio para futuras aplicaciones, y el modelo de ramas que combina las ramas de Trello con las ramas `feature/entrega-N-OHSA`.

**Prompt 2:**

**Prompt 3:**

### **2.4. Infraestructura y despliegue**

**Prompt 1:**

**Prompt 2:**

**Prompt 3:**

### **2.5. Seguridad**

**Prompt 1:**

**Prompt 2:**

**Prompt 3:**

### **2.6. Tests**

**Prompt 1:**

**Prompt 2:**

**Prompt 3:**

---

### 3. Modelo de Datos

**Prompt 1:**

```
Know that I should be able to register multiple WP sites if necessary so there should not be a limitation to be linked to a single wordpress site
```

*Contexto:* requisito añadido en el Prompt 2 de §2.1, junto con la decisión de usar una única base de datos PostgreSQL (Prompt 1 de §2.1).

*Resultado:* se separó `workspaces` (equipo y roles) de `sites` (cada WordPress conectado). Todo el contenido, el estilo, el calendario y la configuración de Autopilot pasaron a depender del sitio. Se añadieron `memberships`, `invitations`, `style_profiles`, `source_posts`, `document_chunks` y `runs`. Con una sola base de datos, los embeddings tienen claves foráneas reales y borrado en cascada.

**Prompt 2:**

**Prompt 3:**

---

### 4. Especificación de la API

**Prompt 1:**

```
Local preview stress (not a syntax break at §2)
Nothing at ## 2. Arquitectura del Sistema closes or corrupts the document. After that heading the file gets heavy: complex Mermaid (subgraphs, <br/>, Unicode ·), then large ER diagrams and a ~500-line OpenAPI yaml block. Cursor’s Markdown preview can stall or look “cut off” when it hits that load — even when the Mermaid itself is valid.

To stabilize local preview (optional): simplify the §2.1 diagram labels (fewer <br/> / special chars) or move the huge OpenAPI block to docs/api/openapi.yaml and link it.

update prompts based on this interaction too
```

*Contexto:* la vista previa Markdown de Cursor se bloqueaba o parecía cortada a partir de §2, aunque los diagramas eran válidos. El diagnóstico se pegó en la sesión de Claude Code.

*Resultado:*

- La especificación OpenAPI completa (516 líneas) se movió a `docs/api/openapi.yaml`, sin cambios en su contenido.
- En §4 quedan una tabla con los tres endpoints, su comportamiento y ejemplos de petición y respuesta, de modo que `readme.md` pasó de unas 1.980 a unas 1.500 líneas.
- Se comprobó que el fichero sigue siendo YAML válido.
- Los diagramas de §2.1 y §2.4 se simplificaron (ver Prompt 3 de §2.1). Los siete diagramas del documento se volvieron a renderizar con Mermaid 11 sin errores.

**Prompt 2:**

**Prompt 3:**

---

### 5. Historias de Usuario

**Prompt 1:**

```
3. Yes, it is part of the MVP
...
5. real roles
```

*Contexto:* puntos 3 y 5 del Prompt 1 de §2.1.

*Resultado:* Autopilot pasó a ser una historia Must (HU-06), con criterios de aceptación sobre calendario, límites, pausa automática y auditoría. Los roles reales dieron lugar a HU-07 (invitaciones y roles). Además, las personas admin y editor de las historias pasaron a ser roles reales del producto.

**Prompt 2:**

**Prompt 3:**

---

### 6. Tickets de Trabajo

**Prompt 1:**

```
/opsx:explore readme.md tickets for MVP documented as OpenSpec changes.
we need to explore every ticket and break down into multiple changes if necessary to keep them well scoped following a hight standard in the SDLC. This will help to provide more detail in the changes themselves and reduce the readme.md size by only linking to the defined changes.
```

*Resultado:* el asistente revisó el estado del repositorio y señaló tres condicionantes:

- `openspec/` estaba en `.gitignore`, así que los enlaces desde el README darían 404 en GitHub.
- Al archivar un cambio se mueve de carpeta, y su enlace deja de funcionar; el destino estable es la especificación resultante.
- La plantilla pide los tres tickets con detalle en el propio README.

Propuso sustituir los tickets divididos por capa (DB-01 como esquema completo, BE-01, FE-01) por cambios que cubren cada uno una capacidad de principio a fin y son dueños de sus propias migraciones. El resultado fue un desglose en 16 cambios, con sus dependencias y dos olas de entrega (Entrega 2 y Entrega 3).

**Prompt 2:**

```
1. done
2. English
3. follow recommendation
4. proposals for all 16 for now
5. if something needs to be breakdown more let me know, I'll support the idea
```

*Contexto:* respuestas a cinco preguntas: quitar `openspec/` del `.gitignore`, idioma de los artefactos, tickets resumidos en el README con enlace frente a solo enlaces, alcance de los artefactos y validez del desglose.

*Resultado:*

- `openspec/config.yaml` con el contexto del proyecto y reglas para propuestas, especificaciones y tareas.
- 16 propuestas, cada una con dependencias, lista de exclusiones y referencias a las historias y secciones del README.
- Un ajuste de orden: `add-task-runs` pasa después de la conexión de sitios, porque `runs` depende de `sites`.
- En `readme.md`, la §6 pasó a ser la lista de cambios, una definición de hecho común y tres tickets resumidos (base de datos, backend, frontend) que enlazan al cambio completo; el backlog de la §5 ganó una columna con los cambios de cada historia.

**Prompt 3:**

```
yes
```

*Contexto:* aprobación de los dos desgloses adicionales que propuso el asistente. Autopilot (13 puntos) era demasiado grande para un solo cambio, y el despliegue en producción no hace falta hasta tener URL pública.

*Resultado:* `add-autopilot` se sustituyó por `add-autopilot-pipeline` (comprobación cada 15 minutos, una ejecución por franja, modos review y auto) y `add-autopilot-guardrails` (límites semanales y de presupuesto, pausa automática, alertas y auditoría). El despliegue salió de `bootstrap-platform` a un cambio nuevo, `add-production-deployment`. En total quedan 18 cambios, y se actualizaron las referencias entre propuestas y la tabla de la §6.

---

### 7. Pull Requests

**Prompt 1:**

**Prompt 2:**

**Prompt 3:**
