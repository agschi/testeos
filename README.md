# Guía del colaborador de documentación técnica de Azure

Ha encontrado el repositorio de GitHub, que contiene los archivos de origen de la documentación técnica que se publica en el Centro de documentación de Azure en [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Este repositorio también contiene una guía para ayudarle a colaborar con nuestra documentación técnica. Para ver una lista de los artículos incluidos en la guía del colaborador, consulte [Azure technical content contributors' guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## Colaboración en la documentación de Azure

Gracias por su interés en la documentación de Azure.

* [Formas de colaborar](#ways-to-contribute)
* [Información acerca de sus colaboraciones al contenido de Azure](#about-your-contributions-to-azure-content)
* [Organización del repositorio](#repository-organization)
* [Uso de GitHub, Git y este repositorio](#use-github-git-and-this-repository)
* [Uso de Markdown para dar formato al tema](#how-to-use-markdown-to-format-your-topic)
* [Más recursos](#Más recursos)
* [Azure technical content contributors' guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md) (abre una nueva página)

## Formas de colaborar

Puede colaborar fácilmente en artículos técnicos en la interfaz de usuario de GitHub. Busque el artículo en este repositorio, o bien consulte el artículo en [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation), y haga clic en el vínculo en el artículo que lleva al archivo de origen de GitHub para el artículo.

##Información acerca de sus colaboraciones al contenido de Azure

###Correcciones menores

Las correcciones menores o aclaraciones que se envían para la documentación y los ejemplos de código de este repositorio están cubiertos por los [Términos de Uso del Sitio Web de Microsoft Azure](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###Envíos mayores

Si se envía una solicitud de incorporación de cambios con cambios importantes o nuevos para la documentación y los ejemplos de código, pondremos un comentario en GitHub en el que le solicitaremos que envíe un Contrato de licencia de colaboración (CLC) en línea si se encuentra en uno de estos grupos:

* Miembros del grupo Microsoft Open Technologies.
* Colaboradores que no trabajan para Microsoft.

Necesitamos que rellene el formulario en línea antes de poder aceptar la solicitud de incorporación de cambios.

Puede encontrar información detallada en [http://azure.github.io/guidelines.html#cla](http://azure.github.io/guidelines.html#cla).

## Organización del repositorio

El contenido del repositorio azure-content sigue la organización de documentación de [Azure.Microsoft.com](http://azure.microsoft.com). Este repositorio contiene dos carpetas raíz:

### \articles

La carpeta *\articles* contiene los artículos de la documentación con formato de archivos de Markdown con la extensión *.md*.

Los artículos del directorio raíz se publican en Azure.Microsoft.com, en la ruta de acceso *http://azure.microsoft.com/documentation/articles/{article-name-without-md}/*.

* **Nombres de archivo y artículos:** Consulte [nuestra guía de nomenclatura de archivos](./contributor-guide/file-names-and-locations.md).

Los artículos con su propia carpeta de servicio se publican en Azure.Microsoft.com, en la ruta de acceso
*http://azure.microsoft.com/documentation/articles/service-folder/{article-name-without-md}/*

* **Subcarpetas multimedia**: la carpeta *\articles* contiene la carpeta *\media* para los archivos multimedia de los artículos del directorio raíz; dentro de ella se encuentran subcarpetas con las imágenes de cada artículo. Las carpetas de servicio contienen una carpeta multimedia independiente para los artículos que se encuentran en cada carpeta de servicio. Las carpetas de imágenes de los artículos tienen el mismo nombre que el archivo del artículo, sin la extensión de archivo *.md*.

### \includes

Puede crear secciones de contenido reutilizable que se incluirán en uno o varios artículos. Consulte [Custom extensions used in our technical content](https://github.com/Azure/azure-content/blob/master/custom-markdown-extensions.md).

### \markdown templates

Esta carpeta contiene nuestra plantilla de Markdown estándar con el formato de Markdown básico que necesita para un artículo.

### \contributor-guide

Esta carpeta contiene artículos que forman parte de nuestra guía del colaborador.

## Uso de GitHub, Git y este repositorio

Para obtener información acerca de cómo colaborar, cómo usar la IU de GitHub para colaborar con pequeños cambios, y cómo bifurcar y clonar el repositorio para colaboraciones más importantes, consulte [Install and set up tools for authoring in GitHub](https://github.com/Azure/azure-content/blob/master/contributor-guide/tools-and-setup.md).

Si instala GitBash y elige trabajar localmente, los pasos para crear una nueva bifurcación de trabajo local, realizar cambios y devolver los cambios a la bifurcación principal se indican en [Git commands for creating a new article or updating an existing article](https://github.com/Azure/azure-content/blob/master/contributor-guide/git-commands-for-master.md).

### Bifurcaciones

Recomendamos crear bifurcaciones de trabajo locales dirigidas a un ámbito de cambio específico. Cada bifurcación debe limitarse a un único concepto/artículo para simplificar el flujo de trabajo y reducir la posibilidad de conflictos de combinación. Los siguientes elementos tienen el ámbito adecuado para una nueva bifurcación:

* Un nuevo artículo (y las imágenes asociadas)
* Modificaciones ortográficas y gramaticales de un artículo.
* Aplicación de un solo cambio de formato en un amplio conjunto de artículos (por ejemplo, un nuevo pie de página con el copyright).

## Uso de Markdown para dar formato al tema

Todos los artículos de este repositorio utilizan Markdown adaptado a GitHub. A continuación se proporciona una lista de recursos.

- [Markdown basics](https://help.github.com/articles/markdown-basics/)

- [Markdown cheat sheet para impresión](https://github.com/Azure/azure-content/blob/master/contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Para ver nuestra lista de editores de Markdown, consulte el tema [Install and set up tools for authoring in GitHub](https://github.com/Azure/azure-content/blob/master/contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## Metadatos de los artículos

Los metadatos del artículo habilitan ciertas funcionalidades en el sitio web azure.microsoft.com, como atribución del autor, atribución del colaborador, rutas de navegación, descripciones del artículo y optimizaciones SEO, así como informes que Microsoft utiliza para evaluar el rendimiento del contenido. Por lo tanto ¡los metadatos son importantes! [Consulte esta guía para asegurarse de que sus metadatos son correctos](https://github.com/Azure/azure-content/blob/master//contributor-guide/article-metadata.md).

## Más recursos

Consulte [Azure technical content contributors' guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md) para ver todos nuestros temas de guía.

<!---HONumber=AcomDC_0307_2016-->