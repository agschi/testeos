<properties
   pageTitle="Orientación sobre escalado automático | Microsoft Azure"
   description="Orientación sobre cómo realizar el escalado automático para asignar dinámicamente los recursos que requiere una aplicación."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/04/2016"
   ms.author="masashin"/>

# Instrucciones de escalado automático

![](media/best-practices-auto-scaling/pnp-logo.png)

## Información general
El escalado automático es el proceso de asignar dinámicamente los recursos que necesita una aplicación para coincidir con los requisitos de rendimiento y satisfacer los contratos de nivel de servicio (SLA) mientras se minimizan los costos de tiempo de ejecución. A medida que crezca el volumen de trabajo, una aplicación puede requerir recursos adicionales para que pueda realizar sus tareas de manera oportuna. A medida que disminuya la demanda, los recursos pueden anular la asignación con el fin de minimizar los costos a la vez que se mantiene un rendimiento adecuado y se cumplen los SLA. El escalado automático aprovecha la elasticidad de los entornos hospedados en la nube mientras alivia la sobrecarga de administración al reducir la necesidad de que un operador tenga que supervisar continuamente el rendimiento de un sistema y tomar decisiones sobre cómo agregar o quitar recursos.
> El escalado automático se aplica a todos los recursos que una aplicación usa, no solo los recursos de proceso. Por ejemplo, si el sistema usa colas de mensajes para enviar y recibir información, podría crear colas adicionales a medida que escale.

## Tipos de escalado
El escalado suele tomar una de dos formas: escalado horizontal y vertical.

- El **escalado vertical** (con frecuencia se conoce como _escalado hacia arriba y abajo_) requiere la modificación del hardware (ampliación o reducción de su capacidad y rendimiento) o la reimplementación de la solución con hardware alternativo que tenga la capacidad y el rendimiento adecuados. En un entorno de nube, la plataforma de hardware suele ser un entorno virtualizado. A menos que el hardware original se había sobreaprovisionado considerablemente, con el consiguiente gasto de capital inicial, el escalado vertical en este entorno implica el aprovisionamiento de recursos más eficaces y, a continuación, el traslado del sistema en estos nuevos recursos. El escalado vertical suele ser un proceso perjudicial que requiere que el sistema deje de estar disponible temporalmente mientras se vuelve a implementar. Es posible mantener el sistema original en ejecución mientras se aprovisiona y se pone en línea el nuevo hardware, pero es probable que abra alguna interrupción mientras el procesamiento pasa del entorno antiguo al nuevo. No es habitual usar el escalado automático para implementar una estrategia de escalado vertical.
- El **escalado horizontal** (con frecuencia se conoce como _ampliación y reducción_) requiere la implementación de la solución en más o menos recursos, que suelen ser recursos básicos en lugar de sistemas de alta potencia. La solución puede seguir ejecutándose sin interrupciones mientras se aprovisionan estos recursos. Una vez completado el proceso de aprovisionamiento, se pueden implementar copias de los elementos que componen la solución en estos recursos adicionales y hacer que estén disponibles. Si disminuye la demanda, se pueden reclamar recursos adicionales después de que los elementos los usan que se hayan cerrado correctamente. Muchos sistemas basados en la nube, incluido Microsoft Azure, admiten la automatización de esta forma de escalado.

## Implementación de una estrategia de escalado automático
La implementación de una estrategia de escalado automático suele implicar los siguientes componentes y procesos:

- Sistemas de instrumentación y supervisión en los niveles de aplicación, servicio e infraestructura que capturan métricas clave, tales como tiempos de respuesta, longitudes de cola, utilización de la CPU y uso de la memoria.
- Lógica de toma de decisiones que puede evaluar los factores de escalado supervisados en comparación con umbrales o programaciones predefinidos del sistema y tomar decisiones sobre si cambiar según la escala o no.
- Componentes responsables de llevar a cabo tareas asociadas con el escalado del sistema, tal como el aprovisionamiento o desaprovisionamiento de recursos.
- Pruebas, supervisión y ajuste de la estrategia de escalado automático para asegurarse de que funciona según lo esperado.

La mayoría de los entornos basados en la nube, como Microsoft Azure, proporcionan mecanismos de escalado automático integrados que si dirigen a escenarios comunes. Si el entorno o servicio que usa no proporciona la funcionalidad de escalado automatizado necesaria o si tiene requisitos extremos de escalado automático que van más allá de sus capacidades, es posible que se necesite una implementación personalizada para recopilar métricas operativas y de sistema, analizar estas para identificar los datos pertinentes y luego escalar los recursos según corresponda.

