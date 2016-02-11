<properties
   pageTitle="Presentación del servicio Contenedor de Azure | Microsoft Azure"
   description="El servicio Contenedor de Azure (ACS) ofrece una forma de simplificar la creación, configuración y administración de un clúster de máquinas virtuales preconfiguradas para ejecutar aplicaciones en contenedor."
   services="virtual-machines"
   documentationCenter=""
   authors="rgardler"
   manager="nepeters"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenedores, microservicios, Mesos, Azure"/>
   
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="home-page"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="rogardle"/>

# Presentación del servicio Contenedor de Azure

El servicio Contenedor de Azure (ACS) ofrece una forma de simplificar la creación, configuración y administración de un clúster de máquinas virtuales preconfiguradas para ejecutar aplicaciones en contenedor. Gracias a una configuración optimizada de herramientas de programación y orquestación de código abierto conocidas, ACS le permite usar sus conocimientos o recurrir a un importante y creciente grupo de expertos comunitarios para implementar y administrar aplicaciones basadas en contenedores en Microsoft Azure.

<br /> ![ACS proporciona un medio para administrar aplicaciones en contenedor en varios hosts de Azure.](./media/acs-intro/acs-cluster.png) <br /><br />

ACS usa Docker para garantizar que los contenedores de su aplicación sean completamente portátiles. También es compatible con su elección de Marathon, Chronos y Apache Mesos o Docker Swarm para garantizar que estas aplicaciones se puedan escalar a miles e incluso a decenas de miles de contenedores.

El servicio Contenedor de Azure permite aprovechar las características empresariales de Azure sin dejar de mantener la portabilidad de aplicaciones, incluso en las capas de orquestación.

