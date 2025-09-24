---
lab:
  title: Implementación de Microsoft Dev Box
  module: Implement Developer Self-Service
---

# Laboratorio 01: Implementación de Microsoft Dev Box

## Tiempo estimado: 60 minutos

## Requisitos previos

- Una cuenta empresarial de Microsoft Entra con tres cuentas de usuario precreadas (y opcionalmente, tres grupos precreados de Microsoft Entra) que representan tres roles diferentes implicados en las implementaciones de Microsoft Dev Box. Para mayor claridad, los nombres de usuario y grupo de las instrucciones para el laboratorio coinciden con la información de la siguiente tabla:

| Usuario              | Grupo                        | Role                  |
| ----------------- | ---------------------------- | --------------------- |
| platformegineer01 | DevCenter_Platform_Engineers | Ingeniero de plataformas     |
| devlead01         | DevCenter_Dev_Leads          | Responsable del equipo de desarrollo |
| devuser01         | DevCenter_Dev_Users          | Desarrollador             |

- Una suscripción de Azure para hospedar recursos de Microsoft Dev Box asociados al inquilino de Microsoft Entra que hospeda las cuentas de usuario y grupo
- Una suscripción de Microsoft Intune asociada al mismo inquilino de Microsoft Entra que la suscripción de Azure
- Una licencia de Microsoft Intune asignada a las tres cuentas de usuario precreadas de Microsoft Entra
- También necesitas una cuenta de GitHub y un repositorio.
  - Para crear una cuenta de GitHub, sigue las instrucciones del artículo [Creación de una cuenta en GitHub](https://docs.github.com/get-started/quickstart/creating-an-account-on-github).
  - Para crear un repositorio de GitHub, sigue las instrucciones del artículo [Creación de un nuevo repositorio](https://docs.github.com/repositories/creating-and-managing-repositories/creating-a-new-repository).
- Un repositorio de GitHub creado como bifurcación de https://github.com/microsoft/devcenter-catalog
- Un repositorio de GitHub creado como bifurcación de https://github.com/MicrosoftLearning/contoso-co-eShop

## Objetivos

Después de completar este taller, podrás:

- Implementación de un entorno de Microsoft Dev Box
- Personalizar un entorno de Microsoft Dev Box

## Ejercicio 1: Implementación de un entorno de Microsoft Dev Box.

En este ejercicio, sacarás provecho de un conjunto de características proporcionadas por Microsoft para implementar un entorno de Microsoft Dev Box. Este enfoque se centra en minimizar el trabajo implicado en la creación de una solución de autoservicio para desarrolladores funcionales.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Creación de un centro de desarrollo
- Tarea 2: Revisión de la configuración del centro de desarrollo
- Tarea 3: Creación de la definición de un equipo de desarrollo
- Tarea 4: Creación un proyecto
- Tarea 5: Creación de un grupo de equipos de desarrollo
- Tarea 6: Configuración de los permisos
- Tarea 7: Cálculo de un equipo de desarrollo

### Tarea 1: Creación de un centro de desarrollo

En esta tarea, crearás un centro de desarrollo de Azure que se usará durante el primer ejercicio de este laboratorio. Un centro de desarrollo es un servicio de ingeniería de plataforma que centraliza la creación y administración de entornos de implementación, desarrollo preconfigurado y escalables, optimizando la colaboración y el uso de recursos para los equipos de desarrollo de software.

1. Inicia un explorador web y ve a Azure Portal en `https://portal.azure.com`.
1. Cuando se te pida autenticar, inicia sesión con la cuenta de usuario Microsoft Entra.
1. En Azure Portal, en el cuadro de texto **Buscar**, busca y selecciona **`Dev centers`**.
1. En la página de **Centros de desarrollo**, selecciona **+ Crear**.
1. En la pestaña **Datos básicos** de la página **Crear un centro de desarrollo**, especifica la siguiente configuración y selecciona **Siguiente: Configuración**:

   | Configuración                                                                 | Valor                                                        |
   | ----------------------------------------------------------------------- | ------------------------------------------------------------ |
   | Suscripción                                                            | Nombre de la suscripción a Azure que usas en este laboratorio |
   | Resource group                                                          | El nombre de un **nuevo** grupo de recursos **rg-devcenter-01**     |
   | Nombre                                                                    | **devcenter-01**                                             |
   | Location                                                                | **(EE. UU.) Este de EE. UU.**                                             |
   | Adjuntar un catálogo de inicio rápido: Definiciones del entorno de implementación de Azure | Habilitado                                                      |
   | Adjuntar un catálogo de inicio rápido: Tareas de personalización de Dev Box              | Deshabilitado                                                     |

   > **Nota:** Configurarás un catálogo con tareas de personalización habilitadas en el segundo ejercicio de este laboratorio.

1. En la pestaña **Configuración** de la página **Crear un centro de desarrollo**, especifica la siguiente configuración y, a continuación, selecciona **Revisar + crear**:

   | Configuración                                    | Valor   |
   | ------------------------------------------ | ------- |
   | Habilitación de catálogos por proyecto                | Habilitado |
   | Permitir la red hospedada por Microsoft en proyectos | Habilitado |
   | Habilitación de la instalación del agente de Azure Monitor    | Habilitado |

   > **Nota:** Por diseño, los recursos de los catálogos adjuntos al centro de desarrollo están disponibles para todos los proyectos que contiene. La configuración **Habilitar catálogos por proyecto** permite adjuntar también catálogos adicionales a proyectos seleccionados arbitrariamente.

   > **Nota:** Los equipos de desarrollo se pueden conectar a una red virtual en su propia suscripción de Azure o a una hospedada por Microsoft, en función de si necesitan comunicarse con los recursos del entorno. Si no es necesario, habilitando la configuración **Permitir red hospedada por Microsoft en proyectos**, se presenta la opción de conectar equipos de desarrollo a una red hospedada por Microsoft, lo que minimiza eficazmente la sobrecarga de administración y configuración.

   > **Nota:** La configuración **Habilitar la instalación del agente de Azure Monitor** desencadena automáticamente la instalación del agente de Azure Monitor en todos los equipos de desarrollo del centro de desarrollo.

1. En la pestaña **Revisar + crear**, espera a que se complete el proceso de validación y, a continuación, selecciona **Crear**.

   > **Nota:** Espera a que se aprovisione el proyecto. La creación del proyecto puede tardar alrededor de 1 minuto.

1. En la página **Implementación completada**, selecciona **Ir al recurso**.

### Tarea 2: Revisión de la configuración del centro de desarrollo

En esta tarea, revisarás la configuración básica del centro de desarrollo que has creado en la tarea anterior.

1. En el explorador web que muestra Azure Portal, en la página **devcenter-01**, en el menú de navegación vertical del lado izquierdo, expande la sección **Configuración del entorno** y selecciona **Catálogos**.
1. En la página de **Catálogos de devcenter-01\|**, observa que el centro de desarrollo está configurado con el catálogo **quickstart-environment-definitions**, que apunta al repositorio de GitHub`https://github.com/microsoft/devcenter-catalog.git`.
1. Comprueba que la columna **Estado** contiene la entrada **Sincronización correcta**. Si no es así, usa la siguiente secuencia de pasos para volver a crear el catálogo:

   1. Selecciona la casilla situada junto a la entrada de catálogo generada automáticamente **quickstart-environment-definitions** y, a continuación, en la barra de herramientas, selecciona **Eliminar**.
   1. En la página de **Catálogos de devcenter-01 \|**, selecciona **+ Agregar**.
   1. En el panel **Agregar catálogo**, en el cuadro de texto **Nombre**, escribe **`quickstart-environment-definitions-fix`**, en la sección **Ubicación del catálogo**, selecciona **GitHub**, en **Tipo de autenticación**, selecciona **Aplicación de GitHub**, deja la casilla **Sincronizar automáticamente este catálogo** habilitada y, a continuación, selecciona **Iniciar sesión con GitHub.**
   1. En la ventana **Iniciar sesión con GitHub**, escribe las credenciales de GitHub y selecciona **Iniciar sesión**.

      > **Nota:** Estas credenciales de GitHub proporcionan acceso a un repositorio de GitHub creado como bifurcación de <https://github.com/microsoft/devcenter-catalog>

   1. Cuando se te solicite, en la ventana **Autorizar Microsoft DevCenter**, selecciona **Autorizar Microsoft DevCenter**.
   1. De nuevo en el panel **Agregar catálogo**, en la lista desplegable **Repositorio**, selecciona **devcenter-catalog**, en la lista desplegable **Rama**, acepta la entrada **Rama predeterminada**, en la **Ruta de acceso a la carpeta**, escribe **`Environment-Definitions`** y, a continuación, selecciona **Agregar**.
   1. De nuevo en la página **Catálogos de devcenter-01 \|**, comprueba que la sincronización se completa correctamente mediante la supervisión de la entrada en la columna **Estado**.

1. En la página **Catálogos de devcenter-01 \|**, selecciona la entrada **quickstart-environment-definitions-fix**.
1. En la página **quickstart-environment-definitions-fix**, revisa la lista de definiciones de entorno predefinidas.

   > **Nota:** Cada entrada representa una definición de un entorno de implementación de Azure definido en una subcarpeta respectiva de la carpeta **Definiciones de entorno** del repositorio `https://github.com/microsoft/devcenter-catalog.git` de GitHub.

   > **Nota:** Un entorno de implementación es una colección de recursos de Azure definidos en una plantilla denominada definición de entorno. Los desarrolladores pueden usar estas definiciones para implementar la infraestructura que servirá para hospedar sus soluciones. Para obtener más información sobre Azure Deployment Environments, consulte el artículo de Microsoft Learn [¿Qué es Azure Deployment Environments?](https://learn.microsoft.com/azure/deployment-environments/overview-what-is-azure-deployment-environments)

### Tarea 3: Creación de la definición de un equipo de desarrollo

En esta tarea, crearás una definición de equipo de desarrollo. Su propósito es definir el sistema operativo, las herramientas, la configuración y los recursos que sirven como plano técnico para crear entornos de desarrollo coherentes y adaptados (denominados equipos de desarrollo).

1. En el explorador web que muestra Azure Portal, en la página **devcenter-01**, en el menú de navegación vertical del lado izquierdo, expande la sección **Configuración del equipo de desarrollo** y selecciona **Definiciones del equipo de desarrollo**.
1. En la página **devcenter-01 \|Definiciones de equipos de desarrollo**, selecciona **+ Crear**.
1. En la página **Crear definición de equipo de desarrollo**, especifica la siguiente configuración y después selecciona **Crear**:

   | Configuración            | Valor                                                                                |
   | ------------------ | ------------------------------------------------------------------------------------ | ----------------------- |
   | Nombre               | **devbox-definition-01**                                                             |
   | Imagen              | \*\*Visual Studio 2022 Enterprise en Windows 11 Empresas + Aplicaciones de Microsoft 365 24H2 | Compatible con la hibernación\*\* |
   | Versión de la imagen      | **Más reciente**                                                                           |
   | Proceso            | **8 vCPU, 32 GB RAM**                                                                |
   | Storage            | **SSD de 256 GB**                                                                       |
   | Habilitación de la hibernación | Habilitado                                                                              |

   > **Nota:** Espera a que se cree la definición del equipo de desarrollo. Debería tardar menos de un minuto.

### Tarea 4: Crear un proyecto del Centro de desarrollo

En esta tarea, crearás un proyecto del Centro de desarrollo. Normalmente, un proyecto de centro de desarrollo corresponde a un proyecto de desarrollo dentro de la organización. Por ejemplo, puede crear un proyecto para el desarrollo de una aplicación de línea de negocio y otro proyecto para el desarrollo del sitio web de la empresa. Todos los proyectos de un centro de desarrollo comparten las mismas definiciones de equipo de desarrollo, conexión de red, catálogos y galerías de proceso. Puedes considerar la posibilidad de crear varios proyectos de centro de desarrollo si tienes varios proyectos de desarrollo que tienen administradores de proyectos y requisitos de permisos de acceso independientes.

1. En el explorador web que muestra Azure Portal, en la página **devcenter-01**, en el menú de navegación vertical del lado izquierdo, expande la sección **Administrar** y selecciona **Proyectos**.
1. En la página **devcenter-01 \|Proyectos**, selecciona **+ Crear**.
1. En la pestaña **Datos básicos** de la página **Crear un proyecto**, especifica la siguiente configuración y después selecciona **Siguiente: Administración de equipos de desarrollo**:

   | Configuración        | Valor                                                        |
   | -------------- | ------------------------------------------------------------ |
   | Suscripción   | Nombre de la suscripción a Azure que usas en este laboratorio |
   | Resource group | **rg-devcenter-01**                                          |
   | Centro de desarrollo     | **devcenter-01**                                             |
   | Nombre           | **devcenter-project-01**                                     |
   | Descripción    | **devcenter-project-01**                                     |

1. En la pestaña **Administración de equipos de desarrollo** de la página **Crear un proyecto**, especifica la siguiente configuración y después selecciona **Siguiente: Catálogos**:

   | Configuración                 | Valor |
   | ----------------------- | ----- |
   | Habilitación de los límites del equipo de desarrollo   | Sí   |
   | Equipos de desarrollo por desarrollador | **2** |

1. En la pestaña **Catálogos** de la página **Crear un proyecto**, especifica la siguiente configuración y después selecciona **Revisar + Crear**:

   | Configuración                            | Valor   |
   | ---------------------------------- | ------- |
   | Definiciones de entorno de implementación | Habilitado |
   | Definiciones de imagen                  | Habilitado |

   > **Nota:** Dado que has habilitado catálogos de nivel de proyecto al crear el centro de desarrollo, para los catálogos adjuntos a un proyecto de desarrollo, esta configuración determina qué elementos de catálogo se importan al proyecto tras la sincronización.

1. En la pestaña **Revisar + Crear** de la página **Crear un centro de desarrollo**, selecciona **Crear**:

   > **Nota:** Espera a que se cree el proyecto. Debería tardar menos de un minuto.

1. En la página **Implementación completada**, selecciona **Ir al recurso**.

### Tarea 5: Creación de un grupo de equipos de desarrollo

En esta tarea, crearás un grupo de equipos de desarrollo en el proyecto del Centro de desarrollo que has creado en la tarea anterior. Los usuarios de equipos de desarrollo usan los equipos de desarrollo para crear equipos de desarrollo. Un grupo de equipos de desarrollo vincula una definición de equipo de desarrollo con una conexión de red. En general, puede elegir entre las conexiones hospedadas por Microsoft o sus propias conexiones de red de Azure. La conexión de red determina la ubicación de un equipo de desarrollo hospedado y su acceso a otros recursos locales y en la nube. Considere la posibilidad de crear un grupo de equipos de desarrollo con una conexión de red más cercana a los usuarios del equipo de desarrollo. Además, para reducir el coste de la ejecución de los equipos de desarrollo, puedes configurar un grupo de equipos de desarrollo para que se apaguen diariamente a una hora predefinida.

1. En el explorador web que muestra Azure Portal, en la página **devcenter-project-01**, en el menú de navegación vertical del lado izquierdo, expande la sección **Administrar** y selecciona **Grupos de equipos de desarrollo**.
1. En la página **devcenter-project-01 \| Grupos de equipos de desarrollo**, selecciona **+ Crear**.
1. En la pestaña **Datos básicos** de la página **Crear un grupo de equipos de desarrollo**, especifica la siguiente configuración y después selecciona **Crear**:

   | Configuración                                                                                                           | Valor                                    |
   | ----------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
   | Nombre                                                                                                              | **devcenter-project-01-devbox-pool-01**  |
   | Definición                                                                                                        | **devbox-definition-01**                 |
   | Conexión de red                                                                                                | **Implementación en una red hospedada por Microsoft** |
   | Región                                                                                                            | **(EE. UU.) Este de EE. UU.**                         |
   | Habilitación del inicio de sesión único                                                                                             | Habilitado                                  |
   | Privilegios de creador de equipo de desarrollo                                                                                        | **Administrador local**                  |
   | Habilitar la detención automática según la programación                                                                                      | Habilitado                                  |
   | Hora de detención                                                                                                         | **07:00 PM**                             |
   | Zona horaria                                                                                                         | Tu zona horaria actual                   |
   | Habilitación de hibernación al desconectar                                                                                    | Habilitado                                  |
   | Periodo de gracia en minutos                                                                                           | **60**                                   |
   | Confirmo que mi organización tiene licencias de Ventaja híbrida de Azure, que se aplicarán a todos los equipos de desarrollo de este grupo. | Habilitado                                  |

   > **Nota:** Espera a que se cree el grupo del equipo de desarrollo. Esto puede tardar unos 2 minutos.

### Tarea 6: Configuración de los permisos

En esta tarea, asignarás permisos adecuados relacionados con el equipo de desarrollo de Microsoft a las tres entidades de seguridad de Microsoft Entra que se han aprovisionado en el entorno de laboratorio. Estas entidades de seguridad corresponden a roles típicos en escenarios de ingeniería de plataformas:

| Usuario              | Grupo                        | Role                  |
| ----------------- | ---------------------------- | --------------------- |
| platformegineer01 | DevCenter_Platform_Engineers | Ingeniero de plataformas     |
| devlead01         | DevCenter_Dev_Leads          | Responsable del equipo de desarrollo |
| devuser01         | DevCenter_Dev_Users          | Desarrollador             |

Microsoft Dev Box usa el control de acceso basado en roles de Azure (RBAC de Azure) para controlar el acceso a la funcionalidad de nivel de proyecto. Los ingenieros de plataforma deben tener control total para crear y administrar centros de desarrollo, sus catálogos y proyectos. Esto requiere poseer el rol de propietario o colaborador, en función de si también necesitan la capacidad de delegar permisos a otros usuarios. A los responsables de los equipos de desarrollo se les debe asignar el rol de administrador de proyectos del Centro de desarrollo, que concede la capacidad de realizar tareas administrativas en proyectos de Microsoft Dev Box. Los usuarios del equipo de desarrollo deben tener la capacidad de crear y administrar sus propios equipos de desarrollo, que están asociados con el rol de usuario de Dev Box.

> **Nota:** Para empezar, asignarás permisos al grupo de Microsoft Entra destinado a contener cuentas de usuario de ingeniero de plataforma.

1. En el explorador web que muestra Azure Portal, ve a la página **devcenter-01** y, en el menú de navegación vertical de la izquierda, selecciona **Control de acceso (IAM)**.
1. En la página **devcenter-01 \|Control de acceso (IAM)**, selecciona **+ Agregar** y, en la lista desplegable, selecciona **Agregar asignación de roles**.
1. En la pestaña **Rol** de la página **Agregar asignación de roles**, selecciona la pestaña **Rol de administrador con privilegios**, en la lista de roles, selecciona **Propietario** y finalmente selecciona **Siguiente**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, asegúrate de que la opción **Usuario, grupo o entidad de servicio** esté seleccionada y haz clic en **+ Seleccionar miembros**.
1. En el panel **Seleccionar miembros**, busca y selecciona **`DevCenter_Platform_Engineers`** y después haz clic en **Seleccionar**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, selecciona **Siguiente**.
1. En la pestaña **Condiciones** de la página **Agregar asignación de roles**, en la sección **Qué puede hacer el usuario**, selecciona la opción **Permitir al usuario asignar todos los roles (con privilegios elevados)** y después selecciona **Siguiente**.
1. En la pestaña **Revisar y asignar** de la página **Agregar asignación de roles**, selecciona **Revisar y asignar**.

   > **Nota:** Después asignarás permisos al grupo de Microsoft Entra destinado a contener cuentas de usuarios responsables del equipo de desarrollo.

1. De nuevo en la página de **devcenter-01 \|Control de acceso (IAM)**, del menú de navegación vertical de la izquierda, expande la sección **Administrar**, selecciona **Proyectos** y, en la lista de proyectos, selecciona **devcenter-project-01**.
1. En la página **devcenter-project-01**, en el menú de navegación vertical de la izquierda, selecciona **Control de acceso (IAM)**.
1. En la página **devcenter-project-01 \|Control de acceso (IAM)**, selecciona **+ Agregar** y, en la lista desplegable, selecciona **Agregar asignación de roles**.
1. En la pestaña **Rol** de la página **Agregar asignación de roles**, asegúrate de que está seleccionada la pestaña **Roles de función de trabajo**, en la lista de roles, selecciona **Administrador de proyectos de DevCenter** y selecciona **Siguiente**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, asegúrate de que la opción **Usuario, grupo o entidad de servicio** esté seleccionada y haz clic en **+ Seleccionar miembros**.
1. En el panel **Seleccionar miembros**, busca y selecciona **`DevCenter_Dev_Leads`** y después haz clic en **Seleccionar**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, selecciona **Siguiente**.
1. En la pestaña **Revisar y asignar** de la página **Agregar asignación de roles**, selecciona **Revisar y asignar**.

   > **Nota:** Por último, asignarás permisos al grupo de Microsoft Entra destinado a contener cuentas de usuario de desarrollador.

1. De nuevo en la página **devcenter-project-01 \|Control de acceso (IAM)**, selecciona **+ Agregar** y, en la lista desplegable, selecciona **Agregar asignación de roles**.
1. En la pestaña **Rol** de la página **Agregar asignación de roles**, asegúrate de que está seleccionada la pestaña **Roles de función de trabajo**, en la lista de roles, selecciona **Usuarios de Dev Box de DevCenter** y selecciona **Siguiente**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, asegúrate de que la opción **Usuario, grupo o entidad de servicio** esté seleccionada y haz clic en **+ Seleccionar miembros**.
1. En el panel **Seleccionar miembros**, busca y selecciona **`DevCenter_Dev_Users`** y después haz clic en **Seleccionar**.
1. En la pestaña **Miembros** de la página **Agregar asignación de roles**, selecciona **Siguiente**.
1. En la pestaña **Revisar y asignar** de la página **Agregar asignación de roles**, selecciona **Revisar y asignar**.

### Tarea 7: Cálculo de un equipo de desarrollo

En esta tarea, evaluarás la funcionalidad de un equipo de desarrollo mediante una cuenta de usuario de desarrollador de Microsoft Entra.

1. Inicia un explorador web de incógnito/in-private y ve al portal para desarrolladores de Microsoft Dev Box en `https://aka.ms/devbox-portal`.
1. Cuando se te solicite que inicies sesión, proporciona las credenciales de la cuenta de usuario **devuser01**.
1. En la página **Le damos la bienvenida, devuser01** del portal para desarrolladores de Microsoft Dev Box, selecciona **+ Nuevo equipo de desarrollo**.
1. En el panel **Agregar un equipo de desarrollo**, en el cuadro de texto **Nombre**, escribe **`devuser01box01`**
1. Revisa otra información presentada en el panel **Agregar un equipo de desarrollo**, incluyendo el nombre del proyecto, las especificaciones del grupo de equipos de desarrollo, el estado de compatibilidad con la hibernación y el tiempo de apagado programado. Además, ten en cuenta la opción de aplicar personalizaciones y la notificación de que la creación del equipo de desarrollo puede tardar hasta 65 minutos.

   > **Nota:** Los nombres de los equipos de desarrollo deben ser únicos dentro de un proyecto.

1. En el panel **Agregar un equipo de desarrollo**, selecciona **Crear**.

   > **Nota:** No esperes a que se cree el equipo de desarrollo. En su lugar, continúa directamente con el siguiente ejercicio de este laboratorio. Sin embargo, considera la posibilidad de volver a este ejercicio y recorrer el resto de esta tarea una vez completado el siguiente ejercicio.

1. Una vez que el equipo de desarrollo está completamente aprovisionado y en ejecución, conéctate a él al seleccionar la opción **Conectar a través de la aplicación**.

   > **Nota:** La conectividad a un equipo de desarrollo se puede establecer mediante una aplicación de Windows de Escritorio remoto, un cliente de Escritorio remoto (mstsc.exe) o directamente dentro de una ventana del explorador web.

1. En la ventana emergente titulada **Este sitio está intentando abrir el centro de conexiones de Escritorio remoto de Microsoft**, selecciona **Abrir**. Esto iniciará automáticamente una sesión de Escritorio remoto en el equipo de desarrollo.
1. Cuando se te soliciten las credenciales, autentifícate al proporcionar el nombre de usuario y la contraseña de la cuenta **devuser01**.
1. Dentro de la sesión de Escritorio remoto del equipo de desarrollo, comprueba que su configuración incluye una instalación de aplicaciones de Visual Studio 2022 y Microsoft 365.

   > **Nota:** Puedes apagar el equipo de desarrollo directamente desde el portal para desarrolladores de Microsoft Dev Box como usuario de desarrollo seleccionando primero el símbolo de puntos suspensivos en la interfaz **Dev Box** y, a continuación, seleccionando **Apagar** en el menú en cascada. Como alternativa, como ingeniero de plataforma o responsable del equipo de desarrollo, puedes controlar el ciclo de vida del equipo de desarrollo desde la sección **Grupos de equipos de desarrollo** del proyecto del centro de desarrollo correspondiente.

## Ejercicio 2: personalización de un entorno de Microsoft Dev Box

En este ejercicio, personalizarás la funcionalidad del entorno de Microsoft Dev Box. Este enfoque se centra en la extensión de los cambios que puedes aplicar al implementar una solución de autoservicio de desarrollador personalizada.

El ejercicio consta de las tareas siguientes:

- Tarea 1: Creación de una Azure Compute Gallery y adjuntarla al centro de desarrollo
- Tarea 2: Configuración de la autenticación y autorización para Image Builder de Azure
- Tarea 3: Creación de una imagen personalizada mediante Image Builder de Azure
- Tarea 4: Creación de una conexión de red del Centro de desarrollo de Azure
- Tarea 5: Adición de las definiciones de imágenes a un proyecto del Centro de desarrollo de Azure
- Tarea 6: Creación de un grupo de equipos de desarrollo personalizado
- Tarea 7: Cálculo de un equipo de desarrollo personalizado

### Tarea 1: Creación de una Azure Compute Gallery y adjuntarla al centro de desarrollo

En esta tarea, crearás una Azure Compute Gallery y la asociarás al Centro de desarrollo que creaste en el ejercicio anterior de este laboratorio. Una galería es un repositorio que reside en la suscripción de Azure y permite crear la estructura y la organización en torno a imágenes personalizadas. Después de adjuntar una galería de Compute a un Centro de desarrollo y las rellenas de imágenes, puedes crear definiciones del equipo de desarrollo basadas en imágenes almacenadas en la galería de Compute.

1. Inicia un explorador web y ve a Azure Portal en `https://portal.azure.com`.
1. Cuando se te pida que te autentiques, inicia sesión con tu cuenta de usuario de Microsoft.
1. En Azure Portal, en el cuadro de texto **Buscar**, busca y selecciona **`Azure compute galleries`**.
1. En la página de las **galerías de Azure Compute**, selecciona **+ Crear**.
1. En la pestaña **Datos básicos** de la página **Crear Azure Compute Gallery**, especifica la siguiente configuración y, a continuación, selecciona **Siguiente: Método de uso compartido**:

   | Configuración        | Valor                                                        |
   | -------------- | ------------------------------------------------------------ |
   | Suscripción   | Nombre de la suscripción a Azure que usas en este laboratorio |
   | Resource group | **rg-devcenter-01**                                          |
   | Nombre           | **compute_gallery_01**                                       |
   | Región         | **(EE. UU.) Este de EE. UU.**                                             |

1. En la pestaña **Método de uso compartido** de la página **Crear Azure Compute Gallery**, deja la opción predeterminada **Control de acceso basado en roles (RBAC)** seleccionada y, a continuación, selecciona **Revisar + crear**.
1. En la pestaña **Revisar + crear**, espera a que se complete el proceso de validación y, a continuación, selecciona **Crear**.

   > **Nota:** Espera a que se aprovisione el proyecto. La creación de la Azure Compute Gallery debe tardar menos de 1 minuto.

1. En Azure Portal, busca y selecciona **`Dev centers`** y, en la página de **Centros de desarrollo**, selecciona **devcenter-01**.
1. En la página **devcenter-01**, en el menú de navegación vertical del lado izquierdo, expande la sección **Configuración del equipo de desarrollo** y selecciona **Galerías Azure compute**.
1. En la página **devcenter-01 \| Galerías Azure Compute**, selecciona **+ Agregar**.
1. En el panel **Agregar Azure Compute Gallery**, en la lista desplegable **Galería**, selecciona **compute_gallery_01** y, a continuación, selecciona **Agregar**.

   > **Nota:** Si recibes un mensaje de error: "_Este centro de desarrollo no tiene una identidad asignada por sistema o usuario. Las galerías no se pueden agregar hasta que se haya asignado una identidad"_. deberás asignar una identidad asignada por el sistema al Centro de desarrollo.
   > Para ello, en Azure Portal, en la página **devcenter-01**, en el menú de navegación vertical del lado izquierdo, selecciona **Identidad** en Configuración, en la pestaña **Asignado por el sistema**, establece el conmutador de **Estado** en **Activado** y, a continuación, selecciona **Guardar**.

### Tarea 2: Configuración de la autenticación y la autorización

En esta tarea, crearás una identidad administrada asignada por el usuario que usará Azure Image Builder para agregar imágenes a la Azure Compute Gallery que creaste en la tarea anterior. También configurarás los permisos necesarios creando un rol personalizado de control de acceso basado en roles (RBAC) y asignándolo a la identidad administrada. Esto te permitirá usar Azure Image Builder en la siguiente tarea para compilar una imagen personalizada.

1. En Azure Portal, selecciona el icono de la barra de herramientas de **Cloud Shell** para abrir el panel de Cloud Shell y, si es necesario, selecciona **Cambiar a PowerShell** para iniciar una sesión de PowerShell y, en el cuadro de diálogo **Cambiar a PowerShell en Cloud Shell**, selecciona **Confirmar**.

   > **Nota:** Si es la primera vez que abres Cloud Shell, en el cuadro de diálogo **Bienvenido a Azure Cloud Shell**, selecciona **PowerShell**, en el panel **Introducción**, selecciona la opción **No se requiere ninguna cuenta de almacenamiento** y, en la lista desplegable **Suscripción**, selecciona el nombre de la suscripción de Azure que usas en esta pestaña.

1. En la sesión de PowerShell del panel de Cloud Shell, ejecuta los siguientes comandos para asegurarse de que todos los proveedores de recursos necesarios están registrados:

   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
   Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
   Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
   Register-AzResourceProvider -ProviderNamespace Microsoft.Network
   ```

1. Ejecuta el siguiente comando para instalar los módulos de PowerShell necesarios (cuando se te solicite, escribe **A** y presiona la **tecla Entrar**):

   ```powershell
   'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
   ```

1. Ejecuta los siguientes comandos para configurar variables a las que se hará referencia a lo largo del proceso de compilación de imágenes:

   ```powershell
   $currentAzContext = Get-AzContext
   # the target Azure subscription ID
   $subscriptionID=$currentAzContext.Subscription.Id
   # the target Azure resource group name
   $imageResourceGroup='rg-devcenter-01'
   # the target Azure region
   $location='eastus'
   # the reference name assigned to the image created by using the Azure Image Builder service
   $runOutputName="aibWinImg01"
   # image template name
   $imageTemplateName="templateWinVSCode01"
   # the Azure compute gallery name
   $computeGallery = 'compute_gallery_01'
   ```

1. Ejecuta los siguientes comandos para crear una identidad administrada asignada por el usuario (el Generador de imagen de VM usa la identidad de usuario que proporcionaste para almacenar imágenes en la Azure Compute Gallery de destino):

   ```powershell
   # Install the Azure PowerShell module to support AzUserAssignedIdentity
   Install-Module -Name Az.ManagedServiceIdentity
   # Generate a pseudo-random integer to be used for resource names
   $timeInt=$(get-date -UFormat "%s")

   # Create an identity
   $identityName='identityAIB' + $timeInt
   New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Location $location
   $identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
   $identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
   ```

1. Ejecuta los siguientes comandos para conceder a la identidad administrada asignada por el usuario recién creada los permisos necesarios para almacenar imágenes en el grupo de recursos **rg-devcenter-01**:

   ```powershell
   # Set variables
   $imageRoleDefName = 'Custom Azure Image Builder Image Def' + $timeInt
   $aibRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
   $aibRoleImageCreationPath = 'aibRoleImageCreation.json'

   # Customize the role definition file
   Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace '<subscriptionID>', $subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
   ((Get-Content -path $aibRoleImageCreationPath -Raw) -Replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

   # Create a role definition
   New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

   # Assign the role to the VM Image Builder user-assigned managed identity within the scope of the **rg-devcenter-01** resource group
   New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
   ```

### Tarea 3: Creación de una imagen personalizada mediante Image Builder de Azure

En esta tarea, usarás Azure Image Builder para crear una imagen personalizada basada en una plantilla de Azure Resource Manager (ARM) existente que defina una imagen de Windows 11 Empresas con Choco instalado automáticamente y Visual Studio Code. Azure VM Image Builder simplifica considerablemente el proceso de definición y aprovisionamiento de imágenes de VM. Se basa en una configuración de imagen que se especifica para configurar una canalización de creación de imágenes automatizada. Posteriormente, los desarrolladores podrán usar estas imágenes para aprovisionar sus equipos de desarrollo.

1. En la sesión de PowerShell del panel de Cloud Shell, ejecuta los siguientes comandos para crear una definición de imagen que se agregará a la Azure Compute Gallery que creaste en la primera tarea de este ejercicio:

   ```powershell
   # ensure that the image definition security type property is set to 'TrustedLaunch'
   $securityType = @{Name='SecurityType';Value='TrustedLaunch'}
   $features = @($securityType)
   # Image definition name
   $imageDefName = 'imageDefDevBoxVSCode01'

   # Create the image definition
   New-AzGalleryImageDefinition -GalleryName $computeGallery -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'Contoso' -Offer 'vscodedevbox' -Sku '1-0-0' -Feature $features -HyperVGeneration 'V2'
   ```

   > **Nota:** Una imagen de equipo de desarrollo debe cumplir varios requisitos, incluido el uso de Generación 2, Hyper-V v2 y Windows 10 u 11 Empresa versión 20H2 o posterior. Para obtener su lista completa, consulta el artículo de Microsoft Learn [Configuración de la Azure Compute Gallery para Microsoft Dev Box](https://learn.microsoft.com/en-us/azure/dev-box/how-to-configure-azure-compute-gallery).

1. Ejecuta los siguientes comandos para crear un archivo vacío denominado template.json que contendrá una plantilla de ARM que define una imagen de Windows 11 Empresas con Choco instalado automáticamente y Visual Studio Code:

   ```powershell
   Set-Location -Path ~
   $templateFile = 'template.json'
   Set-Content -Path $templateFile -Value ''
   ```

1. En la sesión de PowerShell de Cloud Shell, usa el editor de texto nano para agregar el siguiente contenido al archivo recién creado:

   > **Nota:** Ejecuta el comando `nano ./template.json` para abrir el editor de texto. Para guardar los cambios y salir del editor de texto nano, presiona **Ctrl+X**, a continuación **Y** y, por último **, Entrar**.

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "imageTemplateName": {
         "type": "string"
       },
       "api-version": {
         "type": "string"
       },
       "svclocation": {
         "type": "string"
       }
     },
     "variables": {},
     "resources": [
       {
         "name": "[parameters('imageTemplateName')]",
         "type": "Microsoft.VirtualMachineImages/imageTemplates",
         "apiVersion": "[parameters('api-version')]",
         "location": "[parameters('svclocation')]",
         "dependsOn": [],
         "tags": {
           "imagebuilderTemplate": "win11multi",
           "userIdentity": "enabled"
         },
         "identity": {
           "type": "UserAssigned",
           "userAssignedIdentities": {
             "<imgBuilderId>": {}
           }
         },
         "properties": {
           "buildTimeoutInMinutes": 100,
           "vmProfile": {
             "vmSize": "Standard_DS2_v2",
             "osDiskSizeGB": 127
           },
           "source": {
             "type": "PlatformImage",
             "publisher": "MicrosoftWindowsDesktop",
             "offer": "Windows-11",
             "sku": "win11-21h2-ent",
             "version": "latest"
           },
           "customize": [
             {
               "type": "PowerShell",
               "name": "Install Choco and Vscode",
               "inline": [
                 "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))",
                 "choco install -y vscode"
               ]
             }
           ],
           "distribute": [
             {
               "type": "SharedImage",
               "galleryImageId": "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>",
               "runOutputName": "<runOutputName>",
               "artifactTags": {
                 "source": "azureVmImageBuilder",
                 "baseosimg": "win11multi"
               },
               "replicationRegions": ["<region1>", "<region2>"]
             }
           ]
         }
       }
     ]
   }
   ```

1. Ejecuta los siguientes comandos para reemplazar los marcadores de posición de la template.json por los valores específicos del entorno de Azure:

   ```powershell
   $replRegion2 = 'eastus2'
   $templateFilePath = '.\template.json'
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<subscriptionID>', $subscriptionID | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<rgName>', $imageResourceGroup | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<runOutputName>', $runOutputName | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<imageDefName>', $imageDefName | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<sharedImageGalName>', $computeGallery | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<region1>', $location | Set-Content -Path $templateFilePath
   (Get-Content -Path $templateFilePath -Raw ) -Replace '<region2>', $replRegion2 | Set-Content -Path $templateFilePath
   ((Get-Content -Path $templateFilePath -Raw) -Replace '<imgBuilderId>', $identityNameResourceId) | Set-Content -Path $templateFilePath
   ```

1. Ejecuta el siguiente comando para enviar la plantilla al servicio Azure Image Builder (el servicio procesa la plantilla enviada mediante la descarga de los Artifacts dependientes, como scripts, y almacenarlos en un grupo de recursos de almacenamiento provisional, que incluye el prefijo de **TI\_**, para crear una imagen de máquina virtual personalizada:

   ```powershell
   New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -Api-Version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location
   ```

1. Ejecuta el siguiente comando para invocar el proceso de compilación de imagen:

   ```powershell
   Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
   ```

1. Ejecuta el siguiente comando para determinar el estado de aprovisionamiento de imágenes:

   ```powershell
   Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup | Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
   ```

   > **Nota:** La salida siguiente indicará que el proceso de compilación se ha completado correctamente:

   ```powershell
   Name                LastRunStatusRunState LastRunStatusMessage ProvisioningState
   ----                --------------------- -------------------- -----------------
   templateWinVSCode01 Succeeded                                  Succeeded
   ```

1. Como alternativa, para supervisar el progreso de la compilación, usa el siguiente procedimiento:

   1. En Azure Portal, busca y selecciona **`Image templates`**.
   1. En la página **Plantillas de imagen**, selecciona **templateWinVSCode01**.
   1. En la página **templateWinVSCode01**, en la sección **Essentials**, anota el valor de la entrada **Estado de ejecución de compilación**.

   > **Nota:** El proceso de compilación puede tardar unos 30 minutos. No esperes a que se complete la operación, sino avanza a la siguiente tarea de este ejercicio.

1. Una vez completada la compilación, en Azure Portal, busca y selecciona **`Azure compute galleries`**.
1. En la página **Galerías de Azure Compute**, selecciona **compute_gallery_01**.
1. En la página **compute_gallery_01**, asegúrate de que la pestaña **Definiciones** está seleccionada y, en la lista de definiciones, selecciona **imageDefDevBoxVSCode**.
1. En la página **imageDefDevBoxVSCode**, selecciona la pestaña **Versiones** y comprueba que la entrada **1.0.0 (versión más reciente)** aparece en la lista con el **Estado de aprovisionamiento** establecido en **Correcto**.
1. Selecciona la entrada **1.0.0 (versión más reciente)**.
1. En la página **1.0.0 (compute_gallery_01/imageDefDevBoxVSCode/1.0.0)**, revisa la configuración de la versión de la imagen de VM.

### Tarea 4: Creación de una conexión de red del Centro de desarrollo de Azure

En esta tarea, configurarás las redes del Centro de desarrollo de Azure para que se usen en un escenario que requiera conectividad privada a los recursos hospedados en una red virtual de Azure. A diferencia de la red hospedada de Microsoft que has aprovechado en el primer ejercicio de este laboratorio, las conexiones de red virtual también admiten escenarios híbridos (lo que proporciona conectividad a recursos locales) y combinación híbrida de Microsoft Entra de equipos de desarrollo de Azure (además de compatibilidad con la unión a Microsoft Entra).

1. En la ventana del explorador web que muestra Azure Portal, en el cuadro de texto **Buscar**, busca y selecciona **`Virtual networks`**.
1. En la página **Redes virtuales**, selecciona + **Crear**.
1. En la pestaña **Datos básicos** de la página **Crear red virtual**, especifica la siguiente configuración y selecciona **Siguiente**:

   | Configuración        | Valor                                                        |
   | -------------- | ------------------------------------------------------------ |
   | Suscripción   | Nombre de la suscripción a Azure que usas en este laboratorio |
   | Resource group | **rg-devcenter-01**                                          |
   | Nombre           | **vnet-01**                                                  |
   | Location       | **(EE. UU.) Este de EE. UU.**                                             |

1. En la pestaña **Seguridad** de la página **Crear red virtual**, revisa la configuración existente sin cambiar sus valores predeterminados y, a continuación, selecciona **Siguiente**.
1. En la pestaña **Direcciones de IP** de la página **Crear red virtual**, revisa la configuración existente sin cambiar sus valores predeterminados y, a continuación, selecciona **Revisar + Crear**.
1. En la pestaña **Revisar + Crear** de la página **Crear una red virtual**, selecciona **Crear**.

   > **Nota:** Espera a que se cree el vínculo de red virtual. Debería tardar menos de un minuto.

1. En Azure Portal, en el cuadro de texto **Buscar**, busca y selecciona **`Network connections`**.
1. En la página **Conexiones de red**, selecciona **+ Crear**.
1. En la pestaña **Datos básicos** de la página **Crear una conexión de red**, especifica la siguiente configuración y, a continuación, selecciona **Revisar + Crear**:

   | Configuración         | Valor                                                        |
   | --------------- | ------------------------------------------------------------ |
   | Suscripción    | Nombre de la suscripción a Azure que usas en este laboratorio |
   | Resource group  | **rg-devcenter-01**                                          |
   | Nombre            | **network-connection-vnet-01**                               |
   | Red virtual | **vnet-01**                                                  |
   | Subred          | **default**                                                  |

1. En la pestaña **Revisar + Crear** de la página **Crear una red virtual**, selecciona **Crear**.

   > **Nota:** Espera a que se cree la conexión de red. Esto puede tardar 1 minuto.

1. En Azure Portal, busca y selecciona **`Dev centers`** y, en la página de **Centros de desarrollo**, selecciona **devcenter-01**.
1. En la página **devcenter-01**, en el menú de navegación vertical del lado izquierdo, expande la sección **Configuración del equipo de desarrollo** y selecciona **Redes**.
1. En la página **Redes de devcenter-01\|**, selecciona **+ Agregar**.
1. En el panel **Agregar conexión de red**, en la lista desplegable **Conexión de red**, selecciona **network-connection-vnet-01** y, a continuación, selecciona **Agregar**.

   > **Nota:** No esperes a que se cree la conexión de red, sino que continúa con la siguiente tarea. Agregar una conexión de red puede tardar aproximadamente 1 minuto,

### Tarea 5: Adición de las definiciones de imágenes a un proyecto del Centro de desarrollo de Azure

En esta tarea, agregarás definiciones de imagen a un proyecto del Centro de desarrollo de Azure que creaste en el primer ejercicio de este laboratorio. Las definiciones de imagen combinan una imagen de Azure Marketplace o una imagen personalizada con tareas configurables que definen modificaciones adicionales que se aplicarán a la imagen subyacente. Una definición de imagen se puede usar para crear una nueva imagen (que contenga todos los cambios, incluidos los aplicados por tareas) o para crear grupos de equipos de desarrollo directamente. La creación de una imagen reutilizable minimiza el tiempo necesario para el aprovisionamiento de equipos de desarrollo.

Para configurar imágenes para personalizaciones del equipo de Microsoft Dev Box, los catálogos de nivel de proyecto deben estar habilitados (ya los has completado en el primer ejercicio de este laboratorio). En esta tarea, configurarás las opciones de sincronización del catálogo para el proyecto. Esto implicará adjuntar un catálogo que contenga archivos de definición de imagen.

1. En el explorador web que muestra Azure Portal, en el cuadro de texto **Buscar**, busca y selecciona **`Dev centers`**.
1. On the **Dev centers** page, select **devcenter-01**.
1. En la página **devcenter-01**, en el menú de navegación vertical de la izquierda, expande la sección **Administrar** y selecciona **Proyectos**.
1. En la página **devcenter-01 \|Proyectos**, en la lista de proyectos, selecciona **devcenter-project-01**.
1. En la página **devcenter-project-01**, en el menú de navegación vertical de la izquierda, expande la sección **Configuración** y selecciona **Catálogos**.
1. En la página **devcenter-project-01 \|Catálogos**, selecciona **+ Agregar**.
1. En el panel **Agregar catálogo**, en el cuadro de texto **Nombre**, escribe **`image-definitions-01`**, en la sección **Origen del catálogo**, selecciona **GitHub**, en **Tipo de autenticación**, selecciona **Aplicación GitHub**, deja habilitada la casilla **Sincronizar automáticamente este catálogo** y después selecciona **Iniciar sesión con GitHub**.
1. Si se te solicita, en la ventana **Iniciar sesión con GitHub**, escribe tus credenciales de GitHub y selecciona **Iniciar sesión**.

   > **Nota:** Debes bifurcar el repositorio https://github.com/MicrosoftLearning/contoso-co-eShop en la cuenta de GitHub antes de poder completar este paso.

1. Si se te solicita, en la ventana **Autorizar Microsoft DevCenter**, selecciona **Autorizar Microsoft DevCenter**.
1. De nuevo en el panel **Agregar catálogo**, en la lista desplegable **Repositorio**, selecciona **contoso-co-eShop**, en la lista desplegable **Rama**, acepta la entrada **Rama predeterminada**, en la **Ruta de la carpeta**, escribe **`.devcenter/catalog/image-definitions`** y después selecciona **Agregar**.
1. De vuelta en la página **devcenter-project-01 \|Catálogos**, comprueba que la sincronización se completa correctamente mediante la supervisión de la entrada en la columna **Estado**.
1. Selecciona el vínculo **Sincronización correcta** en la columna **Estado**, revisa el panel de notificaciones resultante, comprueba que se han agregado 3 elementos al catálogo y cierra el panel seleccionando el símbolo **x** en la esquina superior derecha.
1. De vuelta en la página **devcenter-project-01 \|Catálogos**, selecciona **image-definitions-01** y comprueba que contiene tres entradas denominadas **ContosoBaseImageDefinition**, **backend-eng** y **frontend-eng**.

   > **Nota:** Para este laboratorio, probarás la funcionalidad de la definición de imagen **frontend-eng**, que tiene el siguiente contenido:

   ```yaml
   $schema: "1.0"
   name: "frontend-eng"
   # Using the "Windows 11 Enterprise 24H2" image as the base
   image: microsoftwindowsdesktop_windows-ent-cpc_win11-24H2-ent-cpc
   description: "This definition is for the eShop frontend engineering environment"

   tasks:
     - name: ~/winget
       description: Install Visual Studio Code
       parameters:
         package: Microsoft.VisualStudioCode
         runAsUser: true
   ```

1. En Azure Portal, vuelve a la página **devcenter-project-01**, en el menú de navegación vertical de la izquierda, expande la sección **Administrar**, selecciona **Definiciones de imagen** y comprueba que la página muestra las mismas 3 definiciones de imagen que has identificado anteriormente en esta tarea.

### Tarea 6: Creación de un grupo de equipos de desarrollo personalizado

En esta tarea, usarás las definiciones de imagen recién aprovisionadas para crear un grupo de equipos de desarrollo. El grupo también usará la conexión de red que has configurado anteriormente en este ejercicio.

1. En Azure Portal que muestra la página **devcenter-project-01 \|Definiciones de imagen**, en el menú de navegación vertical del lado izquierdo, en la sección **Administrar**, selecciona **Grupos de cuadros de desarrollo**.
1. En la página **devcenter-project-01 \| Grupos de equipos de desarrollo**, selecciona **+ Crear**.
1. En la pestaña **Datos básicos** de la página **Crear un grupo de equipos de desarrollo**, especifica la siguiente configuración y después selecciona **Crear**:

   | Configuración                                                                                                           | Valor                                                 |
   | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------- |
   | Nombre                                                                                                              | **devcenter-project-01-devbox-pool-02**               |
   | Definición                                                                                                        | **frontend-eng**                                      |
   | Proceso                                                                                                           | **8 vCPU, 32 GB RAM**                                 |
   | Storage                                                                                                           | **SSD de 256 GB**                                        |
   | Conexión de red                                                                                                | **Implementación en una conexión de red en mi organización** |
   | Nombre de conexión a la red                                                                                           | **network-connection-vnet-01**                        |
   | Habilitación del inicio de sesión único                                                                                             | Habilitado                                               |
   | Privilegios de creador de equipo de desarrollo                                                                                        | **Administrador local**                               |
   | Habilitar la detención automática según la programación                                                                                      | Habilitado                                               |
   | Hora de detención                                                                                                         | **07:00 PM**                                          |
   | Zona horaria                                                                                                         | Tu zona horaria actual                                |
   | Habilitación de hibernación al desconectar                                                                                    | Habilitado                                               |
   | Periodo de gracia en minutos                                                                                           | **60**                                                |
   | Confirmo que mi organización tiene licencias de Ventaja híbrida de Azure, que se aplicarán a todos los equipos de desarrollo de este grupo. | Habilitado                                               |

   > **Nota:** Espera a que se cree el grupo del equipo de desarrollo. Esto puede tardar unos 2 minutos.

### Tarea 7: Cálculo de un equipo de desarrollo personalizado

En esta tarea, evaluarás la funcionalidad de un equipo de desarrollo personalizado mediante una cuenta de usuario de desarrollador de Microsoft Entra.

> **Nota:** Teniendo en cuenta el tiempo adicional necesario para completar esta tarea, su finalización es opcional.

1. Inicia un explorador web de incógnito/in-private y ve al portal para desarrolladores de Microsoft Dev Box en `https://aka.ms/devbox-portal`.
1. Cuando se te solicite que inicies sesión, proporciona las credenciales de la cuenta de usuario **devuser01**.
1. En la página **Le damos la bienvenida, devuser01** del portal para desarrolladores de Microsoft Dev Box, selecciona **+ Nuevo equipo de desarrollo**.
1. En el panel **Agregar un equipo de desarrollo**, en el cuadro de texto **Nombre**, escribe **`devuser01box02`**.
1. En la lista desplegable **Grupo de equipos de desarrollo**, selecciona **devcenter-project-01-devbox-pool-02**.
1. Revisa otra información presentada en el panel **Agregar un equipo de desarrollo**, incluyendo el nombre del proyecto, las especificaciones del grupo de equipos de desarrollo, el estado de compatibilidad con la hibernación y el tiempo de apagado programado. Además, ten en cuenta la opción de aplicar personalizaciones y la notificación de que la creación del equipo de desarrollo puede tardar hasta 65 minutos.
1. En el panel **Agregar un equipo de desarrollo**, selecciona **Crear**.
1. Una vez que el equipo de desarrollo está completamente aprovisionado y en ejecución, conéctate a él al seleccionar la opción **Conectar a través de la aplicación**.
1. En la ventana emergente titulada **Este sitio está intentando abrir el centro de conexiones de Escritorio remoto de Microsoft**, selecciona **Abrir**. Esto iniciará automáticamente una sesión de Escritorio remoto en el equipo de desarrollo.
1. Cuando se te soliciten las credenciales, autentifícate al proporcionar el nombre de usuario y la contraseña de la cuenta **devuser01**.
1. En la sesión de Escritorio remoto en el cuadro de desarrollo, comprueba que su configuración incluye una instalación de Visual Studio Code.

   > **Nota:** También puedes validar el resultado de la personalización al seleccionar el símbolo de puntos suspensivos en la interfaz **Su Dev Box** y después seleccionar **Personalizaciones** en el menú en cascada.