## Consideraciones para la implementación del escalado automático
El escalado automático no es una solución instantánea. La simple adición de recursos a un sistema o la ejecución de instancias adicionales de un proceso no garantiza que mejorará el rendimiento del sistema. Tenga en cuenta lo siguiente a la hora de diseñar una estrategia de escalado automático:

- El sistema debe diseñarse para el escalado horizontal. Evite hacer suposiciones acerca de la afinidad de la instancia; no diseñe soluciones que requieran la ejecución constante del código en una instancia específica de un proceso. Al escalar horizontalmente un servicio en la nube o sitio web, no presuponga que una serie de solicitudes del mismo origen siempre se enrutará a la misma instancia. Por la misma razón, diseñe los servicios sin estado para evitar la necesidad de disponer de una serie de solicitudes de que una aplicación siempre se dirija a la misma instancia de un servicio. Al diseñar un servicio que lee y procesa los mensajes de una cola, no haga ninguna suposición sobre qué instancia de los identificadores de servicio controla un mensaje específico, ya que el escalado automático podría iniciar instancias adicionales de un servicio a medida que crezca la longitud de la cola. En el [patrón de consumidores de la competencia](http://msdn.microsoft.com/library/dn568101.aspx) se describe cómo controlar esta situación.
- Si la solución implementa una tarea de ejecución prolongada, diseñe esta tarea para que admita el escalado horizontal (tanto reducción como ampliación). Sin la debida atención, este tipo de tarea podría impedir que una instancia de un proceso se apague correctamente cuando se reduce la escala del sistema, o bien podría perder datos si el proceso finaliza de manera forzada. De manera ideal, refactorice una tarea de ejecución prolongada y divida el procesamiento que realiza en fragmentos más pequeños y discretos. En el [patrón de canalizaciones y filtros](http://msdn.microsoft.com/library/dn568100.aspx) se proporciona un ejemplo de cómo puede lograr esto. Como alternativa, puede implementar un mecanismo de punto de comprobación que registre información sobre el estado de la tarea en intervalos regulares y que guarde este estado en almacenamiento duradero accesible para cualquier instancia del proceso que ejecute la tarea. De este modo, si el proceso se cierra, el trabajo que estaba realizando puede reanudarse desde el último punto de comprobación con otra instancia.
- Cuando las aplicaciones de segundo plano se ejecutan en instancias de proceso independientes, como es el caso en los roles de trabajo de una aplicación hospedada en los Servicios en la nube, es posible que necesite escalar diferentes partes de la aplicación con distintas directivas de escalado. Por ejemplo, es posible que tenga que implementar instancias adicionales de proceso de interfaz de usuario sin aumentar el número de instancias de proceso de segundo plano o viceversa. Si ofrece diferentes niveles de servicio (por ejemplo, paquetes de servicio básico y premium), es posible que, para cumplir el SLA, tenga que escalar horizontalmente los recursos de proceso para los paquetes de servicio premium de forma más agresiva que aquellos para los paquetes de servicio básico.
- Considere el uso de la longitud de la cola sobre la que se comunican las instancias de proceso de interfaz de usuario y segundo plano como impulsor para su estrategia de escalado automático. Este es el mejor indicador de la existencia de un desequilibrio o diferencia entre la carga actual y la capacidad de procesamiento de la tarea en segundo plano.
- Si basa su estrategia de escalado automático en contadores que miden procesos empresariales, tal como el número de pedidos realizados por hora o el tiempo de ejecución promedio de una transacción compleja, asegúrese de comprender a fondo la relación entre los resultados de estos tipos de contadores y los requisitos reales de capacidad de proceso. Es posible que sea necesario escalar más de un componente o unidad de proceso en respuesta a los cambios en los contadores de proceso de negocio.  
- Para impedir que un sistema intente escalar horizontalmente de manera excesiva y evitar los costos asociados con la ejecución de varios miles de instancias, considere la posibilidad de limitar el número máximo de instancias que se pueden agregar automáticamente. La mayoría de los mecanismos de escalado automático le permiten especificar el número mínimo y máximo de instancias de una regla. Además, considere la posibilidad de degradar correctamente la funcionalidad que proporciona el sistema si se implementó el número máximo de instancias y el sistema aún está sobrecargado.
- Tenga en cuenta que el escalado automático podría no ser el mecanismo más adecuado para controlar una ráfaga súbita de carga de trabajo. Se necesita tiempo para aprovisionar e iniciar nuevas instancias de un servicio o agregar recursos a un sistema, y es posible que el pico se haya superado para cuando estos recursos adicionales se hagan disponibles. En este escenario, es posible que sea mejor limitar el servicio. Para más información, consulte el [patrón de limitación](http://msdn.microsoft.com/library/dn589798.aspx).
- Por el contrario, si necesita la capacidad para procesar todas las solicitudes cuando el volumen fluctúa rápidamente, y el costo no es un factor de impacto principal, considere el uso de una estrategia agresiva de escalado automático en la que se inician instancias adicionales con mayor rapidez o usar una directiva programada que inicia un número suficiente de instancias como para satisfacer la carga máxima antes de que se espere esa carga.
- El mecanismo de escalado automático debe supervisar el proceso de escalado automático y registrar los detalles de cada evento escalado automático (qué lo desencadenó, qué recursos se agregaron o quitado y cuándo). Si crea un mecanismo de escalado automático personalizado, asegúrese de que incorpore esta capacidad. La información se puede analizar para ayudar a medir la eficacia de la estrategia de escalado automático y ajustarla según sea necesario. Tanto a corto plazo a medida que los patrones de uso se hace más evidentes, y a largo plazo a medida que el negocio se expanda o evolucionen los requisitos de la aplicación. Si una aplicación alcanzara el límite definido para el escalado automático, el mecanismo también podría alertar a un operador, quién podrían iniciar manualmente recursos adicionales si la situación lo necesitara. Tenga en cuenta que, en estas circunstancias, el operador también puede ser responsable de quitar manualmente estos recursos una vez que se reduzca la carga de trabajo.

## Escalado automático en una solución de Azure
Hay varias opciones para configurar el escalado automático en las soluciones de Azure:

- **Escalado automático de Azure**. Esta característica admite los escenarios de escalado más comunes basados en una programación y, opcionalmente, las operaciones de escalado desencadenas basadas en métricas en tiempo de ejecución (por ejemplo, utilización del procesador, longitud de la cola o contadores integrados o personalizados). Puede configurar directivas sencillas de escalado automático para una solución rápida y fácilmente mediante el Portal de administración de Azure. Para un control más minucioso puede hacer uso de la [API de REST de administración de servicios de Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx) o la [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com//library/azure/dn790568.aspx). La [biblioteca de administración de servicios de supervisión de Azure](http://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring) y la [biblioteca de Microsoft Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/) (en vista preliminar) son SDK que permiten recopilar métricas de diferentes recursos y realizar escalado automático haciendo uso de las API de REST. Para recursos donde el Administrador de recursos de Azure no está disponible o si está utilizando Servicios en la nube, puede usar la API de REST de administración de servicios para escalado automático. Se recomienda usar el Administrador de recursos de Azure (ARM) para el escalado ajuste automático en los demás casos.
- **Una solución personalizada** basada en la instrumentación de la aplicación y características de administración de Azure. Por ejemplo, podría usar Diagnósticos de Azure u otros métodos de instrumentación en la aplicación junto con código personalizado para supervisar continuamente y exportar las métricas de la aplicación. Podría tener reglas personalizadas que funcionaran en estas métricas que harían uso de las API de REST de administración de servicios y del Administrador de recursos para desencadenar el escalado automático. Las métricas para desencadenar una operación de escalado pueden ser cualquier contador integrado o personalizado, u otra instrumentación que implemente dentro de la aplicación. Sin embargo, una solución personalizada no es fácil de implementar y solo debería considerarse si ninguno de los métodos anteriores pueden satisfacer sus requisitos. El [bloque de escalado automático de la aplicación](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx) hizo uso de este enfoque.
- **Servicios de terceros**, como [Paraleap AzureWatch](http://www.paraleap.com/AzureWatch), que le permiten escalar una solución según programaciones, indicadores de rendimiento de la carga de servicio y el sistema, reglas personalizadas y combinaciones de diferentes tipos de reglas.

Al elegir qué solución de escalado automático adoptar, tenga en cuenta los siguientes puntos:

- Use las características de escalado automático integradas de la plataforma, si satisfacen sus requisitos. Si no es así, considere detenidamente si realmente necesita características de escalado más complejas. Entre algunos ejemplos de requisitos adicionales que van más allá de los que ofrece la capacidad de escalado automático integrada se puede incluir mayor granularidad de control, diferentes maneras de detectar eventos desencadenadores para el escalado, el escalado en varias suscripciones, el escalado de otros tipos de recursos, etc.
- Tenga en cuenta si puede predecir la carga en la aplicación con precisión suficiente como para depender solo en un escalado automático programado (adición y eliminación de instancias para satisfacer los picos de demanda anticipados). Cuando no sea posible, use el escalado automático reactivo basado en las métricas recopiladas en tiempo de ejecución para permitir que la aplicación controle los cambios imprevisibles en la demanda. No obstante, suele ser apropiado combinar estos métodos. Por ejemplo, cree una estrategia que agregue recursos, tales como proceso, almacenamiento y colas, según una programación de los momentos en que sabe que la aplicación está principalmente ocupada. Esto ayuda a garantizar que la capacidad esté disponible cuando sea necesario, sin provocar el retraso que se produce al iniciar nuevas instancias. Además, para cada regla programada, defina métricas que permitan el escalado automático reactivo durante ese período para asegurarse de que la aplicación pueda atender picos sostenidos pero imprevisibles en la demanda.
- A menudo, es difícil comprender la relación entre las métricas y los requisitos de capacidad, especialmente durante la implementación inicial de una aplicación. Es preferible aprovisionar un poco de capacidad adicional al principio y luego supervisar y optimizar las reglas de escalado automático para acercar la capacidad a la carga real.

### Uso del escalado automático de Azure
El escalado automático de Azure le permite configurar las opciones de escalar horizontalmente o reducir horizontalmente una solución. El escalado automático de Azure puede agregar y quitar automáticamente instancias de roles web y de trabajo de Servicios en la nube de Azure, Servicios móviles de Azure y aplicaciones de Sitios web de Azure. Además, puede permitir el escalado automático con el inicio o la detención de las instancias de máquinas virtuales de Azure. Una estrategia de escalado automático de Azure consta de dos conjuntos de factores:

- Un escalado automático basado en programaciones que puedan garantizar la disponibilidad de instancias adicionales para que coincidan con un pico de uso esperado y que puedan reducir horizontalmente una vez que haya transcurrido el tiempo pico. Esto le permite asegurar que dispone de instancias suficientes en ejecución sin tener que esperar a que el sistema reaccione ante la carga.
- Un escalado automático basado en métricas que reaccione a factores, como por ejemplo, la utilización media de la CPU durante la última hora o el trabajo pendiente de mensajes que está procesando la solución en una cola de Almacenamiento de Azure o Bus de servicio. Esto permite que la aplicación reaccione independientemente de las reglas de escalado automático programado para acomodar los cambios no planeados o imprevistos de la demanda.

Al usar el escalado automático de Azure, tenga en cuenta los siguientes puntos:

- La estrategia de escalado automático combina el escalado programado y basado en métricas. Puede especificar ambos tipos de reglas para un servicio, de modo que una aplicación se escale tanto según una programación y en respuesta a cambios en la carga.
- Debe configurar las reglas de escalado automático de Azure y luego supervisar el rendimiento de la aplicación con el tiempo. Use los resultados de esta supervisión para ajustar la manera en que se escala el sistema, si fuera necesario. No obstante, tenga en cuenta que el escalado automático no es un proceso instantáneo, se necesita tiempo para reaccionar ante una métrica como, por ejemplo, una utilización media de la CPU que supera un umbral especificado o es inferior a este.
- Las reglas de escalado automático que usan un mecanismo de detección basado en un atributo desencadenador medido (por ejemplo, el uso de la CPU o la longitud de cola) usan un valor agregado con el tiempo, en lugar de valores instantáneos, para desencadenar una acción de escalado automático. De forma predeterminada, el agregado es un promedio de los valores. Esto impide que el sistema reaccione demasiado rápido o que provoque una oscilación rápida. También permite tiempo para que las nuevas instancias de inicio automático entren en modo de ejecución, lo que impide que se produzcan acciones adicionales de escalado automático mientras se inician las nuevas instancias. Para los Servicios en la nube y Máquinas virtuales, el período predeterminado para la agregación es de 45 minutos, por lo que la métrica puede tardar hasta a este período de tiempo para desencadenar el escalado automático en respuesta a picos de demanda. Puede cambiar el período de agregación mediante el SDK. Sin embargo, tenga en cuenta que los períodos de menos de 25 minutos pueden producir resultados imprevisibles (consulte el artículo sobre el [escalado automático de los Servicios en la nube según el porcentaje de CPU con Azure Monitoring Services Management Library](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/) para más información). En el caso de Sitios web de Azure, el período medio es mucho más corto, lo que permite que las nuevas instancias estén disponibles en unos cinco minutos después de que se produzca un cambio en la medida de desencadenador media.
- Si configura el escalado automático mediante el SDK en lugar del portal web, puede especificar una programación más detallada durante la que las reglas están activas. También puede crear sus propias métricas y usarlas con o sin las métricas existentes en sus reglas de escalado automático. Por ejemplo, quizás desee usar contadores alternativos, como por ejemplo, el número de solicitudes por segundo o la disponibilidad media de memoria, o bien contadores que midan procesos empresariales específicos.
- Al realizar el escalado automático de Máquinas virtuales de Azure, debe implementar un número de instancias de máquina virtual que sea igual al número máximo que permitirá que inicie el escalado automático. Estas instancias deben formar parte del mismo conjunto de disponibilidad. El mecanismo de escalado automático de Máquinas virtuales no crea ni elimina las instancias de la máquina virtual. En cambio, las reglas de escalado automático que configure inician y detienen un número adecuado de estas instancias. Para más información, consulte [Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales](cloud-services-how-to-scale.md).
- Si no se puede iniciar nuevas instancias, quizás porque se alcanzó el número máximo para una suscripción (por ejemplo, el número máximo de núcleos al usar el servicio Máquinas virtuales) o se produce un error durante el inicio, el portal puede mostrar que una operación de escalado automático se ha realizó correctamente. No obstante, los eventos **ChangeDeploymentConfiguration** posteriores que se muestran en el portal mostrarán únicamente que se solicitó un inicio de servicio y no habrá ningún evento para indicar se completó correctamente.
- En el escalado automático de Azure, puede usar la interfaz de usuario del portal web para vincular recursos, tales como instancias de base de datos SQL y colas a una instancia de servicio de proceso. Esto permite acceder más fácilmente las opciones separadas de configuración de escalado manual y automático para cada uno de los recursos vinculados. Para más información, consulte [Vinculación de un recurso a un servicio en la nube](cloud-services-how-to-manage.md#linkresources) en la página Administración de servicios en la nube y la página [Escalado de una aplicación](cloud-services-how-to-scale.md).
- Al configurar varias directivas y reglas, existe la posibilidad de que podrían producirse conflictos entre ellas. El escalado automático de Azure usa las siguientes reglas de resolución de conflictos para garantizar que siempre hay un número suficiente de instancias en ejecución:
  - Las operaciones de escalar horizontalmente siempre tienen prioridad sobre las operaciones de reducir horizontalmente.
  - Cuando se produce un conflicto en las operaciones de escalar horizontalmente, tiene prioridad la regla que inicia el mayor aumento en el número de instancias.
  - Cuando se produce un conflicto en las operaciones de reducir horizontalmente, tiene prioridad la regla que inicia la menor reducción en el número de instancias.

<a name="the-azure-monitoring-services-management-library"></a>


## Orientación y patrones relacionados
Los siguientes patrones y orientación también pueden ser pertinentes para su escenario al implementar el escalado automático:

- [Patrón de limitación](http://msdn.microsoft.com/library/dn589798.aspx). En este patrón se describe cómo una aplicación puede seguir funcionando y cumplir los contratos de nivel de servicio cuando un aumento en la demanda aplica una carga muy elevada en los recursos. La limitación se puede usar con la escalado automático para impedir que un sistema se vea superado durante la operación de escalar horizontalmente.
- [Patrón de consumidores de la competencia](http://msdn.microsoft.com/library/dn568101.aspx). En este patrón se describe cómo implementar un grupo de instancias de servicio que puede controlar mensajes de cualquier instancia de la aplicación. El escalado automático se puede usar para iniciar y detener las instancias de servicio para que coincidan con la carga de trabajo anticipada. Este método permite que un sistema procese varios mensajes simultáneamente a fin de optimizar el rendimiento, mejorar la escalabilidad y disponibilidad, y equilibrar la carga de trabajo.
- [Orientación sobre instrumentación y telemetría](http://msdn.microsoft.com/library/dn589775.aspx). La instrumentación y telemetría son vitales para la recopilación de información que puede impulsar el proceso de escalado automático.

## Más información
- [Escalado de una aplicación](cloud-services-how-to-scale.md)
- [Escalado automático de una aplicación que ejecuta roles web, roles de trabajo o máquinas virtuales](cloud-services-how-to-manage.md#linkresources)
- [Vinculación de un recurso a un servicio en la nube](cloud-services-how-to-manage.md#linkresources)
- [Escalado de recursos vinculados](cloud-services-how-to-scale.md#scalelink)
- [Azure Monitoring Services Management Library](http://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring)
- [API de REST de administración de servicios de Azure](http://msdn.microsoft.com/library/azure/ee460799.aspx)
- [API de REST del Administrador de recursos de Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx)
- [Biblioteca de Microsoft Insights](https://www.nuget.org/packages/Microsoft.Azure.Insights/)
- [Operaciones en escalado automático](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [Espacio de nombres Microsoft.WindowsAzure.Management.Monitoring.Autoscale](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.management.monitoring.autoscale.aspx)

<!---HONumber=AcomDC_0107_2016-->