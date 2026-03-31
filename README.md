# 🏛️ OTIC-GRA — Documentación Interna

> **Oficina de Tecnologías de la Información**  
> Gobierno Regional de Ayacucho  
> 📧 otic@regionayacucho.gob.pe

---

## 📋 Índice

- [Sistemas en Producción](#sistemas-en-producción)
- [Infraestructura](#infraestructura)
- [Stack Tecnológico](#stack-tecnológico)
- [Repositorios Clave](#repositorios-clave)
- [Accesos y Credenciales](#accesos-y-credenciales)
- [Guía de Despliegue](#guía-de-despliegue)
- [Estándares de Desarrollo](#estándares-de-desarrollo)
- [Contacto y Soporte](#contacto-y-soporte)

---

## 🖥️ Sistemas en Producción

| Sistema | Descripción | URL | Estado |
|---------|-------------|-----|--------|
| **Portal GRA** | Portal web oficial del Gobierno Regional | `regionayacucho.gob.pe` | ✅ Activo |
| **SGD-GRA** | Sistema de Gestión Documental (Java/Payara) | Intranet | ✅ Activo |
| **File Service** | Servicio de archivos con MinIO/S3 (Go) | Interno | ✅ Activo |
| **Zitadel SSO** | Autenticación centralizada para todos los sistemas | Interno | ✅ Activo |
| **Semana Santa** | Sitio web oficial de Semana Santa Ayacucho | `semanasanta.regionayacucho.gob.pe` | ✅ Activo |
| **geobras** | Sistema de georeferenciación de obras públicas | En desarrollo | 🔄 Dev |

---

## 🏗️ Infraestructura

### Servidor y Despliegue
- **Orquestación:** [Dokploy](https://dokploy.com) sobre VPS
- **Proxy inverso:** Nginx Proxy Manager
- **Contenedores:** Docker Compose (stacks separados por servicio)
- **SSL:** Sectigo / Let's Encrypt (renovación automática)

### Almacenamiento
- **Object Storage:** MinIO (compatible S3)
- **Base de datos principal:** PostgreSQL 15+
- **PostGIS:** Habilitado en instancia para `geobras`

### Backups
- **Base de datos SGD (`IDOSGD_GRA`):** ~542 GB — backup automático con `pg_dump -F d -j 4`
- **Destinos:** NAS local + almacenamiento remoto
- **Repo de scripts:** [`sgd-db-backup`](https://github.com/GRA-OTIC/sgd-db-backup)

### Autenticación
- **SSO:** Zitadel — gestiona todos los sistemas internos
- **Protocolo:** OIDC / OAuth2
- ⚠️ Ante cualquier problema de acceso admin, contactar al equipo OTIC antes de intentar recuperación manual.

---

## ⚙️ Stack Tecnológico

### Backend
- **Lenguaje principal:** Go (Fiber framework)
- **Legacy:** Java 8 / Payara (SGD-PCM)
- **APIs:** REST + WebSocket (tramitedoc-agent)
- **Autenticación de servicios:** HMAC-SHA256

### Frontend
- **Framework principal:** Next.js (App Router)
- **Sites estáticos:** Astro 6
- **Animaciones/3D:** Three.js, GSAP, WebGL

### Base de Datos
- **Principal:** PostgreSQL
- **Extensiones:** PostGIS (geobras)
- **Migraciones:** Manual / scripts SQL versionados

### DevOps
- **CI/CD:** GitHub + Dokploy webhooks
- **Documentación API:** Bruno + Scalar UI
- **Automatización:** n8n
- **Notificaciones:** WAHA (WhatsApp)

---

## 📁 Repositorios Clave

| Repo | Descripción | Lang |
|------|-------------|------|
| `sgd` | Frontend SGD — Next.js | TypeScript |
| `geobras` | Georeferenciación de obras + PostGIS | TypeScript |
| `semana-santa` | Sitio Semana Santa Ayacucho (Astro 6 + WebGL) | Astro |
| `sgd-db-backup` | Scripts de backup automatizado PostgreSQL | Shell |
| `sisplan-2026` | Sistema de planificación 2026 | HTML |

> Ver todos los repositorios: [github.com/orgs/GRA-OTIC/repositories](https://github.com/orgs/GRA-OTIC/repositories)

---

## 🔐 Accesos y Credenciales

> ⚠️ **NUNCA** almacenar contraseñas, tokens o secrets en este README ni en repositorios.  
> Todos los accesos se gestionan vía Zitadel SSO o por solicitud directa al equipo OTIC.

**Para solicitar acceso a un sistema:**
1. Enviar correo a `otic@regionayacucho.gob.pe`
2. Indicar sistema requerido y justificación
3. El equipo OTIC creará el usuario en Zitadel

**Variables de entorno y secrets:**
- Se gestionan directamente en Dokploy por ambiente (dev/prod)
- No se versionan en git (`.env` está en `.gitignore` en todos los repos)

---

## 🚀 Guía de Despliegue

### Flujo estándar

```bash
# 1. Clonar el repositorio
git clone https://github.com/GRA-OTIC/<repo>.git

# 2. Copiar variables de entorno
cp .env.example .env
# Editar .env con los valores del ambiente

# 3. Levantar servicios
docker compose up -d

# 4. Verificar logs
docker compose logs -f
```

### Despliegue en producción (Dokploy)
- Todo despliegue a producción se realiza mediante **Dokploy**
- Configurar el webhook de GitHub en el servicio correspondiente
- Los cambios en `main` disparan el redeploy automáticamente

### Migración de archivos (bytea → MinIO)
- Ver script ETL en Python disponible internamente
- Proceso: extrae binarios de PostgreSQL `bytea` y los sube al file service
- Demo DB: 81 GB → 522 MB tras migración

---

## 📐 Estándares de Desarrollo

### Git
- Branch principal: `main`
- Features: `feat/<descripcion>`
- Fixes: `fix/<descripcion>`
- Commits en español o inglés, descriptivos

### Código
- **Go:** seguir `gofmt` + `golint`; documentar paquetes públicos con GoDoc
- **TypeScript/Next.js:** ESLint + Prettier; usar Server Actions para mutaciones
- **Notificaciones UI:** usar `sonner` (`toast`) — NO `useToast` hook
- **Imágenes en Next.js:** preferir `<Image>` de next/image; agregar `eslint-disable` si se usa `<img>` con justificación

### APIs
- Documentar con **Bruno** (colecciones en el repo)
- Exponer docs con **Scalar UI** integrado en el servidor Go
- Autenticación: JWT vía Zitadel en todos los endpoints protegidos

---

## 📞 Contacto y Soporte

| Rol | Contacto |
|-----|----------|
| Equipo OTIC | `otic@regionayacucho.gob.pe` |
| Incidencias críticas | Contacto directo vía WhatsApp (ver directorio interno) |
| Repositorios | [github.com/GRA-OTIC](https://github.com/GRA-OTIC) |

---

*Última actualización: 2026 · OTIC — Gobierno Regional de Ayacucho*
