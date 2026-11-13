<div align="center">
  
# ⚽ FutbolWebApp

**Sistema de Gestión Deportiva — Web Mobile Application**

[![DataFlex](https://img.shields.io/badge/DataFlex-25.0-00599C?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0id2hpdGUiPjxwYXRoIGQ9Ik0xMiAyQzYuNDggMiAyIDYuNDggMiAxMnM0LjQ4IDEwIDEwIDEwIDEwLTQuNDggMTAtMTBTMTcuNTIgMiAxMiAyeiIvPjwvc3ZnPg==)](https://www.dataaccess.com/)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=for-the-badge&logo=mysql&logoColor=white)](https://www.mysql.com/)
[![Gitflow](https://img.shields.io/badge/Workflow-Gitflow-F05032?style=for-the-badge&logo=git&logoColor=white)](https://github.com/FJBP-code/FutbolWebApp)
[![Status](https://img.shields.io/badge/Sprint_1-Completado-2EA043?style=for-the-badge)](#)

*Aplicación Web Mobile desarrollada en **DataFlex 25.0** conectada a **MySQL** para la administración integral de ligas, equipos, entrenadores, jugadores y posiciones tácticas.*

[Explorar Estructura](#️-arquitectura-del-proyecto) • [Modelo de Datos](#️-modelo-de-datos-mysql) • [Vistas y UI](#️-vistas-y-funcionalidades) • [Instalación](#-instalación-y-configuración) • [Gitflow](#-flujo-de-trabajo-gitflow)

</div>

---

## 📋 Descripción del Proyecto

**FutbolWebApp** es una solución web adaptativa (*responsive*) diseñada bajo la arquitectura **Web Mobile (Drilldown)** de DataFlex. El propósito del proyecto (enmarcado en el **Sprint 1**) es migrar e integrar una base de datos relacional en **MySQL** con el entorno de DataFlex, reemplazando las tablas embebidas por defecto y proporcionando una interfaz táctil e intuitiva basada en vistas de selección (*Select*), vistas de detalle/edición (*Zoom*) y un panel de control centralizado (*Dashboard*).

### ✨ Características Principales
* **Navegación Drilldown (Mobile-First):** Flujo ágil entre listados maestros y formularios de detalle mediante eventos `OnRowClick` y rutas registradas (`WebRegisterPath`).
* **Integración ODBC con MySQL:** Mapeo completo de tablas relacionales y migración de tablas del sistema de sesiones y usuarios (`WebAppUser`, `WebAppSession`, `WebAppServerProps`).
* **Integridad Referencial (DDOs):** Uso de *DataDictionaries* interconectados para garantizar las reglas de negocio entre ligas, clubes y su plantilla.
* **Dashboard Interactivo:** Panel principal con acceso directo mediante tarjetas (*tiles*) hacia los módulos principales del sistema.
* **Seguridad Aplicada:** Exclusión de credenciales sensibles (`DFConnId.ini`) del control de versiones público y limpieza de pistas de acceso en la vista de autenticación.

---

## 🏗️ Arquitectura del Proyecto

La estructura del repositorio está organizada siguiendo las mejores prácticas de un entorno **DataFlex 25.0**, versionando únicamente los recursos necesarios para el despliegue y desarrollo colaborativo:

```text
FutbolWebApp/
├── 📁 AppHtml/                 # Motor web (DfEngine), temas CSS (Df_Material) y recursos estáticos
├── 📁 AppSrc/                  # Código fuente de la aplicación y vistas Web (.wo)
│   ├── 📄 WebApp.src           # Punto de entrada, configuración global y menú de navegación
│   ├── 📄 Dashboard.wo         # Vista principal con los 5 tiles de acceso rápido
│   ├── 📄 Login.wo             # Control de acceso y autenticación de usuarios
│   ├── 📄 SelectEntrenador.wo  # Listado maestro de entrenadores
│   ├── 📄 ZoomEntrenador.wo    # Ficha de detalle/edición de entrenador
│   ├── 📄 Jugadores.wo         # Listado maestro de jugadores (oSelectJugadores)
│   ├── 📄 Jugador.wo           # Ficha de detalle/edición de jugador (oZoomJugador)
│   ├── 📄 SelectPosicion.wo    # Listado maestro de posiciones
│   ├── 📄 ZoomPosicion.wo      # Ficha de detalle/edición de posición
│   ├── 📄 SelectEquipo.wo      # Listado maestro de equipos
│   ├── 📄 ZoomEquipo.wo        # Ficha de detalle/edición de equipo
│   ├── 📄 SelectLiga.wo        # Listado maestro de ligas
│   └── 📄 ZoomLiga.wo          # Ficha de detalle/edición de liga
├── 📁 DDSrc/                   # DataDictionaries (.dd), definiciones (.fd) y DDClassList.xml
├── 📁 Data/                    # Esquema de archivos (Filelist.cfg) y tablas del sistema
├── 📄 .gitignore               # Reglas de exclusión (ejecutables, logs, caché, IdeSrc y credenciales)
└── 📄 FutbolWebApp.sws         # Archivo de configuración del Workspace
```

---

## 🗄️ Modelo de Datos (MySQL)

La base de datos `futbol` está alojada en **MySQL** y se comunica con el servidor mediante el controlador `ODBC_DRV` (identificador `DFCONNID=Futbol`). 

| Nº Tabla | Nombre en BD | DataDictionary (`.dd`) | Campos Principales | Relaciones (`Server DDO`) |
| :---: | :--- | :--- | :--- | :--- |
| **1** | `entrenador` | `centrenadorDataDictionary` | `ID`, `Nombre`, `Apellido`, `Nacionalidad`, `EquipoID` | `EquipoID` ➔ `equipo.ID` |
| **2** | `equipo` | `cequipoDataDictionary` | `ID`, `Nombre`, `LigaID` | `LigaID` ➔ `liga.ID` |
| **3** | `jugador` | `cjugadorDataDictionary` | `ID`, `Nombre`, `Apellido`, `Nacionalidad`, `EquipoID` | `EquipoID` ➔ `equipo.ID` |
| **4** | `jugadorposicion` | `cjugadorposicionDataDictionary` | `ID`, `JugadorID`, `PosicionID` | Tabla intermedia N:M (`jugador` ↔ `posicion`) |
| **5** | `liga` | `cligaDataDictionary` | `ID`, `Nombre`, `Pais`, `Equipos`, `EquiposMax` | *Tabla Maestra* |
| **6** | `posicion` | `cposicionDataDictionary` | `ID`, `Nombre` | *Tabla Maestra* |

> 💡 **Nota técnica sobre tablas embebidas:** Las tablas por defecto del entorno (`CodeMast`, `CodeType`, `WebAppUser`, `WebAppSession` y `WebAppServerProps`) también fueron exportadas y vinculadas exitosamente al motor MySQL.

---

## 🖥️ Vistas y Funcionalidades

### 1. Vistas Select y Zoom (CRUD)
Se han implementado los pares de vistas **Select** (consulta y búsqueda) y **Zoom** (alta, baja y modificación) para las 5 entidades clave requeridas en el sprint:

| Módulo | Vista Select (Listado) | Columnas Visibles en Lista | Vista Zoom (Detalle) | Jerarquía DDO Cargada |
| :--- | :--- | :--- | :--- | :--- |
| **Entrenadores** | `SelectEntrenador.wo` | `Nombre`, `Apellido` | `ZoomEntrenador.wo` | `liga` ➔ `equipo` ➔ `entrenador` |
| **Jugadores** | `Jugadores.wo` | `Nombre`, `Apellido` | `Jugador.wo` | `liga` ➔ `equipo` ➔ `jugador` |
| **Posiciones** | `SelectPosicion.wo` | `Nombre` | `ZoomPosicion.wo` | `posicion` |
| **Equipos** | `SelectEquipo.wo` | `Nombre`, `Liga` (`liga.Nombre`) | `ZoomEquipo.wo` | `liga` ➔ `equipo` |
| **Ligas** | `SelectLiga.wo` | `Nombre` | `ZoomLiga.wo` | `liga` |

* **Navegación fluida:** Todas las listas incluyen respuesta al clic en fila (`pbServerOnRowClick = True`) que invoca `Send NavigateForward of oZoom... Self`, solucionando el error de plantilla no definida (`DFERR 4402`) y permitiendo abrir la ficha de detalle de inmediato.

### 2. Dashboard (`Dashboard.wo`)
La pantalla de inicio presenta un diseño limpio con el tema **`Df_Material`**, organizado en una cuadrícula responsiva de 2 columnas que incluye el panel de bienvenida de sesión (`oWelcomeTile`) y **5 tarjetas interactivas (`cWebHtmlBox`)**:

* 🎽 **Entrenadores** (`oTileEntrenadores`) ➔ Abre `oSelectEntrenador`
* 🏃 **Jugadores** (`oTileJugadores`) ➔ Abre `oSelectJugadores`
* 📍 **Posiciones** (`oTilePosiciones`) ➔ Abre `oSelectPosicion`
* 🛡️ **Equipos** (`oTileEquipos`) ➔ Abre `oSelectEquipo`
* 🏆 **Ligas** (`oTileLigas`) ➔ Abre `oSelectLiga`

---

## 🚀 Instalación y Configuración

Sigue estos pasos para desplegar el entorno de desarrollo en tu máquina local:

### Prerrequisitos
* **DataFlex Studio 25.0** (64-bit) instalado y configurado con IIS.
* Servidor **MySQL 8.0+** activo.
* Conector **MySQL ODBC Driver** configurado en el sistema (DSN de sistema o usuario llamado `Futbol`).

### Paso a paso

1. **Clonar el repositorio:**
   ```powershell
   git clone https://github.com/FJBP-code/FutbolWebApp.git
   cd FutbolWebApp
   ```

2. **Preparar la Base de Datos:**
   * Ejecuta el script SQL del proyecto en tu servidor MySQL para crear el esquema `futbol` y sus tablas.
   * Verifica que tu origen de datos ODBC (`DSN=Futbol`) apunte correctamente a dicho esquema.

3. **Configurar las credenciales de conexión (`DFConnId.ini`):**
   > 🔒 Por motivos de seguridad en repositorios públicos, el archivo `Data/DFConnId.ini` está excluido mediante `.gitignore`.
   
   Crea manualmente el archivo `Data/DFConnId.ini` con la siguiente estructura (o reconecta la base de datos desde el asistente de DataFlex Studio):
   ```ini
   [connection1]
   id=Futbol
   driver=ODBC_DRV
   connection=DSN=Futbol
   UID=<tu_usuario_mysql>
   DFPWD250=<tu_contraseña_cifrada_por_dataflex>
   ```

4. **Compilar y Ejecutar:**
   * Abre el archivo `FutbolWebApp.sws` en **DataFlex Studio 25.0**.
   * Presiona **F5** (Compile & Run) para generar `Programs/WebApp.exe` y lanzar la aplicación en `http://localhost/FutbolMobWebApp/Index.html`.

---

## 🔀 Flujo de Trabajo (Gitflow)

El desarrollo del proyecto se ha gestionado estrictamente bajo la metodología **Gitflow**, manteniendo un historial limpio y modularizado por cada funcionalidad solicitada:

```text
main     ●─────────────────────────────────────────────────────────● (Release Sprint 1)
          \                                                       /
develop    ●──●────────────────●───────────────●──────────────●──● (Rama de Integración)
               \              / \             / \            /
feature/*       ●── (Vistas) ●   ●── (Tiles) ●   ●── (Fix) ─●
```

### Estructura de Ramas Utilizadas
* **`main`**: Rama principal de producción con el producto final validado.
* **`develop`**: Rama troncal de integración donde convergen todas las características probadas.
* **Ramas `feature/`** (creadas a partir de `develop` y fusionadas con `--no-ff`):
  * `feature/vista-entrenador` — Implementación de Select y Zoom para Entrenadores.
  * `feature/vistaJugadores` — Implementación de Select y Zoom para Jugadores y ajuste de columnas.
  * `feature/vistaPosicion` — Implementación de Select y Zoom para Posiciones.
  * `feature/vistaEquipo` — Implementación de Select y Zoom para Equipos con relación a nombre de Liga.
  * `feature/vistaLiga` — Implementación de Select y Zoom para Ligas y estandarización de nombres.
  * `feature/dashboard-tiles` — Creación de las 5 tarjetas de acceso rápido en `Dashboard.wo`.
  * `feature/login-sin-hint` — Mejora de seguridad eliminando pistas de credenciales en `Login.wo`.

---

## 🛠️ Tecnologías y Herramientas

* **Lenguaje y Framework:** DataFlex 25.0 (Web Mobile / Drilldown Architecture)
* **Motor de Base de Datos:** MySQL 8.0 + ODBC Driver (`ODBC_DRV`)
* **Frontend:** HTML5, CSS3 (Tema `Df_Material`), JavaScript (`DfEngine`)
* **Control de Versiones:** Git & GitHub (CLI / Sourcetree)

---

<div align="center">

Desarrollado por **[FJBP-code](https://github.com/FJBP-code)**  
Repositorio oficial: [github.com/FJBP-code/FutbolWebApp](https://github.com/FJBP-code/FutbolWebApp)

</div>