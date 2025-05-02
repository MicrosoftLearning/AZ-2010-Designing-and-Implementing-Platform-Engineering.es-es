---
lab:
  title: Implementación de la supervisión en tiempo real con Azure Monitor
  module: Observability and Continuous Improvement
---

# Laboratorio 02: Implementación de la supervisión en tiempo real con Azure Monitor

## Tiempo estimado: 20 minutos

## Requisitos previos

- Suscripción de Azure: Si no tienes una suscripción de Azure, crea una [cuenta gratuita](https://azure.microsoft.com/free/).
- Conocimientos básicos sobre los conceptos de supervisión y observabilidad.
- Conocimientos básicos de los servicios de Azure.

## Objetivos

- Crea una aplicación web de ejemplo.
- Habilita y configura Azure Monitor y Application Insights.
- Crea paneles personalizados para visualizar las métricas de la aplicación.
- Configura alertas para notificar a las partes interesadas las anomalías de rendimiento.
- Analiza los datos de rendimiento para ver posibles mejoras.

## Ejercicio 1: Creación de una aplicación web de ejemplo

Como ingeniero de plataforma, debes asegurarte de que las aplicaciones que se ejecutan en la plataforma son observables y se supervisan continuamente. En este laboratorio, crearás una aplicación web de ejemplo en Azure, configurarás Azure Monitor y Application Insights, configurarás paneles personalizados y crearás alertas para realizar un seguimiento del rendimiento de la aplicación.

### Tarea 1: Crear una aplicación web de Azure

1. Abra Azure Portal.
1. En la barra de búsqueda, escribe App Services y selecciónalo.
1. Haz clic en + Crear y selecciona Aplicación Web.
1. En la pestaña Básica:
   - Suscripción: Seleccione su suscripción a Azure.
   - Grupo de recursos: haz clic en Crear nuevo, escribe **`monitoringlab-rg`** y haz clic en Aceptar.
   - Nombre: escribe un nombre único, como **`monitoringlab-webapp`**.
   - Publicar: seleccione Código.
   - Pila en tiempo de ejecución: elige .NET 8 (LTS).
   - Región: selecciona una región cercana.
1. Haz clic en Revisar + crear y después en Crear.
1. Espera a que se complete la implementación y después haz clic en "Ir al recurso".
1. En la pestaña Información general, haz clic en la dirección URL para comprobar que la aplicación web se esté ejecutando.

### Tarea 2: Comprobar Application Insights

1. En el recurso Aplicación web, desplázate hasta la sección Supervisión.
1. Haz clic en Application Insights.
1. Application Insights ya está habilitado para esta aplicación web. Haz clic en el vínculo para abrir el recurso de Application Insights.
1. En el recurso de Application Insights, haz clic en el panel de aplicaciones para ver los datos de rendimiento que proporciona el panel predeterminado.

## Ejercicio 2: Configuración de Azure Monitor y paneles

### Tarea 1: Acceder a Azure Monitor

1. En Azure Portal, busca Monitor y haz clic en él.
1. Haz clic en Métricas en el panel izquierdo.
1. En la sección Ámbito, selecciona la aplicación web en la suscripción y el grupo de recursos donde has implementado la aplicación web.
1. Haz clic en Aplicar y observa las métricas disponibles para la aplicación web.

### Tarea 2: Agregar métricas clave al panel

1. En la sección Ámbito, selecciona la aplicación web (monitoringlab-webapp).
1. En Métrica, elige Tiempo de respuesta.
1. Establece la agregación en Promedio y haz clic en + Agregar métrica.
1. Repite el proceso para métricas adicionales:
   - Tiempo de CPU (recuento)
   - Solicitudes (promedio)
1. Haz clic en el panel y ánclalo en el panel.
1. Selecciona el tipo Compartido y selecciona la suscripción y el panel de monitoringlab-webapp.
1. Haga clic en Anclar al panel.
1. Haz clic en el icono del panel de la izquierda para ver el panel.
1. Comprueba que las métricas se muestren en el panel y se actualicen en tiempo real.

## Ejercicio 3: Creación de alertas

### Tarea 1: Definir las condiciones y acciones de alerta

1. En Azure Monitor, haz clic en Alertas.
1. Haz clic en + Crear y selecciona Regla de alertas.
1. En Ámbito, selecciona la aplicación web (monitoringlab-webapp) y haz clic en Aplicar.
1. En Condición, haz clic en el campo Nombre de señal y selecciona Tiempo de respuesta.
1. Configuración de la regla de alertas:
   - Tipo de umbral: dinámico
   - Tipo de agregación: promedio
   - El valor es: Mayor o menor que
   - Sensibilidad de umbral: Alta
   - Cuándo evaluar: cada minuto, revisa los 5 minutos anteriores.
1. En Acciones, selecciona Usar acciones rápidas.
1. Especifique:
   - Nombre del grupo de acciones: WebAppMonitoringAlerts
   - Nombre para mostrar: WebAlert
   - Correo electrónico: escribe tu dirección de correo electrónico.
1. Haga clic en Save(Guardar).
1. Haga clic en Siguiente: Detalles.
1. Escribe el nombre `WebAppResponseTimeAlert` y selecciona el nivel de gravedad Detallado.
1. Haga clic en Revisar y crear y, a continuación, en Crear.

   > **Nota:** La regla de alerta ya está creada y desencadenará una notificación por correo electrónico cuando el tiempo de respuesta supere el umbral. Puedes forzar que la alerta se desencadene al enviar un gran número de solicitudes a la aplicación web. Por ejemplo, puedes usar pruebas de carga de Azure o una herramienta como Apache JMeter.

1. Vuelve a Azure Monitor > Alertas.
1. Haz clic en Reglas de alertas y deberías ver la regla de alertas que has creado.

## Ejercicio 4: Análisis de datos de rendimiento

### Tarea 1: Revisar las métricas recopiladas

1. En Azure Portal, vaya a Application Insights.
1. Haz clic en el panel de la aplicación.
1. Haz clic en el icono Rendimiento para analizar los tiempos de respuesta del servidor y la carga. También puedes ver el número de solicitudes y solicitudes con errores.
1. Haz clic en Analizar con libros y selecciona Análisis de contadores de rendimiento.
1. Haz clic en Libros.
1. Selecciona el análisis de rendimiento del libro en Rendimiento.
1. Puedes ver los datos de rendimiento de la aplicación web.

> **Nota:** Puedes personalizar el libro para incluir métricas y filtros adicionales.
