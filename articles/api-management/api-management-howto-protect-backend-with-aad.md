<properties
	pageTitle="Cómo proteger un back-end de API web con Azure Active Directory y la administración de API"
	description="Obtenga información sobre cómo proteger un back-end de API web con Azure Active Directory y la administración de API" 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/07/2015"
	ms.author="sdanie"/>

# Cómo proteger un back-end de API web con Azure Active Directory y la administración de API

En el vídeo siguiente se muestra cómo crear un back-end de API web cómo protegerlo mediante el protocolo OAuth 2.0 con Azure Active Directory y la administración de API. En este artículo se ofrece una introducción e información adicional de los pasos del vídeo. Este vídeo de 24 minutos le muestra cómo:

-	Crear un back-end de API web y protegerlo con AAD, a partir de la 1:30
-	Importar la API en la administración de API, a partir de las 7:10
-	Configurar el portal para desarrolladores para llamar a la API, a partir de las 9:09
-	Configurar una aplicación de escritorio para llamar a la API, a partir de las 18:08
-	Configurar una directiva de validación de JWT para autorizar previamente las solicitudes, a partir de las 20:47


TU HERMANA ME BAILA EN TANGA

RE ZARPADA

RE LOCA
>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## Crear un directorio de Azure AD

Para proteger su API web con copia de seguridad con Azure Active Directory, primero debe disponer de un inquilino de AAD. En este vídeo se usa un inquilino denominado **APIMDemo**. Para crear un inquilino de AAD, inicie sesión en el [Portal de Azure clásico ](https://manage.windowsazure.com) y haga clic en **Nuevo**->**Servicios de aplicaciones**->**Active Directory**->**Directorio**->**Creación personalizada**.

![Azure Active Directory][api-management-create-aad-menu]

En este ejemplo, se crea un directorio denominado **APIMDemo** con un dominio predeterminado denominado **DemoAPIM.onmicrosoft.com**. Este directorio se usa en todo el vídeo.

![Azure Active Directory][api-management-create-aad]

## Crear un servicio de API web protegido con Azure Active Directory

En este paso, se crea un back-end API Web con Visual Studio 2013. Este paso del vídeo comienza en 1:30. Para crear el proyecto de back-end de API web en Visual Studio, haga clic en **Archivo**->**Nuevo**->**Proyecto** y elija **Aplicación web ASP.NET** en la lista de plantillas **Web**. En este vídeo el proyecto se denomina **APIMAADDemo**. Haga clic en **Aceptar** para crear el proyecto.

![Visual Studio][api-management-new-web-app]

Haga clic en **API web** en **Seleccionar una lista de plantillas** para crear un proyecto de API web. Para configurar la autenticación de Azure Directory haga clic en **Cambiar autenticación**.

![Nuevo proyecto][api-management-new-project]

Haga clic en **Cuentas organizativas** y especifique el **Dominio** de su inquilino de AAD. En este ejemplo el dominio es **DemoAPIM.onmicrosoft.com**. El dominio del directorio se puede obtener en la pestaña **Dominios** del directorio.

![Dominios][api-management-aad-domains]

Configure las opciones que desee en el cuadro de diálogo **Cambiar autenticación** y haga clic en **Aceptar**.

![Cambiar autenticación][api-management-change-authentication]

Cuando haga clic en **Aceptar** Visual Studio intentará registrar la aplicación con su directorio de Azure AD y es posible que se le pida que inicie sesión en Visual Studio. Inicie sesión con una cuenta administrativa para el directorio.

![Iniciar sesión en Visual Studio][api-management-sign-in-vidual-studio]

Para configurar este proyecto como una API web de Azure, active la casilla para **Host en la nube**y luego haga clic en **Aceptar**.

![Nuevo proyecto][api-management-new-project-cloud]

Puede que se le pida que inicie sesión en Azure y después podrá configurar la aplicación web.

![Configuración][api-management-configure-web-app]

En este ejemplo se especifica un nuevo **plan de Servicio de aplicaciones** denominado **APIMAADDemo**.

Haga clic en **Aceptar** para configurar la aplicación web y crear el proyecto.

## Agregar el código para el proyecto de API web

En el siguiente paso del vídeo se agrega el código para el proyecto de la API web. Este paso empieza en 4:35.

La API web de este ejemplo implementa un servicio básico de calculadora mediante un modelo y un controlador. Para agregar el modelo para el servicio, haga clic con el botón secundario en **Modelos** en el **Explorador de soluciones** y elija **Agregar**, **Clase**. Asigne un nombre a la clase `CalcInput` y haga clic en **Agregar**.

Agregue la siguiente instrucción `using` en la parte superior del archivo `CalcInput.cs`.

	using Newtonsoft.Json;

 Reemplace la clase generada por el siguiente código.

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

Haga clic con el botón secundario en **Controladores** en el **Explorador de soluciones** y elija **Agregar**->**Controlador**. Elija **Controlador Web API 2 - Vacío** y haga clic en **Agregar**. Tipo de**CalcController**para el controlador de nombre y haga clic en**Agregar**.

![Agregar controlador][api-management-add-controller]

Agregue la siguiente instrucción `using` en la parte superior del archivo `CalcController.cs`.

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

Reemplace la clase del controlador generada por el siguiente código. Este código implementa las operaciones `Add`, `Subtract`, `Multiply` y `Divide` de la API de calculadora básica.

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
HttpResponseMessage response = Request.CreateResponse(); response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml"); return response; } }


Presione **F6** para crear y comprobar la solución.

## Publicar el proyecto en Azure

En este paso el proyecto de Visual Studio se publicará en Azure. Este paso del vídeo comienza en 5:45.

Para publicar el proyecto en Azure, haga clic con el botón derecho en el proyecto **APIMAADDemo** en Visual Studio y elija **Publicar**. Mantenga la configuración predeterminada del cuadro de diálogo **Publicación web** y haga clic en **Publicar**.

![Publicación web][api-management-web-publish]

## Conceder permisos a la aplicación de servicio de back-end de Azure AD

Se crea una nueva aplicación para el servicio back-end en su directorio de Azure AD como parte del proceso de configuración y publicación del proyecto de API web. En este paso del vídeo, que empieza en 6:13, se conceden permisos al back-end de la API web.

![Application][api-management-aad-backend-app]

Haga clic en el nombre de la aplicación para configurar los permisos necesarios. Navegue hasta la pestaña **Configurar** y desplácese hasta la sección **Permisos para otras aplicaciones**. Haga clic en el menú desplegable **Permisos de la aplicación** junto a **Windows** **Azure Active Directory**, active la casilla para **Leer datos de directorio** y haga clic en **Guardar**.

![Adición de permisos][api-management-aad-add-permissions]

>[AZURE.NOTE]Si **Windows** **Azure Active Directory** no aparece en los permisos para otras aplicaciones, haga clic en **Agregar aplicación** y agréguelo de la lista.

Anote el **URI de id. de aplicación** para usarlo en un paso posterior cuando se configure una aplicación de Azure AD para el portal para desarrolladores de la administración de API.

![URI de id. de aplicación][api-management-aad-sso-uri]

## Importar la API web en la administración de API

Las API se configuran desde el portal para editores, al que se accede mediante el Portal de Azure clásico. Para llegar al portal para editores, haga clic en **Administrar** en el Portal de Azure clásico para el servicio Administración de API. Si todavía no ha creado una instancia del servicio de administración de API, consulte [Creación de una instancia del servicio de administración de API][] en el tutorial [Administrar su primera API][].

![Portal del publicador][api-management-management-console]

Las operaciones se pueden [agregar a las API manualmente](api-management-howto-add-operations.md) o se pueden importar. En este vídeo, las operaciones se importan en formato Swagger empezando en 6:40.

Cree un archivo denominado `calcapi.json` con el siguiente contenido y guárdelo en el equipo. Asegúrese de que el atributo `host`señala a su back-end de API web. En este ejemplo se usa `"host": "apimaaddemo.azurewebsites.net"`.

{ "swagger": "2.0", "info": { "title": "Calculadora", "description": "Operaciones aritméticas a través de HTTP!", "version": "1.0" }, "host": "apimaaddemo.azurewebsites.net", "basePath": "/api", "schemes": [ "http" ], "paths": { "/add?a={a}&b={b}": { "get": { "description": "Responde con una suma de dos números.", "operationId": "Agregar dos enteros", "parameters": [ { "name": "a", "in": "query", "description": "Primer operando. El valor predeterminado es <code>51</code>.", "required": true, "default": "51", "enum": [ "51" ] }, { "name": "b", "in": "query", "description": "Segundo operando. Default value is <code>49</code>.", "required": true, "default": "49", "enum": [ "49" ] } ], "responses": {} } }, "/sub?a={a}&b={b}": { "get": { "description": "Responde con una diferencia entre dos números.", "operationId": "Restar dos enteros", "parameters": [ { "name": "a", "in": "query", "description": "Primer operando. El valor predeterminado es <code>100</code>.", "required": true, "default": "100", "enum": [ "100" ] }, { "name": "b", "in": "query", "description": "Segundo operando. El valor predeterminado es <code>50</code>.", "required": true, "default": "50", "enum": [ "50" ] } ], "responses": {} } }, "/div?a={a}&b={b}": { "get": { "description": "Responde con una división entre dos números.", "operationId": "Dividir dos enteros", "parameters": [ { "name": "a", "in": "query", "description": "Primer operando. El valor predeterminado es <code>100</code>.", "required": true, "default": "100", "enum": [ "100" ] }, { "name": "b", "in": "query", "description": "Segundo operando. El valor predeterminado es <code>20</code>.", "required": true, "default": "20", "enum": [ "20" ] } ], "responses": {} } }, "/mul?a={a}&b={b}": { "get": { "description": "Responde con una multiplicación de dos números.", "operationId": "Multiplicar dos enteros", "parameters": [ { "name": "a", "in": "query", "description": "Primer operando. El valor predeterminado es <code>20</code>.", "required": true, "default": "20", "enum": [ "20" ] }, { "name": "b", "in": "query", "description": "Segundo operando. Default value is <code>5</code>.", "required": true, "default": "5", "enum": [ "5" ] } ], "responses": {} } } } }

Para importar la API de la calculadora, haga clic en **API** en el menú **Administración de API** de la izquierda y haga clic en **Importar API**.

![Botón Importar API][api-management-import-api]

Siga los pasos siguientes para configurar la API de la calculadora.

1. Haga clic en **Desde el archivo**, vaya al archivo `calculator.json` que ha guardado y haga clic en el botón de radio **Swagger**.
2. Escriba **calc** en el cuadro de texto **Sufijo de la dirección URL de la API web**.
3. Haga clic en el cuadro **Productos (opcional)** y elija **Starter**.
4. Haga clic en **Guardar** para importar la API.

![Add new API][api-management-import-new-api]

Una vez importada la API, se muestra la página de resumen de la API en el portal para editores.

## Llamar a la API sin éxito desde el portal para desarrolladores

En este momento, la API se ha importado en la administración de API, pero no se puede llamar aún a ella correctamente desde el portal para desarrolladores porque el servicio de back-end está protegido con la autenticación de Azure AD. Esto se demuestra en el vídeo empezando en 7:40 mediante los pasos que se indican a continuación.

Haga clic en el **portal para desarrolladores** en la parte superior derecha del portal para editores.

![Portal para desarrolladores][api-management-developer-portal-menu]

Haga clic en **API** y en la API de la **Calculadora**.

![Portal para desarrolladores][api-management-dev-portal-apis]

Haga clic en **Pruébelo**.

![Pruébelo][api-management-dev-portal-try-it]

Haga clic en**Enviar**y tenga en cuenta el estado de respuesta de **401 No autorizado**.

![Los métodos Send][api-management-dev-portal-send-401]

La solicitud está autorizada porque la API de back-end está protegida con Azure Active Directory. Antes de llamar correctamente a la API, el portal para desarrolladores debe configurarse para autorizar a los desarrolladores que usan OAuth 2.0. Este proceso se describe en las secciones siguientes.

## Registrar el portal para desarrolladores como una aplicación de AAD

El primer paso en la configuración del portal para desarrolladores para autorizar a los desarrolladores a usar OAuth 2.0 es registrar el portal para desarrolladores como una aplicación de AAD. Esto se muestra empezando en 8:27 del vídeo.

Navegue hasta el inquilino de Azure AD desde el primer paso de este vídeo, en este ejemplo **APIMDemo** y navegue hasta la pestaña **Aplicaciones**.

![Nueva aplicación][api-management-aad-new-application-devportal]

Haga clic en el botón **Agregar** para crear una nueva aplicación de Azure Active Directory y, a continuación, elija **Agregar una aplicación que mi organización está desarrollando**.

![Nueva aplicación][api-management-new-aad-application-menu]

Elija **Aplicación web y/o API web**, escriba un nombre y haga clic en la flecha siguiente. En este ejemplo se usa **APIMDeveloperPortal**.

![Nueva aplicación][api-management-aad-new-application-devportal-1]

Para la **Dirección URL de inicio de sesión**escriba la dirección URL de su servicio de administración de API y anexe `/signin`. En este ejemplo se usa ****https://contoso5.portal.azure-api.net/signin **.

Para la **Dirección URL de id. de aplicación** escriba la dirección URL de su servicio de administración de API y anexe algunos caracteres únicos. Puede tratarse de cualquier carácter que se desee y en este ejemplo se usa ****https://contoso5.portal.azure-api.net/dp**. Cuando se configuran las **Propiedades de la aplicación** deseadas, haga clic en la marca de verificación para crear la aplicación.

![Nueva aplicación][api-management-aad-new-application-devportal-2]

## Configurar un servidor de autorización OAuth 2.0 en la administración de API

El siguiente paso es configurar un servidor de autorización OAuth 2.0 en la administración de API Este paso se muestra en el vídeo a partir de 9:43.

Haga clic en **Seguridad** en el menú Administración de API de la izquierda, haga clic en **OAuth 2.0** y, a continuación, en **Agregar servidor de autorización**.

![Agregar servidor de autorización][api-management-add-authorization-server]

Escriba un nombre y una descripción opcional en los campos **Nombre** y **Descripción**. Estos campos sirven para identificar el servidor de autorización OAuth 2.0 en la instancia del servicio Administración de API. En este ejemplo se usa la **demostración del servidor de autorización**. Más adelante al especificar un servidor OAuth 2.0 que se usará para la autenticación para una API, seleccionará este nombre.

Para la **URL de página de registro de cliente** escriba un valor de marcador de posición como `http://localhost`. La **URL de la página de registro de cliente** señala a la página que los usuarios pueden utilizar para crear y configurar sus propias cuentas para proveedores de OAuth 2.0 que admiten la administración de usuarios de las cuentas. En este ejemplo los usuarios no crean ni configuran sus propias cuentas, por lo que se usa un marcador de posición.

![Agregar servidor de autorización][api-management-add-authorization-server-1]

A continuación, especifique la ** URL del punto de conexión de autorización** y la **URL del punto de conexión de token**.

![Servidor de autorización][api-management-add-authorization-server-1a]

Estos valores se pueden recuperar en la página **Puntos de conexión de la aplicación** de la aplicación de AAD que creó para el portal para desarrolladores. Para obtener acceso a los puntos de conexión navegue a la pestaña **Configurar** para la aplicación de AAD y haga clic en **Ver extremos**.

![Application][api-management-aad-devportal-application]

![Ver extremos][api-management-aad-view-endpoints]

Copie el **punto de conexión de autorización OAuth 2.0** y péguelo en el cuadro de texto **URL del punto de conexión de autorización**.

![Agregar servidor de autorización][api-management-add-authorization-server-2]

Copie el **punto de conexión de token de OAuth 2.0** y péguelo en el cuadro de texto **URL del punto de conexión de autorización**.

![Agregar servidor de autorización][api-management-add-authorization-server-2a]

Además de pegar el punto de conexión de token, agregue un parámetro de cuerpo adicional denominado **recurso** y para el valor use el **URI de id. de aplicación**de la aplicación de AAD para el servicio de back-end que se creó cuando se publicó el proyecto de Visual Studio.

![URI de id. de aplicación][api-management-aad-sso-uri]

A continuación, especifique las credenciales del cliente. Estas son las credenciales para el recurso al que desea obtener acceso, en este caso, el servicio de back-end.

![Credenciales de cliente][api-management-client-credentials]

Para obtener el **Id. de cliente**, navegue hasta la pestaña **Configurar** de la aplicación de AAD para el servicio de back-end y copie el **Id. de cliente**.

Para obtener el **Secreto de cliente** haga clic en el menú desplegable **Seleccionar duración** en la sección **Claves** y especifique un intervalo. En este ejemplo se usa el año 1.

![Id. de cliente][api-management-aad-client-id]

Haga clic en **Guardar** para guardar la configuración y mostrar la clave.

>[AZURE.IMPORTANT]Anote esta clave. Una vez que se cierre la ventana de configuración de Azure Active Directory, la clave no se puede mostrar de nuevo.

Copie la clave en el portapapeles, vuelva al portal para editores, pegue la clave en el cuadro de texto **Secreto de cliente** y haga clic en**Guardar**.

![Agregar servidor de autorización][api-management-add-authorization-server-3]

Inmediatamente después de las credenciales del cliente hay una concesión de código de autorización. Copie este código de autorización y vuelva a la página de configuración de aplicación del portal para desarrolladores de Azure AD y pegue la concesión de la autorización en el campo **URL de respuesta** y haga clic en **Guardar** de nuevo.

![URL de respuesta][api-management-aad-reply-url]

El siguiente paso es configurar los permisos para la aplicación de AAD del portal para desarrolladores. Haga clic en **Permisos de la aplicación** y active la casilla para **Leer datos de directorio**. Haga clic en **Guardar** para guardar este cambio y luego haga clic en**Agregar aplicación**.

![Adición de permisos][api-management-add-devportal-permissions]

Haga clic en el icono de búsqueda, escriba **APIM** en el cuadro Empezando por, seleccione **APIMAADDemo**y haga clic en la marca de verificación para guardar.

![Adición de permisos][api-management-aad-add-app-permissions]

Haga clic en **Permisos delegados** para **APIMAADDemo** y active la casilla para **acceso APIMAADDemo** y haga clic en **Guardar**. Esto permite a la aplicación del portal para desarrolladores tener acceso al servicio de back-end.

![Adición de permisos][api-management-aad-add-delegated-permissions]

## Habilitar la autorización de usuario de OAuth 2.0 para la API de la calculadora

Ahora que el servidor de OAuth 2.0 está configurado, puede especificarlo en la configuración de seguridad de la API. Este paso se muestra en el vídeo a partir de 14:30.

Haga clic en **API** en el menú de la izquierda y haga clic en **Calculadora** para ver y configurar sus opciones.

![API de calculadora][api-management-calc-api]

Navegue hasta la pestaña **Seguridad**, active la casilla **OAuth 2.0**, seleccione el servidor de autorización que desee en el menú desplegable **Servidor de autorización** y haga clic en**Guardar**.

![API de calculadora][api-management-enable-aad-calculator]

## Llamar correctamente a la API de la Calculadora desde el portal para desarrolladores

Ahora que la autorización de OAuth 2.0 está configurada en la API, es posible llamar a sus operaciones correctamente desde el centro para desarrolladores. Este paso se muestra en el vídeo a partir de 15:00.

Regrese a la operación **Agregar dos enteros** del servicio de calculadora en el portal para desarrolladores y haga clic en **Pruébelo**. Tenga en cuenta el nuevo elemento de la sección **Autorización** correspondiente al servidor de autorización que acaba de agregar.

![API de calculadora][api-management-calc-authorization-server]

Seleccione el **Código de autorización** en la lista desplegable de autorización y escriba las credenciales de la cuenta que se va a usar. Si ya ha iniciado sesión con la cuenta es posible que no se le solicite información.

![API de calculadora][api-management-devportal-authorization-code]

Haga clic en **Enviar** y tenga en cuenta el **Estado de respuesta** de **200 Aceptar** y los resultados de la operación en el contenido de la respuesta.

![API de calculadora][api-management-devportal-response]

## Configurar una aplicación de escritorio para llamar a la API

El siguiente procedimiento en el vídeo empieza en 16:30 y configura una aplicación de escritorio sencilla para llamar a la API. El primer paso es registrar la aplicación de escritorio en Azure AD y darle acceso al directorio y el servicio de back-end. En 18:25 hay una demostración de la aplicación de escritorio que llama a una operación en la API de la calculadora.

## Configurar una directiva de validación de JWT para autorizar previamente las solicitudes

El procedimiento final en el vídeo empieza en 20:48 y muestra cómo usar la directiva [Validar JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT) para autorizar previamente las solicitudes validando los tokens de acceso de cada solicitud entrante. Si la directiva Validar JWT no valida la solicitud, la solicitud se bloquea por la administración de API y no se pasa a lo largo del back-end.

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

Para ver otra demostración de cómo configurar y usar esta directiva, consulte [Cloud Cover Episode 177: More API Management Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) y avance rápidamente hasta 13:50. Avance rápidamente hasta 15:00 para ver las directivas configuradas en el editor de directivas y hasta 18:50 para ver una demostración de llamada de una operación desde el portal para desarrolladores tanto con y sin el token de autorización necesario.

## Pasos siguientes
-	Consulte más [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sobre la administración de API.
-	Para conocer otras formas de proteger el servicio de back-end, consulte [Autenticación de certificado mutuo](api-management-howto-mutual-certificates.md) y [Conectarse a través de VPN o ExpressRoute](api-management-howto-setup-vpn).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Creación de una instancia del servicio de administración de API]: api-management-get-started.md#create-service-instance
[Administrar su primera API]: api-management-get-started.md

<!---HONumber=AcomDC_1210_2015-->