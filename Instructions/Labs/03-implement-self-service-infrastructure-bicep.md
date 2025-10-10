---
lab:
  title: Implementación de la infraestructura de autoservicio con Bicep
  module: Strategic Platform Road Mapping
---

# Laboratorio 03: Implementación de la infraestructura de autoservicio con Bicep

## Tiempo estimado: 20 minutos

## Requisitos previos

- Suscripción de Azure: Si no tienes una suscripción de Azure, crea una [cuenta gratuita](https://azure.microsoft.com/free/).
- Conocimientos básicos de los servicios de Azure y CLI de Azure.
- Abre Visual Studio Code instalado con la extensión Bicep.
- CLI de Azure instalado y configurado en la máquina local.
- Familiaridad con los conceptos de infraestructura como código (IaC).

## Objetivos

- Configuración de Bicep en el entorno.
- Creación de una plantilla de Bicep para definir los recursos de Azure.
- Implementación de Azure App Service con back-end de SQL Database.
- Aplicación de la gobernanza mediante etiquetas y directivas.
- Implementación del escalado automatizado con Bicep.

## Ejercicio 1: Creación de una plantilla de Bicep para implementar recursos de Azure

En un entorno de ingeniería de plataforma, los desarrolladores necesitan una manera de aprovisionar la infraestructura de forma coherente y eficaz. Este laboratorio te guiará a través del uso de Bicep, una herramienta de infraestructura como código (IaC) para implementar y administrar los recursos de Azure de forma de autoservicio. Crearás una plantilla de Bicep para aprovisionar un grupo de recursos, un Azure App Service, una base de datos de Azure SQL y una cuenta de almacenamiento al tiempo que aplica la gobernanza con etiquetado y directivas.

### Tarea 1: Instalación de la CLI de Bicep

1. Abre el terminal.
1. Inicie sesión en una cuenta de Azure:

   ```bash
   az login
   ```

   > **NOTA:** Siga las instrucciones para autenticarse con la cuenta de Azure. Esto abrirá un explorador web para la autenticación.

1. Para comprobar que Bicep está instalado, ejecuta:

   ```bash
   az bicep version
   ```

   Si Bicep todavía no se ha instalado, instálalo con:

   ```bash
   az bicep install
   ```

1. Confirma la instalación mediante la ejecución de:

   ```bash
   az bicep version
   ```

   Deberías ver la salida como la versión X.Y.Z de la CLI de Bicep.

   > **NOTA:** Asegúrate de que tienes instalada la [CLI de Azure](https://learn.microsoft.com/cli/azure/install-azure-cli).

### Tarea 2: Creación de plantillas de Bicep

1. En el terminal local, crea un nuevo archivo:

   ```bash
   code main.bicep
   ```

1. Copia y pega el siguiente código de Bicep en el archivo:

   ```bicep
   param location string = 'eastus2'
   param appServicePlanName string = 'bicepAppPlan'
   param webAppName string = 'bicep-webapp'
   param storageAccountName string = 'biceplabstorage'
   param sqlServerName string = 'bicep-sqlserver'
   param sqlDatabaseName string = 'bicepdb'
   param sqlAdminUser string
   @secure()
   param sqlAdminPassword string

   targetScope = 'resourceGroup'

   resource storage 'Microsoft.Storage/storageAccounts@2021-09-01' = {
     name: storageAccountName
     location: location
     sku: {
       name: 'Standard_LRS'
     }
     kind: 'StorageV2'
   }

   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'F1'
       tier: 'Free'
     }
   }

   resource webApp 'Microsoft.Web/sites@2021-02-01' = {
     name: webAppName
     location: location
     properties: {
       serverFarmId: appPlan.id
     }
   }

   resource sqlServer 'Microsoft.Sql/servers@2022-05-01-preview' = {
     name: sqlServerName
     location: location
     properties: {
       administratorLogin: sqlAdminUser
       administratorLoginPassword: sqlAdminPassword
       version: '12.0'
     }
   }

   resource sqlDb 'Microsoft.Sql/servers/databases@2022-05-01-preview' = {
     name: sqlDatabaseName
     location: location
     parent: sqlServer
     properties: {
       collation: 'SQL_Latin1_General_CP1_CI_AS'
       maxSizeBytes: 2147483648
     }
   }
   ```

   > **NOTA:** Puedes instalar la extensión de Bicep en Visual Studio Code para obtener el resaltado de sintaxis e IntelliSense para archivos de Bicep.

   > **IMPORTANTE:** Asegúrate de que storageAccountName, webAppName y sqlServerName son únicos globalmente. Si se produce un error en la implementación debido a conflictos de nombres, modifica los nombres en consecuencia.

   > **NOTA:** Si se produce un error relacionado con la región, intenta implementar en otra región cambiando el valor del parámetro `location`.

1. Guarde y cierre el archivo.

### Tarea 3: Implementación de una plantilla con la CLI de Azure

1. Ejecuta el siguiente comando para crear el grupo de recursos:

   ```bash
   az group create --name BicepLab-RG --location centralus

   ```

   > **NOTA:** Es posible que tengas que iniciar sesión en tu cuenta de Azure si aún no estás autenticado. Para obtener instrucciones detalladas, consulta [Autenticación en Azure mediante la CLI de Azure](https://learn.microsoft.com/cli/azure/authenticate-azure-cli).

2. Implementa la plantilla en el grupo de recursos:

   ```bash
   az deployment group create --resource-group BicepLab-RG --template-file main.bicep --parameters sqlAdminUser='azureuser' sqlAdminPassword='YourSecurePassword123!'

   ```

   > **NOTA:** Reemplaza ¡TuContraseñasegura123! con una contraseña segura y tu azureuser con un nombre de usuario válido.

3. Espera a que la implementación se complete. Aparece un mensaje de confirmación.
4. En Azure Portal, ve a Grupos de recursos.
5. Busca BicepLab-RG y haz clic en él.
6. Comprueba que los recursos se crearon correctamente.

Has implementado correctamente un Azure App Service con un back-end de SQL Database mediante una plantilla de Bicep. Ahora puedes administrar la infraestructura como código e implementar recursos de forma coherente y eficaz. El siguiente paso sería integrar esta plantilla en la canalización de CI/CD para implementaciones automatizadas.

## Ejercicio 2: Aplicación de la gobernanza con etiquetas y directivas

En un entorno de infraestructura de autoservicio, es esencial aplicar la gobernanza para garantizar el cumplimiento y el control de costos. Las etiquetas y las directivas son dos mecanismos clave para lograrlo.

### Tarea 1: Adición de etiquetas a los recursos

1. Ejecuta el siguiente comando para agregar etiquetas al grupo de recursos:

   ```bash
   az group update --name BicepLab-RG --set tags.Owner='PlatformEngineering'
   ```

1. Comprueba que la etiqueta se agregó ejecutando:

   ```bash
   az group show --name BicepLab-RG --query tags
   ```

### Tarea 2: Creación de una directiva para aplicar el etiquetado

1. Crea un archivo de directiva JSON:

   ```bash
   code tagging-policy.json
   ```

1. Copia y pega la siguiente definición de directiva en el archivo:

   ```json
   {
     "if": {
       "not": {
         "field": "tags[Owner]",
         "exists": "true"
       }
     },
     "then": {
       "effect": "deny"
     }
   }
   ```

1. Crea la definición de directiva usando el archivo JSON:

   ```bash
    az policy definition create --name 'tagging-policy' --display-name 'Enforce tagging' --rules @tagging-policy.json --mode All
   ```

1. Asigna la directiva al grupo de recursos:

   ```bash
   az policy assignment create --name 'tagging-policy-assignment' --display-name 'Enforce tagging' --policy "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policyDefinitions/tagging-policy" --scope "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG"

   ```

   > **IMPORTANTE:** Reemplaza <subscription-id> por el id. de suscripción a Azure

1. Comprueba que la directiva se asignó mediante la ejecución de:

   ```bash
   az policy assignment list --output table
   ```

### Tarea 3: Prueba del cumplimiento de directivas

1. Para eliminar la etiqueta del grupo de recursos, ejecuta el siguiente comando:

   ```bash
   az tag update --resource-id /subscriptions/<subscription-id>/resourceGroups/BicepLab-RG --operation delete --tags Owner
   ```

   > **IMPORTANTE:** Reemplaza <subscription-id> por el id. de suscripción de Azure.

1. Deberías ver un mensaje de error que indica que se denegó la operación debido al cumplimiento de directivas. No se debe quitar la etiqueta. Esto confirma que las directivas funcionan según lo previsto. Para resolver el error, quita temporalmente la asignación de directivas y vuelve a ejecutar el comando.

   ```bash
   az policy exemption create --name 'tagging-policy-exemption' --policy-assignment "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG/providers/Microsoft.Authorization/policyAssignments/tagging-policy-assignment" --scope "/subscriptions/<subscription-id>/resourceGroups/BicepLab-RG" --display-name "Temporary exemption to remove tag" --exemption-category Waiver
   ```

   > **IMPORTANTE:** Reemplaza <subscription-id> por el id. de suscripción a Azure

Has aplicado correctamente la gobernanza mediante etiquetas y directivas. Ahora, los desarrolladores tendrán que etiquetar los recursos con la etiqueta Propietario, lo que garantiza la propiedad y la responsabilidad adecuadas. Puedes crear directivas adicionales para aplicar otros requisitos de gobernanza, como convenciones de nomenclatura de recursos, tipos de recursos y ubicaciones de recursos.

## Ejercicio 3: Implementación del escalado automatizado con Bicep

En un entorno de ingeniería de plataforma, garantizando que las aplicaciones se pueden escalar de forma eficaz es un requisito clave. El escalado automatizado mejora el rendimiento, optimiza el uso de recursos y minimiza los costos. En este ejercicio, implementarás el escalado automático para un Azure App Service mediante Bicep.

### Tarea 1: Definición de una directiva de escalado automático en Bicep

1. Abre el archivo main.bicep y busca la sección Plan de App Service existente:

   ```bicep
   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'F1'
       tier: 'Free'
     }
   }
   ```

1. Modifica el recurso appPlan para usar la SKU S1 (que admite el escalado automático):

   ```bicep
   resource appPlan 'Microsoft.Web/serverfarms@2021-02-01' = {
     name: appServicePlanName
     location: location
     sku: {
       name: 'S1'
       tier: 'Standard'
     }
     properties: {
       perSiteScaling: false
       maximumElasticWorkerCount: 10
     }
   }
   ```

1. Inmediatamente después de este recurso, agrega la configuración de autoscaleSetting:

   ```bicep
   resource autoscaleSetting 'Microsoft.Insights/autoscaleSettings@2022-10-01' = {
   name: 'autoscale-rule'
   location: location
   properties: {
    profiles: [
      {
        name: 'defaultProfile'
        capacity: {
          minimum: '1'
          maximum: '5'
          default: '1'
        }
        rules: [
          {
            metricTrigger: {
              metricName: 'CpuPercentage'
              metricResourceUri: resourceId('Microsoft.Web/serverfarms', appServicePlanName)
              operator: 'GreaterThan'
              threshold: 75
              timeAggregation: 'Average'
              statistic: 'Average'
              timeWindow: 'PT5M'
              timeGrain: 'PT1M'
            }
            scaleAction: {
              direction: 'Increase'
              type: 'ChangeCount'
              value: '1'
              cooldown: 'PT1M'
            }
          }
        ]
      }
    ]
   }
   }
   ```

1. Guarde el archivo.
1. Valida el archivo de Bicep actualizado:

   ```bash
   az bicep build --file main.bicep
   ```

1. Implementa la plantilla actualizada:

   ```bash
   az deployment group create --resource-group BicepLab-RG --template-file main.bicep --parameters sqlAdminUser='azureuser' sqlAdminPassword='YourSecurePassword123!'

   ```

1. Comprueba que la directiva de escalado automático se aplicó al Plan de App Service. Ve a Azure Portal, ve al Plan de App Service, selecciona la hoja Escalar horizontalmente (Plan de App Service) y comprueba que la regla de escalado automático está configurada.

   > **NOTA:** Si deseas probar el comportamiento de escalado automático, puedes simular un uso elevado de CPU en App Service mediante la ejecución de una prueba de carga o la generación de tráfico. App Service debería escalar horizontalmente y automáticamente en función de la regla definida.

Has implementado correctamente el escalado automatizado para un Azure App Service mediante Bicep. Esto garantizará que las aplicaciones puedan gestionar el aumento del tráfico y la demanda de forma eficaz, lo que mejora el rendimiento y reduce los costes.
