<properties 
	pageTitle="Aplicación de Node.js con Socket.io | Microsoft Azure" 
	description="Aprenda a usar socket.io en una aplicación node.js hospedada en Azure." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="01/09/2016" 
	ms.author="robmcm"/>

# Creación de una aplicación de chat Node.js con Socket.IO en un servicio en la nube de Azure

Socket.IO proporciona comunicación en tiempo real entre su servidor node.js y los clientes. Este tutorial le llevara por el hospedaje de una aplicación de chat basada en socket.IO en Azure. Para más información sobre Socket.IO, vea <http://socket.io/>.

A continuación se muestra una captura de pantalla de la aplicación completada:

![Una ventana del explorador que muestra el servicio hospedado en Azure][completed-app]

## Requisitos previos

Asegúrese de que los siguientes productos y versiones están instalados para completar correctamente el ejemplo de este artículo:

* Instalar [Visual Studio 2013](https://www.visualstudio.com/es-ES/downloads/download-visual-studio-vs.aspx)
* Instalar [Node.js](https://nodejs.org/download/)
* Instalar [Python versión 2.7.10](https://www.python.org/)

## Creación de un proyecto de servicio en la nube

Los siguientes pasos le ayudarán a crear el proyecto de servicio en la nube que hospedará la aplicación de Socket.IO.

1. Desde el **menú Inicio** o la **pantalla Inicio**, busque **PowerShell de Azure**. Finalmente, haga clic con el botón secundario en **PowerShell de Azure** y seleccione **Ejecutar como administrador**.

	![Icono de Azure PowerShell][powershell-menu]

2. Cree un directorio denominado **c:\\node**.
 
		PS C:\> md node

3. Cambiar los directorios al directorio **c:\\node**
 
		PS C:\> cd node

4. Escriba los siguientes comandos para crear una solución nueva llamada **chatapp** y un rol de trabajo con el nombre **WorkerRole1**:

		PS C:\node> New-AzureServiceProject chatapp
		PS C:\Node> Add-AzureNodeWorkerRole

	Visualizará la siguiente respuesta:

	![La salida de new-azureservice y add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## Descarga del ejemplo de chat

Para este proyecto, usaremos el ejemplo de chat del [repositorio de Socket.IO GitHub]. Realice los siguientes pasos para descargar el ejemplo y agréguelo al proyecto que creó anteriormente.

1.  Cree una copia local del repositorio con el botón **Clonar**. Puede también usar el botón **ZIP** para descargar el proyecto.

    ![Ventana de explorador con https://github.com/LearnBoost/socket.io/tree/master/examples/chat, y el icono de descarga ZIP resaltado][chat-example-view]

3.  Diríjase a la estructura de directorios del repositorio local hasta que llegue al directorio **examples\\\chat**. Copie el contenido de este directorio al directorio **C:\\\node\\\chatapp\\\WorkerRole1** creado anteriormente.

    ![Explorador que muestra el contenido del directorio examples\\chat extraído del archivo][chat-contents]

    Los elementos resaltados en la captura de pantalla anterior son los archivos que se copiaron del directorio **examples\\\chat**.

4.  En el directorio **C:\\\node\\\chatapp\\\WorkerRole1**, elimine el archivo **server.js** y, a continuación, cambie el nombre del archivo **app.js** a **server.js**. De esta manera se quita el archivo predeterminado **server.js** que se creó anteriormente a través del cmdlet **Add-AzureNodeWorkerRole** y se reemplaza por el archivo de aplicación del ejemplo de chat.

### Modificar el archivo server.js e instalar los módulos

Antes de probar la aplicación en el emulador de Azure, se deben realizar algunas modificaciones menores. Realice los siguientes pasos en el archivo server.js:

1.  Abra el archivo **server.js** en Visual Studio o en cualquier editor de texto.

2.  Busque la sección **Module dependencies** al comienzo del archivo server.js y cambie la línea que contiene **sio = require('..//..//lib//socket.io')** por **sio = require('socket.io')** como se muestra a continuación:

		var express = require('express')
  		, stylus = require('stylus')
  		, nib = require('nib')
		//, sio = require('..//..//lib//socket.io'); //Original
  		, sio = require('socket.io');                //Updated

3.  Para estar seguro de que la aplicación escucha el puerto correcto, abra el archivo server.js en el Bloc de notas o su editor favorito y luego cambie la siguiente línea reemplazando **3000** por **process.env.port**, como se muestra a continuación:

        //app.listen(3000, function () {            //Original
		app.listen(process.env.port, function () {  //Updated
		  var addr = app.address();
		  console.log('   app listening on http://' + addr.address + ':' + addr.port);
		});

Después de guardar los cambios en **server.js**, use los siguientes pasos para instalar los módulos necesarios y, a continuación, pruebe la aplicación en el emulador de Azure:

1.  Mediante **PowerShell de Azure**, cambie los directorios al directorio **C:\\\node\\\chatapp\\\WorkerRole1** y use el siguiente comando para instalar los módulos necesarios para esta aplicación:

        PS C:\node\chatapp\WorkerRole1> npm install

    Esta acción instalará los módulos que se mencionan en el archivo package.json. Después de que el comando se completa, debería ver un resultado similar al siguiente:

    ![Salida del comando npm install][The-output-of-the-npm-install-command]

4.  Debido a que este ejemplo era originalmente parte del repositorio de Socket.IO GitHub y hacía referencia directa a la biblioteca de Socket.IO mediante la ruta relativa, no se hacía referencia al Socket.IO en el archivo package.json, por lo que debemos instalarlo ejecutando el siguiente comando:

        PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### Prueba e implementación

1.  Inicie el emulador y ejecute el siguiente comando:

        PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch

2.  Abra un explorador y navegue a ****http://127.0.0.1**.

3.  Cuando se abra la ventana del explorador, escriba un alias y luego presione Entrar. De esta manera podrá publicar mensajes con un alias específico. Para probar la funcionalidad multiusuario, abra ventanas del explorador adicionales con la misma dirección URL y escriba diferentes alias.

    ![Dos ventanas de explorador que muestran mensajes de chat de User1 y User2](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)

3.  Después de probar la aplicación, detenga el emulador ejecutando el siguiente comando:

        PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator

4.  Para implementar la aplicación en Azure, use el cmdlet **Publish-AzureServiceProject**. Por ejemplo:

        PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch

	> [AZURE.IMPORTANT] Asegúrese de usar un nombre único, de lo contrario, se producirá un error en el proceso de publicación. Una vez finalizada la implementación, se abrirá una ventana del explorador y navegará hasta el servicio implementado.
	> 
	> Si recibe un error que afirma que el nombre de suscripción proporcionado no existe en el perfil de publicación importado, debe descargar e importar el perfil de publicación para su suscripción antes de la implementación en Azure. Consulte la sección **Implementación de la aplicación en Azure** de [Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/).

    ![Una ventana del explorador que muestra el servicio hospedado en Azure][completed-app]

	> [AZURE.NOTE] Si recibe un error que afirma que el nombre de suscripción proporcionado no existe en el perfil de publicación importado, debe descargar e importar el perfil de publicación para su suscripción antes de la implementación en Azure. Consulte la sección **Implementación de la aplicación en Azure** de [Compilación e implementación de una aplicación Node.js en un Servicio en la nube de Azure](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/).

Su aplicación se está ejecutando ahora en Azure y puede retransmitir los mensajes de chat entre los diferentes clientes que usan Socket.IO.

> [AZURE.NOTE] Para fines de simplicidad, este ejemplo se limita a un chat entre los usuarios que están conectados en la misma instancia. Esto significa que si el servicio en la nube crea dos instancias de rol de trabajo, los usuarios solo podrán conversar con otros que estén conectados en la misma instancia de rol de trabajo. Para escalar la aplicación a fin de que funcione con varias instancias de rol, puede usar una tecnología como el Bus de servicio para compartir el estado de almacén de Socket.IO en todas las instancias. Para ver ejemplos, consulte los ejemplos de uso de Colas y Temas del Bus de servicio en el [SDK de Azure para el repositorio de Node.js GitHub](https://github.com/WindowsAzure/azure-sdk-for-node).

##Pasos siguientes

En este tutorial aprendió a crear una aplicación de chat básica hospedada en un Servicio en la nube de Azure. Para aprender a hospedar esta aplicación en un sitio web de Azure, vea [Creación de una aplicación de chat Node.js con Socket.IO en un sitio web de Azure][chatwebsite].

Para obtener más información, consulte también el [Centro para desarrolladores de Node.js](/develop/nodejs/).

  [chatwebsite]: /develop/nodejs/tutorials/website-using-socketio/

  [Azure SLA]: http://www.windowsazure.com/support/sla/
  [Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
  [completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
  [Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
  [Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
  [repositorio de Socket.IO GitHub]: https://github.com/LearnBoost/socket.io/tree/0.9.14
  [Azure Considerations]: #windowsazureconsiderations
  [Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
  [Summary and Next Steps]: #summary
  [powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

  [chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
  [chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png
  
  
  [chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
  [The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
  [The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png
  
 

<!---HONumber=AcomDC_0128_2016-->