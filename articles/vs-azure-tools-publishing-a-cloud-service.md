<properties
   pageTitle="Publicar un servicio en la nube mediante Azure Tools | Microsoft Azure"
   description="Obtenga información sobre cómo publicar proyectos de servicios en la nube de Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Publicar un servicio en la nube mediante Azure Tools

Con Azure Tools para Microsoft Visual Studio, puede publicar su aplicación de Azure directamente desde Visual Studio. Visual Studio admite la publicación integrada en los entornos de ensayo o de producción de un servicio en la nube.

Para poder publicar una aplicación de Azure, debe tener una suscripción de Azure. También debe configurar un servicio en la nube y una cuenta de almacenamiento para que la use la aplicación. Puede establecerlas en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Al publicar, puede seleccionar el entorno de implementación de su servicio en la nube. También debe seleccionar una cuenta de almacenamiento que se usará para almacenar el paquete de aplicación para la implementación. Después de la implementación, el paquete de aplicación se quita de la cuenta de almacenamiento.

Cuando desarrolla y prueba una aplicación de Azure, puede usar Web Deploy para publicar los cambios de sus roles web incrementalmente. Después de publicar su aplicación en un entorno de implementación, Web Deploy le permite implementar los cambios directamente en la máquina virtual que está ejecutando el rol web. No tiene que empaquetar y publicar toda la aplicación de Azure cada vez que desee actualizar su rol web para probar los cambios. Con este enfoque puede tener los cambios del rol web disponibles en la nube para pruebas sin tener que esperar a publicar la aplicación en un entorno de implementación.

Use los siguientes procedimientos para publicar su aplicación de Azure y para actualizar un rol web mediante el uso Web Deploy:

- Publicar o empaquetar una aplicación de Azure desde Visual Studio

- Actualizar un rol web como parte del ciclo de desarrollo y pruebas

## Publicar o empaquetar una aplicación de Azure desde Visual Studio

Cuando publica su aplicación de Azure, puede realizar una de las siguientes tareas:

- Crear un paquete de servicios: puede usar este paquete y el archivo de configuración del servicio para publicar su aplicación en un entorno de implementación desde el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publicar el proyecto de Azure de Visual Studio: para publicar la aplicación directamente en Azure, use el Asistente para publicación. Para obtener información, consulte [Asistente Publicar aplicación de Azure](vs-azure-tools-publish-azure-application-wizard.md)

### Para crear un paquete de servicios de Visual Studio

1. Cuando esté preparado para publicar su aplicación, abra el Explorador de soluciones, abra menú contextual del proyecto de Azure que contiene sus roles y elija Publicar.

1. Para crear solo un paquete de servicios, siga estos pasos:

  1. En el menú contextual del proyecto de Azure, elija **Empaquetar**.

  1. En el cuadro de diálogo **Empaquetar aplicación de Azure**, elija la configuración del servicio para el que desea crear un paquete y, después, elija la configuración de compilación.

  1. (opcional) Para activar el Escritorio remoto para el servicio en la nube después de haberlo publicado, active la casilla **Habilitar Escritorio remoto para todos los roles** y luego haga clic en **Configuración** para configurar el Escritorio remoto. Si quiere depurar el servicio en la nube después de publicarlo, seleccione **Habilitar Depurador remoto para todos los roles** para activar la depuración remota.

      Para obtener más información, consulte [Usar Escritorio remoto con los roles de Azure](vs-azure-tools-using-remote-desktop-with-azure-roles.md).

  1. Para crear el paquete, elija el vínculo **Paquete**.

      El Explorador de archivos muestra la ubicación del archivo del paquete recién creado. Copie esta ubicación para que se pueda usar desde el Portal de administración de Azure.

  1. Para publicar este paquete en un entorno de implementación, debe usar esta ubicación como la Ubicación del paquete cuando cree un servicio en la nube e implemente este paquete a un entorno con el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Para cancelar el proceso de implementación, en el menú contextual del elemento de línea del registro de actividad seleccione **Cancelar y quitar**. Esto detiene el proceso de implementación y elimina el entorno de implementación de Azure.

    >[AZURE.NOTE] Para quitar este entorno de implementación una vez implementado, debe usar el Portal de administración de Azure.

