# NomiPlus

**Sistema empresarial para la gestión de asistencia y el soporte de procesos de nómina, desarrollado con React, Node.js y Oracle Database.**

NomiPlus reúne la administración de usuarios, el control de acceso por módulos y la importación de marcaciones de asistencia provenientes de dispositivos de control biométrico (huelleros). Su estructura separa la interfaz web, la API y la lógica de base de datos en tres repositorios independientes.

Este repositorio funciona como **presentación pública del proyecto dentro de mi portafolio profesional**. Permite conocer su propósito, alcance y decisiones técnicas, mientras el código de los componentes se conserva en repositorios privados por confidencialidad empresarial.

## Contexto y propósito

En un entorno empresarial, los registros de entrada y salida del personal son una fuente de información para el seguimiento de asistencia y los procesos asociados a nómina. NomiPlus aborda la recepción de esos registros y la administración de los accesos al sistema mediante una aplicación web conectada a Oracle.

El alcance documentado corresponde a los componentes presentes en el código. El proyecto está en evolución: esta descripción no supone que ya exista un motor completo de liquidación de nómina, cálculo de prestaciones o generación de pagos.

## Funcionalidades

### Acceso y administración de usuarios

- Inicio, consulta y cierre de sesión mediante JWT almacenado en una cookie `HttpOnly`.
- Validación del estado del usuario durante el inicio de sesión.
- Consulta de usuarios con búsqueda por código o nombre, filtro por estado y paginación.
- Creación, actualización, activación y desactivación de usuarios.
- Interfaz con formularios, confirmaciones de acciones y notificaciones de resultados.

### Permisos y módulos

- Reglas de acceso asociadas a cada combinación de usuario y módulo.
- Permisos diferenciados de consulta, inserción, actualización, eliminación y reporte.
- Validación de permisos en operaciones administrativas de la API y protección de la ruta de usuarios en la interfaz.
- Operaciones de administración de módulos y reglas disponibles en el backend.
- Generación de reglas iniciales inactivas al registrar usuarios o módulos.

La existencia de un permiso de reporte forma parte del modelo de autorización; no implica que haya un módulo de reportes implementado.

### Importación de asistencia

- Recepción de archivos `.csv` y `.txt` de marcaciones de huellero, con un límite de 10 MB por archivo.
- Carga masiva en Oracle mediante **SQL*Loader** y un archivo de control adaptado al formato de origen.
- Transformación de fechas y normalización de los valores de entrada y salida durante la carga.
- Lectura del log para informar registros procesados, cargados, rechazados, descartados y omitidos.
- Presentación de resultados y advertencias de importación en la interfaz.
- Archivo de los documentos procesados y generación de archivos de diagnóstico de carga.

La importación requiere el formato esperado por el archivo de control; no es un importador genérico de cualquier CSV o TXT.

### Componentes en Oracle

La base de datos contiene paquetes PL/SQL para usuarios, empleados, módulos, reglas de acceso y errores de negocio. La gestión de empleados cuenta con operaciones de consulta, registro, actualización, activación, desactivación y eliminación en esta capa.

**Alcance de la interfaz actual:** inicio de sesión, pantalla de bienvenida, administración de usuarios e importación de eventos. La gestión de empleados está definida en Oracle; la de módulos y reglas también dispone de API. Estas capacidades no se presentan aquí como pantallas completas de la aplicación.

## Tecnologías

| Capa | Tecnologías | Uso |
| --- | --- | --- |
| Frontend | React 19, React Router 7 | Interfaz web, navegación y rutas protegidas. |
| Interfaz y estilos | Material UI, Material Icons, Emotion y CSS Modules | Componentes visuales y estilos por componente. |
| Comunicación HTTP | Axios | Consumo de la API desde el frontend. |
| Herramientas de frontend | Vite 8 y ESLint | Desarrollo, compilación y análisis estático. |
| Backend | Node.js, Express 5 y JavaScript con ES Modules | API HTTP organizada por módulos funcionales. |
| Validación | Zod | Validación de parámetros, consultas y cuerpos de solicitudes. |
| Sesiones | JSON Web Token y cookie-parser | Emisión y lectura de tokens de autenticación. |
| Recepción de archivos | Multer | Gestión de archivos recibidos para importar asistencia. |
| Persistencia | Oracle Database, PL/SQL y node-oracledb | Procedimientos almacenados, consultas y pool de conexiones. |
| Carga masiva | Oracle SQL*Loader | Importación de marcaciones y generación de diagnósticos. |
| Configuración e integración | dotenv y CORS | Variables de entorno y comunicación entre la interfaz y la API. |
| Control de versiones | Git y Git submodules | Separación y vinculación de los tres componentes. |

## Arquitectura y forma de trabajo

El proyecto utiliza una **arquitectura por capas y una organización por módulos funcionales**:

```text
Interfaz web · React
        │
        │ Solicitudes HTTP · Axios
        ▼
API · Node.js + Express
        ├── Rutas, middlewares y validación
        ├── Controladores y servicios
        ├── node-oracledb ──────► Oracle · Paquetes PL/SQL
        └── SQL*Loader ─────────► Oracle · Registros de asistencia
```

- **Frontend:** las páginas, componentes, hooks y servicios se agrupan por funcionalidad. Los contextos comparten el estado de autenticación y permisos; los componentes comunes reutilizan tablas, formularios y notificaciones.
- **Backend:** cada módulo separa rutas, validaciones, controladores y servicios. Los middlewares concentran responsabilidades transversales como autenticación, autorización y validación de solicitudes.
- **Base de datos:** los paquetes PL/SQL encapsulan operaciones y reglas de negocio, con separación entre especificaciones y cuerpos. Las consultas utilizan cursores y, donde corresponde, filtros y paginación.
- **Integración:** el backend administra un pool de conexiones a Oracle y utiliza SQL*Loader para el flujo de carga masiva de asistencia.
- **Organización del código:** cada componente mantiene su propio repositorio e historial. El repositorio padre los relaciona mediante submódulos de Git.

Esta estructura permite trabajar de forma independiente en presentación, servicios HTTP y persistencia, manteniendo responsabilidades delimitadas. Describe la organización técnica observable en el código; no atribuye una metodología de gestión de proyectos específica.

## Repositorios del proyecto

| Componente privado | Responsabilidad |
| --- | --- |
| [nomina-frontend](https://github.com/juanserna1711/nomina-frontend) | Aplicación web en React, navegación, formularios y consumo de la API. |
| [nomina-backend](https://github.com/juanserna1711/nomina-backend) | API en Node.js y Express, acceso a Oracle e integración con SQL*Loader. |
| [nomina-database](https://github.com/juanserna1711/nomina-database) | Paquetes PL/SQL y archivo de control para la importación de asistencia. |

Los enlaces requieren autorización de acceso. Si no cuentas con ella, GitHub puede mostrar una página de repositorio no encontrado. Clonar este repositorio público no concede acceso al contenido de los submódulos privados.

## Confidencialidad y revisión técnica

Este proyecto fue desarrollado en un contexto laboral y corporativo. Para proteger la información de la empresa, el código fuente de los componentes se mantiene privado. Esta presentación pública no concede derechos de uso, copia o distribución del código ni de los activos empresariales.

Si existe interés en conocer mi trabajo, podemos coordinar una **demostración o revisión técnica guiada**, dentro de los límites de confidencialidad y las autorizaciones que correspondan. El objetivo es explicar las decisiones de desarrollo y mostrar mis capacidades sin divulgar información interna.

**Desarrollado por Juan Andrés Serna Castro.** Puedes encontrarme en [mi perfil de GitHub](https://github.com/juanserna1711).
