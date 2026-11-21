# 🛡️ Análisis de Control de Acceso (Broken Access Control - OWASP Top 10)

Este repositorio documenta el análisis técnico, vectores de ataque y estrategias de mitigación correspondientes a 4 escenarios prácticos de control de acceso en aplicaciones web, desarrollados en entornos controlados de **PortSwigger Web Security Academy**.

---

## 📋 Resumen de Escenarios Analizados

| # | Vector / Vulnerabilidad | Causa Raíz Técnica | Impacto | Remediación Principal |
| :--- | :--- | :--- | :--- | :--- |
| **01** | **Exposición de rutas en archivos de indexación** | Confianza en "seguridad por oscuridad" al listar paneles administrativos en `/robots.txt` sin control de sesión. | Acceso no autorizado a funciones críticas. | Implementar validación de rol en el *backend* (RBAC) antes de servir rutas administrativas. |
| **02** | **Lógica de autorización en el cliente (Frontend)** | Envío de rutas administrativas ocultas dentro del código JavaScript público cargado en el navegador. | Descubrimiento y acceso directo a paneles privilegiados. | Nunca exponer lógica de autorización ni rutas internas en el *frontend*; validar permisos en el servidor. |
| **03** | **Manipulación de cookies de sesión sin firma** | Almacenamiento de privilegios en cookies editables en texto plano (`Admin=false/true`) sin validación en servidor. | Escalada de privilegios vertical a administrador. | Gestionar roles exclusivamente en base de datos mediante tokens de sesión firmados e impredecibles (`HttpOnly`, `Secure`). |
| **04** | **Asignación Masiva (Mass Assignment)** | El servidor vincula directamente parámetros JSON enviados por el cliente (`roleid`) hacia el modelo interno de usuario. | Modificación no autorizada de privilegios de cuenta. | Aplicar listas blancas (*Allowlisting*) y DTOs para filtrar estrictamente los campos editables por el usuario. |

---

## 🔍 Análisis Arquitectónico y Defensa (Blue Team)

### 1. El fallo estructural común
En los cuatro escenarios evaluados, la vulnerabilidad surge por delegar decisiones de confianza al cliente (navegador, cookies o parámetros de petición) o asumir que ocultar una ruta equivale a protegerla.

### 2. Controles de Mitigación Recomendados
* **Principio de Denegación por Defecto:** Todo *endpoint* administrativo debe rechazar peticiones con `403 Forbidden` a menos que el servidor verifique explícitamente el rol asociado a la sesión activa.
* **Integridad de Sesión:** Uso obligatorio de identificadores de sesión opacos y firmados criptográficamente, acompañados de los atributos `HttpOnly`, `Secure` y `SameSite=Strict`.
* **Filtrado Estricto de Entradas (API Security):** Separar los objetos de transferencia de datos (DTO) que recibe la API de los modelos internos de base de datos, ignorando cualquier atributo privilegiado no solicitado.

---

## 🛠️ Herramientas Utilizadas
* **Inspección y Análisis HTTP:** Chrome DevTools (`Network`, `Application`, `Sources`, `Console`).
* **Entorno de Pruebas:** PortSwigger Web Security Academy (Access Control Module).

