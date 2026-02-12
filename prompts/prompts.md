# Registro de Prompts

Este documento contiene una recopilación secuencial de los prompts utilizados en el proyecto, formateados para una mejor lectura y referencia.

---

## 1. Prompt: Ejecución y Despliegue del Sistema

**Objetivo:** Actuar como ingeniero experto en despliegues para levantar todo el stack tecnológico del monorepo.

### Instrucciones del Usuario
- **Rol:** Ingeniero experto en despliegues.
- **Contexto:** Información contenida en el `README.md` del monorepo.
- **Tarea Principal:** Ejecutar el software con todos sus componentes:
    - Base de Datos (BD)
    - Backend (Back)
    - Frontend (Front)
- **Requisito Previo:** Docker Desktop ya está corriendo.
- **Entregable:** Notificación una vez que todo esté arriba y funcionando.

---

## 2. Prompt: Conexión SSH a Instancia EC2

**Objetivo:** Verificar la conectividad mediante SSH a la instancia de Amazon EC2 utilizando las credenciales y llaves proporcionadas en el archivo `.env`.

### Instrucciones del Usuario
- **Tarea:** Comprobar la conexión por SSH a la instancia EC2.
- **Contexto:** Información necesaria contenida en el archivo `.env` (IP, usuario y llave privada).
- **Requisito de Visualización:** Ejecutar en una terminal visible para seguimiento del proceso.

---

## 3. Prompt: Validación de Pull Request Abierto (Gate Job)

**Objetivo:** Generar un job de validación en GitHub Actions que determine si la rama actual tiene un PR abierto antes de proceder con el despliegue.

### Instrucciones del Usuario
- **Tarea:** Crear un job inicial tipo "gate" que consulte la API de GitHub mediante el CLI `gh`.
- **Lógica:** Verificar si existe al menos un Pull Request abierto donde el `head` coincida con la rama que disparó el `push`.
- **Salida:** Establecer un output `has_pr` con valores "true" o "false".
- **Restricciones de Seguridad:** Asegurar que el pipeline finalice correctamente si no hay PR, sin marcar el workflow como fallido.

---

## 4. Prompt: Ejecución de Tests del Backend

**Objetivo:** Configurar el entorno y ejecutar las pruebas automatizadas del backend en Node.js.

### Instrucciones del Usuario
- **Tarea:** Generar un job de pruebas para un proyecto Node.js v20.
- **Requisitos:** 
    - Usar Node.js versión 20.
    - Implementar caché de `npm` para mejorar el rendimiento.
    - Ejecutar comandos dentro del directorio `backend/`.
- **Comandos:** Usar `npm ci` y `npm test`.
- **Restricciones:** El job solo debe ejecutarse si el job de validación (`gate`) confirma un PR abierto. No inventar comandos si no existen en el `package.json`.

---

## 5. Prompt: Proceso de Build del Backend

**Objetivo:** Compilar el código fuente TypeScript y preparar los artefactos para el despliegue.

### Instrucciones del Usuario
- **Tarea:** Crear un job que realice el build del backend.
- **Pasos:**
    - Instalar dependencias con `npm ci`.
    - Ejecutar el comando de construcción `npm run build`.
    - Persistir la carpeta de salida `dist/` usando `actions/upload-artifact`.
- **Restricciones:** El job debe depender del éxito de los tests. Usar un tiempo de retención corto para el artefacto para optimizar almacenamiento.

---

## 6. Prompt: Despliegue en AWS EC2 mediante SSH

**Objetivo:** Transferir los artefactos compilados a una instancia EC2 y reiniciar el servicio de forma segura utilizando SSH.

### Instrucciones del Usuario
- **Tarea:** Generar un job de despliegue que use SSH y SCP.
- **Secrets:** Utilizar `EC2_SSH_PRIVATE_KEY`, `EC2_INSTANCE`, `EC2_USER`, `AWS_ACCESS_ID` y `AWS_ACCESS_KEY`.
- **Seguridad:** 
    - Usar `ssh-agent` para cargar la clave privada de forma segura.
    - Configurar correctamente `known_hosts` usando `StrictHostKeyChecking=accept-new`.
- **Acciones en el servidor:**
    - Copiar archivos compilados y archivos de configuración node (`package.json`).
    - Ejecutar `npm install --production` en remoto.
    - Reiniciar el servicio backend (usar PM2 como placeholder para el reinicio).

---
