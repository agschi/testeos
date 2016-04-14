<properties 
   pageTitle="Probar el rendimiento de un servicio en la nube | Microsoft Azure"
   description="Probar el rendimiento de un servicio en la nube mediante el generador de perfiles de Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="12/17/2015"
   ms.author="tarcher" />


# Probar el rendimiento de un servicio en la nube 

##Información general

Puede probar el rendimiento de un servicio en la nube de las maneras siguientes:

- Use Diagnósticos de Azure para recopilar información acerca de solicitudes y conexiones y revisar estadísticas del sitio que muestran el rendimiento del servicio desde una perspectiva del cliente. Para comenzar, consulte [Configuración de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?LinkId=623009).

- Utilice el generador de perfiles de Visual Studio para obtener un análisis exhaustivo de los aspectos de cálculo de cómo se ejecuta el servicio. Como se describe en este tema, puede utilizar el generador de perfiles para medir el rendimiento a medida que un servicio se ejecuta en Azure. Para obtener información acerca de cómo usar el generador de perfiles para medir el rendimiento a medida que un servicio se ejecuta localmente en un emulador de proceso, consulte [Prueba del rendimiento de un servicio en la nube de manera local en el emulador de proceso de Azure con el generador de perfiles de Visual Studio](http://go.microsoft.com/fwlink/p/?LinkId=262845).



## Elección de un método de prueba de rendimiento

###Use Diagnósticos de Azure para recopilar:###

- Estadísticas sobre páginas o servicios web, como solicitudes y conexiones.

- Estadísticas sobre roles, como la frecuencia con la que se reinicia un rol.

- Información general sobre el uso de la memoria, como el porcentaje de tiempo empleado por el recolector de elementos no usados o la memoria establecida de un rol en ejecución.

###Use el generador de perfiles de Visual Studio para:###

- Determine qué funciones requieren más tiempo.

- Mida el tiempo que dura cada una de las partes de un programa de cálculo intensivo.

- Compare informes de rendimiento detallados para dos versiones de un servicio.

- Analice la asignación de memoria con más detalle que el nivel de asignaciones de memoria individuales.

- Analice los problemas de simultaneidad en código multiproceso.

Al usar el generador de perfiles, puede recopilar datos cuando se ejecuta un servicio en la nube localmente o en Azure.

###Recopile datos de generación de perfiles localmente para:###

- Probar el rendimiento de una parte de un servicio en la nube, como la ejecución de un rol de trabajo específico, que no requiere una carga simulada realista.

- Probar el rendimiento de un servicio en la nube en aislamiento, en condiciones controladas.

- Probar el rendimiento de un servicio en la nube antes de implementarlo en Azure.

- Probar el rendimiento de un servicio en la nube de forma privada, sin interrumpir las implementaciones existentes.

- Probar el rendimiento del servicio sin cargos de ejecución en Azure.

###Recopile datos de generación de perfiles en Azure para:###

- Probar el rendimiento de un servicio en la nube bajo una carga simulada o real.

- Utilizar el método de instrumentación para recopilar datos de generación de perfiles, como se describe más adelante en este tema.

- Probar el rendimiento del servicio en el mismo entorno que cuando el servicio se ejecuta en producción.

Normalmente, simula una carga para probar servicios en la nube en condiciones normales o de carga.

## Generación de perfiles de un servicio en la nube en Azure

Al publicar su servicio en la nube desde Visual Studio, puede generar perfiles del servicio y especificar la configuración de generación de perfiles que le proporciona la información que desea. Se inicia una sesión de generación de perfiles para cada una de las instancias de un rol. Para obtener más información acerca de cómo publicar su servicio desde Visual Studio, consulte [Publicación en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Para conocer más información acerca de la generación de perfiles de rendimiento en Visual Studio, consulte [Guía básica para la generación de perfiles de rendimiento](https://msdn.microsoft.com/library/azure/ms182372.aspx) y [Analizar el rendimiento de la aplicación mediante las herramientas de generación de perfiles](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

>[AZURE.NOTE]Puede habilitar IntelliTrace o bien la generación de perfiles al publicar su servicio en la nube. No puede habilitar ambas cosas.

###Métodos de recopilación del generador de perfiles

Puede utilizar métodos de recopilación diferentes para la generación de perfiles, en función de sus problemas de rendimiento:

- **Muestreo de CPU**: este método recopila estadísticas de la aplicación que son útiles para el análisis inicial de los problemas de uso de CPU. El muestreo de CPU es el método sugerido para iniciar la mayoría de las investigaciones de rendimiento. Hay poca repercusión en cuanto a la aplicación para la cual está generando un perfil al recopilar datos de muestreo de CPU.

- **Instrumentación**: este método recopila datos de tiempo detallados que son útiles para análisis más detallados y para analizar problemas de rendimiento de entrada/salida. El método de instrumentación graba cada entrada, salida y llamada de función de las funciones en un módulo durante una ejecución de generación de perfiles. Este método es útil para recopilar información de tiempo detallada sobre una sección de su código y entender el impacto de las operaciones de entrada y salida en el rendimiento de la aplicación. Este método está deshabilitado para un equipo que ejecuta un sistema operativo de 32 bits. Esta opción está disponible solo cuando ejecuta el servicio en la nube en Azure, no localmente en el emulador de proceso.

- **Asignación de memoria de .NET**: este método recopila datos de asignación de memoria de .NET Framework con el método de generación de perfiles de muestreo. Los datos recopilados incluyen el número y tamaño de los objetos asignados.

- **Simultaneidad**: este método recopila datos de contención de recursos, así como datos de ejecución de procesos y subprocesos que son útiles para el análisis de aplicaciones multiproceso. El método de simultaneidad recopila datos de cada evento que bloquea la ejecución de su código, por ejemplo, cuando un subproceso espera a que se libere el acceso bloqueado a un recurso de aplicación. Este método es útil para analizar aplicaciones multiproceso.

- También puede habilitar **generación de perfiles de interacción de capa**, que ofrece información adicional sobre los tiempos de ejecución de llamadas de ADO.NET sincrónicas en funciones de aplicaciones de varias capas que se comunican con una o más bases de datos. Puede recopilar datos de interacción de capa con cualquiera de los métodos de generación de perfiles. Para obtener información acerca de la generación de perfiles de interacción de capa, consulte la [vista Interacciones de capa](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## Configuración de opciones de generación de perfiles

La ilustración siguiente muestra cómo configurar sus opciones de generación de perfiles desde el cuadro de diálogo Publicar aplicación de Azure.

![Configurar opciones de generación de perfiles](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

>[AZURE.NOTE]Para activar la casilla **Habilitar generación de perfiles** debe tener instalado el generador de perfiles en el equipo local que está usando para publicar su servicio en la nube. De forma predeterminada, el generador de perfiles se instala al instalar Visual Studio.

### Para configurar opciones de generación de perfiles

1. En el Explorador de soluciones, abra el menú contextual de su proyecto de Azure y, a continuación, elija **Publicar**. Para obtener pasos detallados acerca de cómo publicar un servicio en la nube, consulte [Publicar un servicio en la nube mediante Azure Tools](http://go.microsoft.com/fwlink/p?LinkId=623012).

1. En el cuadro de diálogo **Publicar aplicación de Azure**, elija la pestaña **Configuración avanzada**.

1. Para habilitar la generación de perfiles, seleccione la casilla **Habilitar generación de perfiles**.

1. Para configurar sus opciones de generación de perfiles, elija el hipervínculo **Configuración**. Aparece el cuadro de diálogo Configuración de generación de perfiles.

1. En los botones de opción **¿Qué método de generación de perfiles desea usar?**, elija el tipo de generación de perfiles que necesita.

1. Para recopilar los datos de generación de perfiles de interacción de capa, seleccione la casilla **Habilitar generación de perfiles de interacción de capa**.

1. Para guardar la configuración, elija el botón **Aceptar**.

    Al publicar esta aplicación, esta configuración se usa para crear la sesión de generación de perfiles para cada rol.

## Vista de informes de generación de perfiles

Se crea una sesión de generación de perfiles para cada instancia de un rol en su servicio en la nube. Para ver sus informes de generación de perfiles de cada sesión en Visual Studio, puede ver la ventana Explorador de servidores y, a continuación, elegir el nodo de proceso de Azure para seleccionar una instancia de un rol. Es entonces cuando puede ver el informe de generación de perfiles como se muestra en la siguiente ilustración.

![Ver informe de generación de perfiles desde Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### Para ver informes de generación de perfiles

1. Para ver la ventana Explorador de servidores en Visual Studio, en la barra de menús elija Ver, Explorador de servidores.

1. Elija el nodo de proceso de Azure y, a continuación, elija el nodo de implementación de Azure para el servicio en la nube que seleccionó para generar perfiles cuando publicó desde Visual Studio.

1. Para ver los informes de generación de perfiles para una instancia, elija el rol en el servicio, abra el menú contextual para una instancia específica y, a continuación, elija **Ver el informe de generación de perfiles**.

    El informe, un archivo .vsp, se descarga desde Azure y el estado de la descarga aparece en el registro de actividad de Azure. Al completarse la descarga, el informe de generación de perfiles aparece en una pestaña en el editor de Visual Studio denominada <Role name>\_<Instance Number>\_<identifier>.vsp. Aparecen datos de resumen del informe.

1. Para mostrar diferentes vistas del informe, en la lista Vista actual, elija el tipo de vista que desee. Para obtener más información, consulte [Vistas de informes de las herramientas de generación de perfiles](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## Pasos siguientes

[Depuración de Servicios en la nube](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicación en un servicio en la nube de Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

<!---HONumber=AcomDC_1223_2015-->