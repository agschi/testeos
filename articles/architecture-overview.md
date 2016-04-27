<properties 
	pageTitle="Arquitectura de aplicaciones en Microsoft Azure" 
	description="Información general de arquitectura que cubre patrones de diseño habituales." 
	services="" 
	documentationCenter="" 
	authors="Rboucher" 
	manager="jwhit" 
	editor="mattshel"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015" 
	ms.author="robb"/>

#Arquitectura de aplicaciones en Microsoft Azure
Recursos para la creación de aplicaciones que usan Microsoft Azure. Esto incluye herramientas para ayudarle a dibujar diagramas para describir visualmente sistemas de software.



##Patrones de diseño de arquitectura de Azure
Microsoft publica la serie de patrones de diseño de arquitectura para ayudarle a crear sus propios diseños personalizados. Los patrones están diseñados como guías de arquitectura concisas que pueden combinarse para proporcionar orientación sobre cómo aprovechar mejor la plataforma Microsoft Azure para resolver las necesidades de su organización.


[Información general](../azure-architectures-cpif-overview/)-[Redes híbridas](../azure-architectures-cpif-infrastructure-hybrid-networking/)-[Procesamiento por lotes fuera del sitio](../azure-architectures-cpif-foundation-offsite-batch-processing-tier/)-[Capa de datos multisitio](../azure-architectures-cpif-foundation-multi-site-data-tier/)-[Capa web con equilibrio de carga global](../azure-architectures-cpif-foundation-global-load-balanced-web-tier/)-[Capa de búsqueda de Azure](../azure-architectures-cpif-foundation-azure-search-tier/)
 
Cada patrón contiene
 
- Una descripción del servicio
- Una lista de los servicios de Azure necesarios para aprovechar el patrón
- Diagramas de arquitectura
- Dependencias de arquitectura
- Limitaciones o consideraciones del diseño que pueden influir en el patrón
- Interfaces y extremos
- Antipatrones
- Consideraciones sobre la arquitectura de alto nivel de clave, como disponibilidad y resistencia, SLA compuestos para servicios usados, escala y rendimiento y consideraciones operativas y sobre costos.

![Patrones de diseño de arquitectura de Azure](./media/architecture-overview/AzureArchPatterns.jpg)


##Póster de patrones de diseño
Microsoft Patterns and Practices ha publicado el libro [Cloud Design Patterns](http://msdn.microsoft.com/library/dn568099.aspx) que está disponible en MSDN y como descarga PDF. También hay disponible un póster de gran formato en el que se enumeran todos los patrones.

![Póster de patrones de nube de Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)



##Curso de certificación de arquitectura de Microsoft

Microsoft acaba de lanzar un nuevo curso de arquitectura compatible con el examen de certificación de Microsoft 70-534. Está [disponible gratis en EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x). Usa la nueva [plantilla de Visio de proyectos 3D](#3d-blueprint-visio-template).

![Curso de certificación de arquitectura de Microsoft](./media/architecture-overview/EDXCourse.png)


##Proyectos de arquitectura de Microsoft

Microsoft publica un conjunto de [proyectos de arquitectura](http://aka.ms/azblueprints) de alto nivel que muestran cómo crear tipos específicos de sistemas con productos de Microsoft.

Cada proyecto incluye:

- Un archivo plano **2D basado en Visio** 2003 que puede descargar y modificar 
- Un archivo **PDF en perspectiva 3D** para presentar el proyecto a un público menos técnico
- Un **vídeo** que le guía a través de la versión 3D. 

Los proyectos usan el [conjunto de símbolos de nube y empresariales](#symbol-and-icon-sets).

![Diagrama 3D de Proyectos de arquitectura de Microsoft](./media/architecture-overview/BluePrintThumb.jpg)



##Plantilla de Visio de proyectos 3D

Las versiones 3D de los [Proyectos de arquitectura de Microsoft](http://aka.ms/azblueprints) se crearon inicialmente en una herramienta de terceros. Una nueva plantilla de Visio 2013 (y versiones posteriores) incluida el 5 de agosto de 2015 como parte de un [Curso de certificación de arquitectura de Microsoft distribuido en EDX.ORG](#microsoft-architecture-certification-course).

La plantilla también está disponible fuera del curso.

- [Ver el vídeo de entrenamiento](http://aka.ms/3dBlueprintTemplateVideo) primero para saber lo que puede hacer   
- Descargar la [plantilla de Visio de proyectos 3D de Microsoft](http://aka.ms/3DBlueprintTemplate)
- Descargar los [símbolos de nube y empresariales](#drawing-symbol-and-icon-sets) para utilizar con la plantilla 3D. 

Envíenos un correo electrónico a [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) si tiene preguntas específicas no respondidas en los materiales de entrenamiento o para proporcionarnos sus comentarios. La facilidad de uso es uno de los objetivos principales de la plantilla; háganos saber lo que está bien y qué le causa problemas.

![Plantilla de Visio de proyectos 3D de Microsoft](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)



##Conjuntos de símbolos e iconos de dibujo 

[Vea el vídeo de entrenamiento de Visio y de los símbolos](http://aka.ms/CnESymbolsVideo) y [descargue el conjunto de símbolos de nube y empresariales](http://aka.ms/CnESymbols) para ayudarle a crear materiales técnicos que describen Azure, Windows Server, SQL Server, etc. Puede utilizar los símbolos en diagramas de arquitectura, materiales de entrenamiento, presentaciones, hojas de datos, infografía, notas del producto e incluso en libros de terceros si estos entrenan a personas para usar los productos de Microsoft. Sin embargo, no están pensados para su uso en interfaces de usuario.

Los símbolos de CnE están en los formatos de Visio y PNG. En el conjunto se incluyen instrucciones sobre cómo usar PNG en PowerPoint.

El conjunto de símbolos se envía cada trimestre y se actualiza a medida que se publican nuevos servicios.

Están disponibles símbolos adicionales para Microsoft Office y tecnologías relacionadas en la [Galería de símbolos de Microsoft Office Visio](http://www.microsoft.com/es-ES/download/details.aspx?id=35772), aunque no están optimizados para diagramas de arquitectura como el conjunto de CnE.

**Comentarios:** si ha usado los símbolos de CnE, rellene la breve [encuesta](http://aka.ms/azuresymbolssurveyv2) de 5 preguntas o envíenos un correo electrónico a [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) si tiene preguntas y problemas específicos. Nos gustaría conocer su opinión, incluidos comentarios constructivos para seguir invirtiendo tiempo en ellos.

![Conjunto de iconos y símbolos empresariales y de nube](./media/architecture-overview/CnESymbols.png)


##Infografías de arquitectura

Microsoft publica varios pósteres e infografías relacionados con la arquitectura. Entre ellos, se encuentran [Creación de aplicación en la nube para el mundo real](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) y [Escalación de aplicaciones con Servicios en la nube](https://azure.microsoft.com/documentation/infographics/cloud-services/).

![Infografías de arquitectura de Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

<!---HONumber=AcomDC_0128_2016-->
<!---Otro comentario-->