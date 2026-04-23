Para llevar tu proyecto de **crudclinica** al siguiente nivel, es fundamental dominar las herramientas de línea de comandos. Esto te permitirá automatizar la conexión entre Flutter y Firebase sin tener que configurar archivos manualmente cada vez.

Aquí tienes la guía técnica paso a paso:

---

## 1. Software Necesario: Node.js y npm
Para usar Firebase CLI, necesitas **Node.js**, que incluye automáticamente **npm** (Node Package Manager).

### Cómo verificar si ya lo tienes
Abre la terminal (PowerShell o CMD) y escribe:
* `node -v`
* `npm -v`

> **Nota:** Si aparece un error indicando que "no se reconoce el comando", significa que no está instalado o no está en el PATH.

### Instalación paso a paso en Windows
1.  **Descarga:** Ve a [nodejs.org](https://nodejs.org/) y descarga la versión **LTS** (Long Term Support).
2.  **Instalación:** Ejecuta el instalador. Asegúrate de que la casilla **"Add to PATH"** esté marcada.
3.  **Instalación Global:** Al instalar Node.js desde el ejecutable, npm se instala de forma global por defecto para tu usuario.

---

## 2. Instalación de Firebase CLI (firebase-tools)
Una vez que `npm` funciona, instalaremos las herramientas de Firebase de manera global para que puedas usarlas en cualquier carpeta de tu computadora.

### Comando de instalación
Ejecuta el siguiente comando en tu terminal:
```bash
npm install -g firebase-tools
```
*El flag `-g` significa "global".*

### Cómo verificar la instalación
```bash
firebase --version
```

---

## 3. Acceso a Firebase con tu Cuenta de Google
Para que tu computadora tenga permiso de modificar tus proyectos de Firebase, debes iniciar sesión:

1.  En la terminal, escribe:
    ```bash
    firebase login
    ```
2.  Se abrirá automáticamente una ventana en tu navegador web.
3.  Selecciona tu cuenta de Google (la misma que usaste en la consola de Firebase).
4.  Haz clic en **"Permitir"**.
5.  Vuelve a la terminal; verás un mensaje de éxito: `Success! Logged in as usuario@gmail.com`.

---

## 4. Configuración de Firebase para Flutter (FlutterFire CLI)
Para que tu proyecto `crudclinica` reconozca a Firebase de forma automática, Google recomienda usar el ejecutable de FlutterFire.

### Paso 1: Instalar el CLI de FlutterFire
```bash
dart pub global activate flutterfire_cli
```

### Paso 2: Vincular el proyecto
Desde la carpeta raíz de tu proyecto (`crudclinica`), ejecuta:
```bash
flutterfire configure
```
*Este comando te mostrará una lista de tus proyectos en Firebase. Selecciona `crudclinica`, elige las plataformas (Android, iOS, Web) y el comando generará automáticamente el archivo `firebase_options.dart` en tu carpeta `lib`.*



---

## 5. Resumen de comandos esenciales

| Tarea | Comando |
| :--- | :--- |
| **Instalar herramientas** | `npm install -g firebase-tools` |
| **Iniciar Sesión** | `firebase login` |
| **Cerrar Sesión** | `firebase logout` |
| **Listar Proyectos** | `firebase projects:list` |
| **Configurar Flutter** | `flutterfire configure` |

---

## Metodología de Flujo de Trabajo (Agentes y Roles)
En tu rol de **Creador de Software**, el flujo para los estudiantes sería:

1.  **Agente de Infraestructura:** Instala Node/npm y Firebase CLI.
2.  **Agente de Seguridad:** Ejecuta `firebase login` para autenticar el entorno.
3.  **Agente de Integración:** Ejecuta `flutterfire configure` para "unir" el código Dart con la consola de la nube.
4.  **Skill de Despliegue:** El estudiante ya puede usar los comandos de `firebase-tools` para gestionar índices de base de datos o reglas de seguridad.

¿Lograste visualizar la versión de Node.js en tu terminal o tuviste algún problema con el PATH de Windows?
