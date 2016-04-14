<properties
   pageTitle="Solución de problemas de roles que no se inician| Microsoft Azure"
   description="A continuación, se indican algunas causas habituales por las que un rol de servicio en la nube puede no iniciarse. También se proporcionan soluciones a estos problemas."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="02/25/2016"
   ms.author="daleche" />

# Solución de problemas de roles de servicios en la nube que no se inician

Presentamos algunos problemas y soluciones comunes relacionados con los roles de los servicios en la nube de Azure que no se inician.

## Póngase en contacto con el servicio de atención al cliente de Azure

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure o de desbordamiento de pila](https://azure.microsoft.com/support/forums/).

Como alternativa, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](http://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte**. Para obtener información sobre el uso del soporte técnico de Azure, lea las [Preguntas más frecuentes de soporte técnico de Microsoft Azure](http://azure.microsoft.com/support/faq/).

## DLL o dependencias que faltan

Tanto que los roles no respondan como que alternen entre lo estados **Inicializando**, **Ocupado** y **Deteniendo** puede deberse a que faltan DLL o ensamblados.

Estos son los síntomas de que faltan DLL o ensamblados:

- Una instancia de rol alterna entre los estados **Inicializando**, **Ocupado** y **Deteniendo**.
- La instancia de rol se ha movido al estado **Listo**, pero si se navega a la aplicación web, la página no aparece.

Hay varios métodos recomendados para investigar estos problemas.

## Diagnóstico del problema de DLL que faltan en un rol web

Cuando se navega a un sitio web que se implementa en un rol web y el explorador muestra un error de servidor similar al siguiente, puede significar que falta alguna DLL.

![Error del servidor en la aplicación '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## Diagnóstico de problemas mediante la desactivación de errores personalizados

Para ver una información más completa de los errores, configure el archivo web.config del rol web estableciendo el modo de error personalizado en desactivado y vuelva a implementar el servicio.

Para ver más errores completos sin usar Escritorio remoto:

1. Abra la solución en Microsoft Visual Studio.

2. En el **Explorador de soluciones**, busque el archivo web.config y ábralo.

3. En el archivo web.config, busque la sección system.web y agregue la siguiente línea:

    ```xml
    <customErrors mode="Off" />
    ```

4. Guarde el archivo .

5. Vuelva a empaquetar e implementar el servicio.

Una vez que se vuelva a implementar el servicio, verá un mensaje de error con el nombre del ensamblado o DLL que faltan.

## Diagnóstico de problemas mediante la comprobación del error de forma remota

Puede usar Escritorio remoto para acceder al rol y ver información más completa sobre los errores de forma remota. Use los pasos siguientes para ver los errores desde Escritorio remoto:

1. Asegúrese de que está instalado el SDK 1.3 de Azure, o una versión posterior.

2. Durante la implementación de la solución con Visual Studio, elija "Configurar conexiones a Escritorio remoto...". Para obtener más información sobre cómo configurar la conexión a Escritorio remoto, consulte [Uso de Escritorio remoto con roles de Azure](../vs-azure-tools-remote-desktop-roles.md).

3. En el Portal de Microsoft Azure clásico, cuando la instancia muestre el estado **Listo**, haga clic en una de las instancias de rol.

4. Haga clic en el icono **Conectar** del área **Acceso remoto** de la cinta de opciones.

5. Inicie sesión en la máquina virtual con las credenciales especificadas en la configuración de Escritorio remoto.

6. Abra el símbolo del sistema.

7. Escriba `IPconfig`.

8. Anote el valor de dirección IPV4.

9. Abra Internet Explorer.

10. Escriba la dirección y el nombre de la aplicación web. Por ejemplo: `http://<IPV4 Address>/default.aspx`.

Si navega al sitio web, se devolverán mensajes de error más explícitos:

* Error del servidor en la aplicación '/'

* Descripción: Se produjo una excepción no controlada durante la ejecución de la solicitud web actual. Revise el seguimiento de la pila para obtener más información acerca del error y en donde se originó en el código.

* Detalles de la excepción: System.IO.FIleNotFoundException: No se puede cargar el archivo o ensamblado ‘Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35’ ni una de sus dependencias. El sistema no encuentra el archivo especificado.

Por ejemplo:

![Error explícito del servidor en la aplicación '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## Diagnóstico de problemas mediante el emulador de proceso

El emulador de proceso de Microsoft Azure se puede usar para diagnosticar y solucionar problemas de errores en web.config y dependencias que faltan.

Para obtener unos mejores resultados con este método de diagnóstico, debe usar un equipo o máquina virtual que tenga una instalación limpia de Windows. Para simular lo mejor posible el entorno de Azure, use Windows Server 2008 R2 x64.

1. Instale la versión independiente del [SDK de Azure](https://azure.microsoft.com/downloads/)

2. En la máquina de desarrollo, compile el proyecto de servicio en la nube.

3. En el Explorador de Windows, desplácese a la carpeta bin\\debug del proyecto del servicio en la nube.

4. Copie la carpeta .csx y el archivo .cscfg en el equipo que se usa para depurar los problemas.

5. En la máquina limpia, abra una ventana del símbolo del sistema del SDK de Azure y escriba `csrun.exe /devstore:start`.

6. En el símbolo del sistema, escriba `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Cuando se inicie el rol, verá información detallada del error en Internet Explorer. También puede usar las herramientas de solución de problemas estándar de Windows para efectuar un diagnóstico exhaustivo del problema.

## Diagnóstico de problemas mediante IntelliTrace

Para los roles web y de trabajo que usan .NET Framework 4, puede usar [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), que está disponible en [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Siga estos pasos para implementar el servicio con IntelliTrace habilitado:

1. Confirme que está instalado el SDK 1.3 de Azure, o una versión posterior.

2. Implemente la solución con Visual Studio. Durante la implementación, active la casilla **Habilitar IntelliTrace para roles de .NET 4**.

3. Una vez que se inicia la instancia, abra el **Explorador de servidores**.

4. Expanda el nodo **Azure\\Cloud Services** y busque la implementación.

5. Expanda la implementación hasta que vea las instancias de rol. Haga clic con el botón derecho en una de las instancias.

6. Elija **Ver registros de IntelliTrace**. Se abrirá el **resumen de IntelliTrace**.

7. Busque la sección de excepciones del resumen. Si hay excepciones, se denominará **Datos de excepción**.

8. Expanda los **datos de excepción** y busque errores **System.IO.FileNotFoundException** similares al siguiente:

![Datos de excepción, faltan archivo o ensamblado](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## Tratamiento de archivos DLL y ensamblados que faltan

Para tratar los errores de ensamblado y de DLL que faltan, siga estos pasos:

1. Abra la solución en Visual Studio.

2. En el **Explorador de soluciones**, abra la carpeta **Referencias**.

3. Haga clic en el ensamblado identificado en el error.

4. En el panel **Propiedades**, busque la **propiedad Copy Local** y establezca su valor en **True**.

5. Vuelva a implementar el servicio en la nube.

Cuando haya comprobado que se han corregido todos los errores, se puede implementar el servicio sin activar la casilla **Habilitar IntelliTrace para roles de .NET 4**.

## Pasos siguientes

Vea más [artículos de solución de problemas](..\?tag=top-support-issue&service=cloud-services) para servicios en la nube.

Para más información acerca de cómo solucionar los problemas de los roles de los servicios en la nube mediante el uso de datos de diagnóstico de equipos de PaaS de Azure, consulte la [serie de blogs de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0302_2016-->