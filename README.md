# BarrioConnect

> Plataforma de economía hiperlocal que conecta micro-comercios de barrio con sus vecinos.

[![Node.js](https://img.shields.io/badge/Node.js-20.x-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-16-4169E1?logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![Express](https://img.shields.io/badge/Express-4-000000?logo=express&logoColor=white)](https://expressjs.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/status-MVP%20Sprint%201-orange)]()

---

## Tabla de Contenidos

- [Acerca del Proyecto](#acerca-del-proyecto)
- [Stack Tecnológico](#stack-tecnológico)
- [Arquitectura](#arquitectura)
- [Estructura del Repositorio](#estructura-del-repositorio)
- [Requisitos Previos](#requisitos-previos)
- [Instalación](#instalación)
- [Variables de Entorno](#variables-de-entorno)
- [Scripts Disponibles](#scripts-disponibles)
- [Endpoints de la API](#endpoints-de-la-api)
- [Modelo de Datos](#modelo-de-datos)
- [Estrategia de Ramas](#estrategia-de-ramas)
- [Convención de Commits](#convención-de-commits)
- [Calidad de Código](#calidad-de-código)
- [Roadmap](#roadmap)
- [Equipo](#equipo)
- [Licencia](#licencia)

---

## Acerca del Proyecto

**BarrioConnect** es una plataforma web que permite a los micro-comerciantes (panaderos, fruteros, ferreteros, artesanos, plomeros) tener una vitrina digital sin pagar comisiones, y a los vecinos descubrir y contactar negocios cercanos a su ubicación. La transacción se cierra directamente vía WhatsApp, sin intermediarios.

### El Problema

Los pequeños comerciantes locales enfrentan una **brecha de visibilidad**: las grandes plataformas priorizan a quienes pagan publicidad masiva, mientras que el zapatero o el panadero del barrio dependen exclusivamente del tráfico peatonal físico.

### La Solución

Una aplicación **mobile-first** que:

- Permite al comerciante registrar su negocio y publicar productos en menos de 5 minutos.
- Permite al vecino buscar productos por **cercanía geográfica** (geolocalización).
- Conecta a ambos por **WhatsApp** sin comisiones intermediarias.
- Funciona como una vitrina 24/7 sin necesidad de pauta publicitaria.

### Contexto Académico

Este proyecto se desarrolla como **Proyecto de Software** de la **Corporación Universitaria Iberoamericana** bajo la dirección de la docente Tatiana Cabrera, siguiendo metodología ágil Scrum con Sprints de dos semanas.

---

## Stack Tecnológico

| Capa | Tecnología | Versión |
|------|------------|---------|
| Frontend | React + Vite + Tailwind CSS | 18 / 5 / 3 |
| Estado | Zustand | 4.x |
| Backend | Node.js + Express | 20 / 4 |
| Base de Datos | PostgreSQL + PostGIS | 16 |
| Autenticación | JSON Web Tokens (JWT) + bcrypt | — |
| Imágenes | Cloudinary | — |
| Geocodificación | Google Maps API | — |
| Calidad | ESLint + Prettier + Husky + lint-staged | — |
| CI/CD | GitHub Actions | — |
| Hosting (planeado) | Vercel (frontend) + Render (backend) | — |

---

## Arquitectura

El sistema sigue una arquitectura **Cliente-Servidor con API REST**, documentada con el modelo **C4** (Niveles 1 y 2).

```
┌─────────────┐         ┌─────────────┐         ┌─────────────┐
│   Vecino    │         │ Comerciante │         │    Admin    │
│  (Cliente)  │         │ (Vendedor)  │         │             │
└──────┬──────┘         └──────┬──────┘         └──────┬──────┘
       │                       │                       │
       │              HTTPS    │                       │
       └───────────────────────┴───────────────────────┘
                               │
                ┌──────────────▼───────────────┐
                │   SPA (React + Vite)         │
                │   Single Page Application    │
                └──────────────┬───────────────┘
                               │ REST / JSON
                ┌──────────────▼───────────────┐
                │   API (Node.js + Express)    │
                │   Auth · Stores · Products   │
                └──────┬─────────┬─────────┬───┘
                       │         │         │
              ┌────────▼──┐ ┌────▼────┐ ┌──▼──────┐
              │PostgreSQL │ │Cloudinary│ │ G. Maps │
              │   + PostGIS│ │  Images  │ │Geocode  │
              └───────────┘ └─────────┘ └─────────┘
```

Los diagramas C4 completos en notación **Structurizr DSL** están en `docs/c4/`.

---

## Estructura del Repositorio

```
barrioconnect/
├── README.md
├── .gitignore
├── .editorconfig
├── .nvmrc                       # Versión Node.js (20.x)
├── package.json                 # Workspaces npm (frontend + backend)
├── .husky/                      # Git hooks
├── .github/workflows/ci.yml     # Pipeline lint + test
├── docs/
│   ├── adr/                     # Architecture Decision Records
│   ├── c4/                      # Diagramas C4 (Structurizr DSL)
│   └── api/                     # Especificación OpenAPI / Swagger
├── frontend/
│   ├── package.json
│   ├── vite.config.js
│   ├── tailwind.config.js
│   ├── public/
│   └── src/
│       ├── main.jsx
│       ├── App.jsx
│       ├── api/                 # Cliente Axios + interceptors JWT
│       ├── components/          # Componentes reutilizables
│       ├── pages/               # Login, Catalog, ProductDetail, etc.
│       ├── store/               # Stores Zustand
│       ├── hooks/               # Custom hooks
│       └── utils/
└── backend/
    ├── package.json
    ├── .env.example
    └── src/
        ├── server.js
        ├── config/              # db, env
        ├── routes/
        ├── controllers/
        ├── services/
        ├── repositories/
        ├── middlewares/         # auth, errorHandler, validators
        ├── models/              # Esquemas SQL y migraciones
        └── utils/
```

---

## Requisitos Previos

- **Node.js** 20.x (se recomienda gestionarlo con [nvm](https://github.com/nvm-sh/nvm))
- **npm** 10.x
- **PostgreSQL** 16 con la extensión **PostGIS** habilitada
- Cuenta de **Cloudinary** (free tier funciona)
- API Key de **Google Maps** (Geocoding API habilitada)
- **Git**

---

## Instalación

### 1. Clonar el repositorio

```bash
git clone https://github.com/marcelosalas2325-prog/barrioconnect.git
cd barrioconnect
```

### 2. Usar la versión correcta de Node.js

```bash
nvm use
```

### 3. Instalar dependencias del monorepo

```bash
npm install
```

### 4. Configurar variables de entorno

```bash
cd backend
cp .env.example .env
# Editar .env con los valores reales (ver sección "Variables de Entorno")
cd ..
```

### 5. Crear la base de datos y correr migraciones

```bash
cd backend
npm run db:create
npm run db:migrate
npm run db:seed       # Datos de prueba
cd ..
```

### 6. Arrancar en desarrollo

Desde la raíz del proyecto:

```bash
npm run dev
```

Esto levanta en paralelo:

- **Frontend** en `http://localhost:5173`
- **Backend** en `http://localhost:4000`
- **Swagger UI** en `http://localhost:4000/api/docs`

---

## Variables de Entorno

Archivo `backend/.env.example`:

```bash
# Aplicación
NODE_ENV=development
PORT=4000
FRONTEND_URL=http://localhost:5173

# Base de datos
DATABASE_URL=postgresql://user:password@localhost:5432/barrioconnect

# Autenticación
JWT_SECRET=<cadena_aleatoria_64_caracteres>
JWT_REFRESH_SECRET=<cadena_aleatoria_64_caracteres>
JWT_EXPIRES_IN=24h
JWT_REFRESH_EXPIRES_IN=7d

# Cloudinary (almacenamiento de imágenes)
CLOUDINARY_CLOUD_NAME=<cloud_name>
CLOUDINARY_API_KEY=<api_key>
CLOUDINARY_API_SECRET=<api_secret>

# Google Maps (geocodificación)
GOOGLE_MAPS_API_KEY=<api_key>

# Rate Limiting
RATE_LIMIT_MAX=100
RATE_LIMIT_WINDOW_MS=900000
```

> **IMPORTANTE**: Nunca subas el archivo `.env` al repositorio. Ya está incluido en `.gitignore`.

---

## Scripts Disponibles

Desde la raíz del proyecto:

| Comando | Descripción |
|---------|-------------|
| `npm run dev` | Levanta frontend + backend en paralelo |
| `npm run dev:frontend` | Solo frontend (Vite) |
| `npm run dev:backend` | Solo backend (Nodemon) |
| `npm run build` | Build de producción (ambos) |
| `npm run lint` | ESLint en todo el monorepo |
| `npm run format` | Formatea con Prettier |
| `npm test` | Ejecuta suite de tests con Vitest |
| `npm run db:migrate` | Aplica migraciones pendientes |
| `npm run db:rollback` | Revierte la última migración |
| `npm run db:seed` | Inserta datos de prueba |

---

## Endpoints de la API

Documentación completa en Swagger UI: `http://localhost:4000/api/docs`

### Autenticación

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/auth/register` | Crear cuenta (cliente o vendedor) | ❌ |
| POST | `/api/auth/login` | Iniciar sesión, devuelve JWT | ❌ |
| POST | `/api/auth/refresh` | Renovar access token | ❌ |
| POST | `/api/auth/logout` | Cerrar sesión | ✅ |

### Negocios

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/stores` | Listar negocios (filtros: category, lat, lng, radius) | ❌ |
| GET | `/api/stores/:id` | Detalle de un negocio | ❌ |
| POST | `/api/stores` | Crear negocio | ✅ Vendor |
| PATCH | `/api/stores/:id` | Editar negocio | ✅ Vendor |
| DELETE | `/api/stores/:id` | Eliminar negocio (soft delete) | ✅ Vendor |

### Productos

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/products` | Buscar productos (q, category) | ❌ |
| GET | `/api/products/:id` | Detalle de producto | ❌ |
| POST | `/api/products` | Crear producto (con imagen) | ✅ Vendor |
| PATCH | `/api/products/:id` | Editar producto | ✅ Vendor |
| DELETE | `/api/products/:id` | Eliminar producto | ✅ Vendor |

### Favoritos

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/favorites` | Listar favoritos del usuario | ✅ |
| POST | `/api/favorites` | Marcar negocio como favorito | ✅ |
| DELETE | `/api/favorites/:storeId` | Quitar de favoritos | ✅ |

---

## Modelo de Datos

```sql
users          (user_id PK, email UNIQUE, password_hash, role, created_at)
stores         (store_id PK, user_id FK, name, description, address,
                latitude, longitude, whatsapp, category_id FK, active)
products       (product_id PK, store_id FK, name, description,
                price, stock, image_url, category_id FK, active)
categories     (category_id PK, name, icon)
favorites      (user_id FK, store_id FK, created_at, PK compuesta)
messages       (message_id PK, user_id FK, store_id FK, product_id FK, created_at)
```

Las migraciones SQL completas están en `backend/src/models/migrations/`.

---

## Estrategia de Ramas

Se sigue un **Git Flow simplificado**:

| Rama | Propósito | Reglas |
|------|-----------|--------|
| `main` | Producción estable | Protegida. Solo PR desde `develop` con CI verde. |
| `develop` | Integración continua | Rama base de desarrollo. |
| `feature/<descripcion>` | Nueva funcionalidad | Ej: `feature/login-jwt`. Una HU por rama. |
| `fix/<descripcion>` | Corrección de bug | Se mergea a `develop`. |
| `hotfix/<descripcion>` | Corrección crítica en prod | Sale de `main`, vuelve a `main` y `develop`. |
| `release/v<numero>` | Preparación de versión | Solo correcciones menores y bumps. |

---

## Convención de Commits

Se usa [Conventional Commits](https://www.conventionalcommits.org/):

```
feat(auth): implementar login con JWT y refresh tokens
fix(catalog): corregir filtro por categoría en móvil
docs(adr): añadir ADR-008 sobre herramientas de calidad
refactor(api): extraer lógica de geocodificación a servicio
test(auth): cubrir flujo de registro con casos límite
chore(deps): actualizar express a 4.19.2
style(ui): ajustar espaciado en tarjetas de producto
perf(catalog): paginar resultados de búsqueda
```

Tipos válidos: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`, `ci`, `build`.

---

## Calidad de Código

### Pre-commit Hooks (Husky + lint-staged)

Antes de cada commit se ejecuta automáticamente:

- ESLint con autofix sobre archivos `.js` / `.jsx` modificados.
- Prettier sobre archivos modificados.
- Validación del mensaje de commit (Conventional Commits).

### CI Pipeline (GitHub Actions)

En cada `push` y `pull_request`:

- Lint del código.
- Tests unitarios con Vitest.
- Build del frontend.

---

## Roadmap

### ✅ Sprint 1 — Fundamentación (completado)

- [x] Configuración del entorno DevOps
- [x] Diseño de la base de datos (PostgreSQL + PostGIS)
- [x] Módulo de autenticación con JWT y refresh tokens
- [x] Pruebas de usabilidad con 5 usuarios (SUS = 81.5/100)
- [x] Diagramas C4 (Niveles 1 y 2)
- [x] ADRs documentados

### 🚧 Sprint 2 — Funcionalidad Core (en curso)

- [ ] Catálogo de productos con búsqueda y filtros
- [ ] Integración con Cloudinary para imágenes
- [ ] Migrar lógica de cercanía a PostGIS (`ST_DWithin`)
- [ ] Cobertura de tests al 60% en módulos críticos
- [ ] Despliegue en staging (Render + Vercel)

### 📋 Sprint 3 — Interacción y Despliegue

- [ ] Geolocalización del navegador y filtros por radio
- [ ] Integración WhatsApp con `wa.me` y mensaje pre-rellenado
- [ ] Sistema de favoritos
- [ ] Despliegue en producción

### 🔮 Backlog (post-MVP)

- [ ] Modo oscuro
- [ ] Variantes de productos (talla, color)
- [ ] Duplicar producto desde menú contextual
- [ ] Notificaciones push
- [ ] Pasarela de pagos (versión 2.0)
- [ ] Migración progresiva a TypeScript

---

## Equipo

| Nombre | Rol | Contacto |
|--------|-----|----------|
| **David Marcelo Salas Barragán** | Product Owner / Scrum Master / Dev Team | Cód. 100098339 |
| **Tatiana Cabrera** | Docente — Facilitadora del proyecto | Corporación Universitaria Iberoamericana |

---

## Documentación Adicional

- 📐 **Diagramas C4**: [`docs/c4/`](./docs/c4/)
- 📝 **ADRs (Architecture Decision Records)**: [`docs/adr/`](./docs/adr/)
- 📊 **Especificación OpenAPI**: [`docs/api/openapi.yaml`](./docs/api/openapi.yaml)
- 🎨 **Prototipo Figma**: [Enlace al prototipo navegable](#) *(solicitar acceso)*

---

## Licencia

Distribuido bajo la licencia MIT. Ver [`LICENSE`](./LICENSE) para más detalles.

---

## Reconocimientos

- Comunidad de comerciantes locales del barrio que participaron en las pruebas de usabilidad.
- [Simon Brown](https://c4model.com/) por el modelo C4.
- [Michael Nygard](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) por la propuesta de ADRs.
- [Conventional Commits](https://www.conventionalcommits.org/) por el estándar de mensajes.

---

<div align="center">

**Hecho con ❤️ en Bucaramanga, Colombia**

*Apoyando la economía local, un barrio a la vez.*

</div>