1. (Opcional) Después de iniciarse las instancias del rol, Visual Studio muestra automáticamente el entorno de implementación en el nodo **Servicios en la nube** en el Explorador de servidores. Desde aquí puede ver el estado de las instancias de rol individuales. Consulte [Administración de recursos de Azure con Cloud Explorer](vs-azure-tools-resources-managing-with-cloud-explorer.md). La siguiente ilustración muestra las instancias del rol mientras todavía están en el estado Inicializando:

    ![VST\_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## Actualizar un rol web como parte del ciclo de desarrollo y pruebas

Si la infraestructura back-end de su aplicación es estable, pero los roles web necesitan actualizaciones más frecuentes, puede usar Web Deploy para actualizar solo un rol web en su proyecto. Esto resulta práctico cuando no quiere volver a generar y a implementar los roles de trabajo del back-end, o si tiene varios roles web y quiere actualizar solo uno de ellos.

### Requisitos

Estos son los requisitos para usar Web Deploy para actualizar su rol web:

- **Solo con fines de desarrollo y prueba**: los cambios se realizan directamente en la máquina virtual en la que el rol web se está ejecutando. Si esta máquina virtual tiene que ser reciclada, se pierden los cambios porque el paquete original que publicó se usa para volver a crear la máquina virtual del rol. Debe volver a publicar la aplicación para obtener los últimos cambios del rol web.

- **Solo se pueden actualizar los roles web**: los roles de trabajo no se pueden actualizar. Además, no puede actualizar RoleEntryPoint en web role.cs.

- **Solo puede admitir una instancia de un rol web**: no puede tener varias instancias de ningún rol web en su entorno de implementación. Sin embargo, se admiten varios roles web cada uno con solo una instancia.

- **Debe habilitar conexiones de escritorio remoto**: esto es necesario para que Web Deploy pueda utilizar el usuario y la contraseña para conectar con la máquina virtual e implementar los cambios en el servidor que ejecuta Internet Information Services (IIS). Además, podría necesitar conectar a la máquina virtual para agregar un certificado de confianza a IIS en esta máquina virtual. (Esto asegura que la conexión remota de IIS que usa Web Deploy sea segura).

En el siguiente procedimiento se supone que usa el asistente **Publicar aplicación de Azure**.

### Para habilitar Web Deploy al publicar su aplicación

1. Para habilitar la casilla **Habilitar Web Deploy** para todos los roles web, primero debe configurar las conexiones de escritorio remoto. Seleccione **Habilitar Escritorio remoto** para todos los roles y luego escriba en el cuadro **Configuración del Escritorio remoto** que aparece las credenciales que se usarán para conectarse de forma remota. Consulte [Usar Escritorio remoto con los roles de Azure](vs-azure-tools-remote-desktop-roles.md) para obtener más información.

1. Para habilitar Web Deploy para todos los roles web de su aplicación, seleccione **Habilitar Web Deploy para todos los roles web**.

    Se muestra un triángulo de advertencia amarillo. Web Deploy usa de forma predeterminada un certificado autofirmado no de confianza, que no se recomienda para cargar datos confidenciales. Si necesita proteger este proceso por que hay datos confidenciales, puede agregar un certificado SSL para que sea usa para las conexiones de Web Deploy. Este certificado debe ser un certificado de confianza. Para obtener información sobre cómo hacerlo, consulte la sección **Proteger Web Deploy** más adelante sobre este tema.

1. Elija **Siguiente** para ver la pantalla **Resumen** y luego seleccione **Publicar** para implementar el servicio en la nube.

    El servicio en la nube se publicará. La máquina virtual que se crea tiene conexiones remotas habilitadas para IIS para que Web Deploy se pueda usar a fin de actualizar sus roles web sin volver a publicarlos.

    >[AZURE.NOTE] Si tiene configurada más de una instancia para un rol web, aparece un mensaje de advertencia que indica que cada rol web solo se limitará a una instancia en el paquete que se crea para publicar su aplicación. Haga clic en Aceptar para continuar. Como se indica en la sección Requisitos, puede tener más de un rol web, pero solo una instancia de cada rol.

### Para actualizar su rol web usando Web Deploy

1. Para usar Web Deploy, debe realizar cambios en el código del proyecto para cualquiera de sus roles web en Visual Studio que quiera publicar, y luego haga clic con el botón derecho en el nodo de este proyecto en su solución y apunte a **Publicar**. Aparece el cuadro de diálogo **Publicación web**.

1. (Opcional) Si agregó un certificado SSL de confianza para usarlo para las conexiones remotas de IIS, puede desactivar la casilla **Permitir certificado que no es de confianza**. Para obtener información sobre cómo agregar un certificado para proteger Web Deploy, consulte la sección **Proteger Web Deploy** más adelante sobre este tema.

1. Para usar Web Deploy, el mecanismo de publicación necesita el nombre de usuario y la contraseña que configuró para su conexión de escritorio remoto cuando publicó el paquete por primera vez.

  1. En **Nombre de usuario** escriba el nombre de usuario.

  1. En **Contraseña **escriba la contraseña.

  1. (Opcional) Si desea guardar esta contraseña en este perfil, seleccione **Guardar contraseña**.

1. Para publicar los cambios de su rol web, elija **Publicar**.

    La línea de estado muestra **Publicación iniciada**. Cuando la publicación finaliza, se muestra **Publicación correcta**. Ahora los cambios se implementaron en el rol web en su máquina virtual. Ahora puede iniciar su aplicación de Azure para probar sus cambios en el entorno de Azure.

### Para proteger Web Deploy

1. Web Deploy usa de forma predeterminada un certificado autofirmado no de confianza, que no se recomienda para cargar datos confidenciales. Si necesita proteger este proceso por que hay datos confidenciales, puede agregar un certificado SSL para que sea usa para las conexiones de Web Deploy. Este certificado debe ser un certificado de confianza, que haya obtenido de una entidad de certificación (CA).

    Para proteger Web Deploy para cada máquina virtual de cada uno de sus roles web, debe cargar el certificado de confianza que desea usar para Web Deploy en el [Portal de administración de Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Esto garantiza que el certificado se agrega a la máquina virtual que se crea para el rol web al publicar su aplicación.

1. Para agregar un certificado SSL de confianza a IIS para usarlo para las conexiones remotas, siga estos pasos:

  1. Para conectarse a la máquina virtual que está ejecutando el rol web, haga clic en la instancia del rol web en el **Cloud Explorer** o **Explorador de servidores** y después elija **Conectar utilizando Escritorio remoto**. Para obtener pasos detallados sobre cómo conectar a la máquina virtual, consulte [Usar Escritorio remoto con los roles de Azure](vs-azure-tools-remote-desktop-roles.md).

      El explorador le solicitará que descargue un archivo .RDP.

  1. Para agregar un certificado SSL, abra el servicio de administración en el Administrador de IIS. En el Administrador de IIS, habilite SSL abriendo el vínculo **Enlaces** en el panel **Acción**. Aparecerá el cuadro de diálogo **Agregar enlace de sitio**. Elija **Agregar** y, después, seleccione HTTPS en la lista desplegable **Tipo**. En la lista de **certificados SSL**, elija el certificado SSL que obtuvo firmado por una entidad de certificación y que cargó en el Portal de administración de Azure. Para obtener más información, consulte [Configurar los valores de conexión para el servicio de administración](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Si agrega un certificado SSL de confianza, el triángulo de advertencia amarillo ya no aparece en el **Asistente para publicación**.

## Incluir archivos en el paquete de servicio

Puede que deba incluir archivos específicos en su paquete de servicio para que estén disponibles en la máquina virtual que se crea para un rol. Por ejemplo, puede querer agregar un archivo .exe o .msi que sea usado por un script de inicio en su paquete del servicio. O es posible que quiera agregar un ensamblado que requiera un proyecto de rol web o de rol de trabajo. Para incluir archivos, debe agregarlos a la solución de su aplicación de Azure.

### Para incluir archivos en el paquete del servicio

1. Para agregar un ensamblado a un paquete del servicio, siga estos pasos:

  1. En el **Explorador de soluciones** abra el nodo del proyecto al que le falta el ensamblado de referencia.

  1. Para agregar el ensamblado al proyecto, abra el menú contextual de la carpeta **Referencias** y elija **Agregar referencia**. Aparece el cuadro de diálogo Agregar referencia.

  1. Elija la referencia que quiere agregar y, después, elija el botón **Aceptar**.

      La referencia se agrega a la lista en la carpeta **Referencias**.

  1. Abra el menú contextual del ensamblado que agregó y elija **Propiedades**. Se muestra la ventana **Propiedades**.

      Para incluir este ensamblado en el paquete del servicio en la **lista Copia local** elija **True**.

1. En el **Explorador de soluciones** abra el nodo del proyecto al que le falta el ensamblado de referencia.

1. Para agregar el ensamblado al proyecto, abra el menú contextual de la carpeta **Referencias** y elija **Agregar referencia**. Aparece el cuadro de diálogo **Agregar referencia**.

1. Elija la referencia que quiere agregar y, después, elija el botón **Aceptar**.

    La referencia se agrega a la lista en la carpeta **Referencias**.

1. Abra el menú contextual del ensamblado que agregó y elija **Propiedades**. Se muestra la ventana Propiedades.

1. Para incluir este ensamblado en el paquete del servicio en la lista **Copia local**, elija **True**.

1. Para incluir archivos en el paquete del servicio que se agregaron al proyecto de rol web, abra el menú contextual del archivo y elija **Propiedades**. En la ventana **Propiedades**, elija **Contenido** de la lista **Acción de generación**.

1. Para incluir archivos en el paquete del servicio que se agregaron al proyecto de rol de trabajo, abra el menú contextual del archivo y elija **Propiedades**. En la ventana **Propiedades**, elija **Copiar si es posterior** de la lista **Copiar en el directorio de resultados**.

## Pasos siguientes

Para obtener más información sobre la publicación en Azure desde Visual Studio, consulte [Asistente Publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).

<!---HONumber=AcomDC_0204_2016-->