Mientras el servicio se encuentra en versión de vista previa, pedimos a todos los interesados en probarlo que se [propongan a sí mismos](http://aka.ms/acspreview). Una vez que se ha proporcionado acceso a la versión de vista previa, se enviará un correo electrónico con información adicional como plantillas de implementación e instrucciones de introducción. Para usar el servicio, necesitará una suscripción de Azure. Si aún no la tiene, ¿por qué no se suscribe a una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)?

Uso del servicio Contenedor de Azure
-----------------------------

Nuestro objetivo con el servicio Contenedor de Azure es proporcionar un entorno de hospedaje de contenedores mediante el uso de tecnologías y herramientas de código abierto, conocidas por nuestros clientes. Con este fin, se exponen los puntos de conexión de API estándar para Docker y su Orchestrator elegido. Al usar estos puntos de conexión, puede aprovechar cualquier software que se comunique con los mismos. Por ejemplo, en el caso del punto de conexión Docker Swarm, puede optar por usar Docker Compose, mientras que para Apache Mesos, puede decidirse a utilizar CLI de DCOS.

Creación de un clúster de Docker con el servicio Contenedor de Azure
-------------------------------------------------------

Una vez que ha [solicitado](http://aka.ms/acspreview) y se le ha concedido acceso a la versión de vista previa, puede usar una de las plantillas del Administrador de recursos de Azure que le permiten implementar su primer clúster a través del Portal de Azure. Gracias a estas plantillas, podrá crear un servicio de forma rápida y empezar a implementar aplicaciones en él inmediatamente. Para empezar, solo tiene que decidir el tamaño del clúster y si desea usar Docker Swarm o Apache Mesos para administrar sus aplicaciones.

También puede [usar la línea de comandos](/documentation/articles/resource-group-template-deploy/) para crear servicios Contenedor de Azure con estas mismas plantillas. Cuando se haya familiarizado con la estructura de estas plantillas, podrá escribir las suyas propias y automatizar completamente la creación de un clúster del servicio Contenedor de Azure.

Los usuarios de la versión de vista previa recibirán documentación y soporte completos, que se publicarán aquí una vez abierto el servicio para el público.

Implementación de una aplicación
------------------------

Durante la vista previa, se ofrece la posibilidad de elegir entre Docker Swarm o Apache Mesos (con los marcos de DCOS Marathon y DCOS Chronos) para la orquestación.

### Uso de Apache Mesos

Apache Mesos es un proyecto de código abierto que se hospeda en Apache Software Foundation. Incluye algunos de los [principales nombres de TI](http://mesos.apache.org/documentation/latest/powered-by-mesos/) como usuarios y colaboradores.

![ACS configurado para Swarm, que muestra agentes y patrones.](media/acs-intro/acs-mesos.png)

Mesos contiene un conjunto de características increíble.

-   Escalabilidad a 10.000 nodos

-   Patrón y esclavos replicados tolerantes a errores que usan ZooKeeper

-   Soporte para contenedores de Docker

-   Aislamiento nativo entre las tareas con contenedores de Linux

-   Programación de varios recursos (memoria, CPU, disco y puertos)

-   API de Java, Python y C++ para desarrollar nuevas aplicaciones paralelas

-   IU web para ver el estado del clúster

Mesos admite un gran número de [marcos](http://mesos.apache.org/documentation/latest/frameworks/) que pueden usarse para programar cargas de trabajo en el servicio Contenedor de Azure. De forma predeterminada, ACS incluye los marcos de Marathon y Chronos.

#### Uso de Marathon y Chronos

Marathon es un sistema de inicialización y control de todo el clúster para servicios en cgroups o, en el caso de ACS, contenedores de Docker. Es un asociado ideal para Chronos, un programador de trabajos tolerante a errores para Mesos que controla dependencias y programaciones basadas en el tiempo.

Marathon y Chronos proporcionan una IU web desde la que puede implementar sus aplicaciones. Tendrá acceso a esta desde una dirección URL semejante a `http://DNS\_PREFIX.REGION.cloudapp.azure.com`, donde DNS\_PREFIX y REGION se definen en el momento de la implementación. Por supuesto, también puede proporcionar su propio nombre DNS.

Además, puede usar las API de REST para comunicarse con Marathon y Chronos. Hay varias bibliotecas de cliente disponibles para cada herramienta, que abarcan distintos lenguajes y, por supuesto, puede usar el protocolo HTTP en cualquier lenguaje. Muchas herramientas de DevOps conocidas también proporcionan soporte para estos programadores. De este modo se proporciona la máxima flexibilidad a su equipo de operaciones al trabajar con un clúster de ACS.

Los usuarios de la versión de vista previa recibirán documentación y soporte completos, que se publicarán aquí una vez abierto el servicio para el público.

### Uso de Docker Swarm

Docker Swarm proporciona agrupación en clústeres nativa para Docker. Como Docker Swarm proporciona servicio a la API de Docker estándar, cualquier herramienta que ya se comunique con Docker daemon podrá usar Swarm para escalar de forma transparente a varios hosts en el servicio Contenedor de Azure.

![ACS configurado para usar Apache Mesos, que muestra JumpBox, agentes y patrones.](media/acs-intro/acs-swarm.png)

Entre las herramientas compatibles para administrar contenedores en un clúster Swarm se incluyen las siguientes:

-   Dokku

-   CLI de Docker y Docker Compose

-   Krane

-   Jenkins

Los usuarios de la versión de vista previa recibirán documentación y soporte completos, que se publicarán aquí una vez abierto el servicio para el público.

Obtención de acceso
--------------

Mientras el servicio se encuentra en versión de vista previa, pedimos a todos los interesados en probarlo que se [propongan a sí mismos](http://aka.ms/acspreview). En primer lugar, necesitará una suscripción de Azure. Si aún no la tiene, ¿por qué no se suscribe a una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/)?

Vídeos
------
Anuncio de AzureCon:

> [AZURE.VIDEO azurecon-2015-deep-dive-on-the-azure-container-service-with-mesos]  

Introducción a ACS:

> [AZURE.VIDEO connect-2015-getting-started-developing-with-docker-and-azure-container-service]


<!---HONumber=AcomDC_0128_2016-->
<!---Edite la linea 118-->