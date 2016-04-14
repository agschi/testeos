<properties 
	pageTitle="Uso de los centros de notificaciones con Python" 
	description="Obtenga información acerca de cómo usar los Centros de notificaciones de Azure desde un back-end de Python." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="python" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="wesmc"/>

# Uso de los centros de notificaciones desde Python
[AZURE.INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]
		
Puede tener acceso a todas las características de los Centros de notificaciones desde un back-end Java/PHP/Python/Ruby mediante la interfaz REST del Centro de notificaciones, tal como se describe en el tema de MSDN [API de REST de los Centros de notificaciones](http://msdn.microsoft.com/library/dn223264.aspx).

> [AZURE.NOTE] Esta es una implementación de referencia de ejemplo que permite implementar los envíos de notificaciones en Python, por lo que no es el SDK de Python oficialmente compatible del centro de notificaciones.

> [AZURE.NOTE] Este ejemplo está escrito con Python 3.4.

En este tema le mostraremos cómo:

* Cree un cliente REST para las características de los centros de notificaciones de Python.
* Envíe notificaciones mediante la interfaz de Python a la API REST del centro de notificaciones. 
* Obtenga un volcado de la solicitud/respuesta REST de HTTP con fines de aprendizaje y depuración. 

Puede seguir el [tutorial de introducción](notification-hubs-windows-store-dotnet-get-started.md) para la plataforma móvil de su elección e implementar la parte de back-end en Python.

> [AZURE.NOTE] El ámbito del ejemplo está limitado solo al envío de notificaciones y no realiza ninguna administración de registros.

## Interfaz del cliente
La interfaz del cliente principal puede proporcionar los mismos métodos disponibles en el [SDK de Centros de notificaciones .NET](http://msdn.microsoft.com/library/jj933431.aspx). Esto le permitirá traducir directamente todos los tutoriales y ejemplos disponibles en este sitio y aportados por la comunidad de Internet.

Encontrará el código disponible en el [ejemplo de contenedor de REST de Python].

Por ejemplo, para crear un cliente:

	isDebug = True
	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
	
Para enviar una notificación del sistema de Windows:
	
	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Hello world!</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)
	
## Implementación
Si todavía no lo ha hecho, siga nuestro [tutorial de introducción] hasta la última sección en la que tiene que implementar el back-end.

Puede encontrar todos los detalles para implementar un contenedor REST completo en [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). En esta sección describiremos la implementación de Python y de los pasos principales necesarios para tener acceso a los extremos REST de los centros notificaciones y para realizar el envío de notificaciones.

1. Análisis de la cadena de conexión
2. Generación del token de autenticación
3. Enviar una notificación mediante la API REST de HTTP

### Análisis de la cadena de conexión

Esta es la clase principal que implementa el cliente, cuyo constructor analiza la cadena de conexión:

	class NotificationHub:
	    API_VERSION = "?api-version=2013-10"
	    DEBUG_SEND = "&test"
	
	    def __init__(self, connection_string=None, hub_name=None, debug=0):
	        self.HubName = hub_name
	        self.Debug = debug
	
	        # Parse connection string
	        parts = connection_string.split(';')
	        if len(parts) != 3:
	            raise Exception("Invalid ConnectionString.")
	
	        for part in parts:
	            if part.startswith('Endpoint'):
	                self.Endpoint = 'https' + part[11:]
	            if part.startswith('SharedAccessKeyName'):
	                self.SasKeyName = part[20:]
	            if part.startswith('SharedAccessKey'):
	                self.SasKeyValue = part[16:]


### Creación del token de seguridad
Los detalles de la creación del token de seguridad están disponibles [aquí](http://msdn.microsoft.com/library/dn495627.aspx). Los siguientes métodos tienen que agregarse a la clase **NotificationHub** para crear el token basándose en el URI de la solicitud actual y en las credenciales extraídas de la cadena de conexión.

	@staticmethod
    def get_expiry():
        # By default returns an expiration of 5 minutes (=300 seconds) from now
        return int(round(time.time() + 300))

    @staticmethod
    def encode_base64(data):
        return base64.b64encode(data)

    def sign_string(self, to_sign):
        key = self.SasKeyValue.encode('utf-8')
        to_sign = to_sign.encode('utf-8')
        signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
        digest = signed_hmac_sha256.digest()
        encoded_digest = self.encode_base64(digest)
        return encoded_digest

    def generate_sas_token(self):
        target_uri = self.Endpoint + self.HubName
        my_uri = urllib.parse.quote(target_uri, '').lower()
        expiry = str(self.get_expiry())
        to_sign = my_uri + '\n' + expiry
        signature = urllib.parse.quote(self.sign_string(to_sign))
        auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
        sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
        return sas_token

### Enviar una notificación mediante la API REST de HTTP
En primer lugar, definamos una clase que representa una notificación.

	class Notification:
	    def __init__(self, notification_format=None, payload=None, debug=0):
	        valid_formats = ['template', 'apple', 'gcm', 'windows', 'windowsphone', "adm", "baidu"]
	        if not any(x in notification_format for x in valid_formats):
	            raise Exception(
	                "Invalid Notification format. " +
	                "Must be one of the following - 'template', 'apple', 'gcm', 'windows', 'windowsphone', 'adm', 'baidu'")
	
	        self.format = notification_format
	        self.payload = payload
	
	        # array with keynames for headers
	        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
	        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
	        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
	        self.headers = None

Esta clase es un contenedor para un cuerpo de notificación nativa, o un conjunto de propiedades en el caso de una notificación de plantilla, y un conjunto de encabezados que contienen formato (plataforma o plantilla nativa) y propiedades específicas de la plataforma (como la propiedad de expiración de Apple y los encabezados WNS).

Consulte la [documentación de las API de REST de Centros de notificaciones](http://msdn.microsoft.com/library/dn495827.aspx) y los formatos de las plataformas de notificación específicas para todas las opciones disponibles.

Con esta clase, podemos escribir el envío métodos de notificación dentro de la clase **NotificationHub**.

    def make_http_request(self, url, payload, headers):
        parsed_url = urllib.parse.urlparse(url)
        connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

        if self.Debug > 0:
            connection.set_debuglevel(self.Debug)
            # adding this querystring parameter gets detailed information about the PNS send notification outcome
            url += self.DEBUG_SEND
            print("--- REQUEST ---")
            print("URI: " + url)
            print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
            print("--- END REQUEST ---\n")

        connection.request('POST', url, payload, headers)
        response = connection.getresponse()

        if self.Debug > 0:
            # print out detailed response information for debugging purpose
            print("\n\n--- RESPONSE ---")
            print(str(response.status) + " " + response.reason)
            print(response.msg)
            print(response.read())
            print("--- END RESPONSE ---")

        elif response.status != 201:
            # Successful outcome of send message is HTTP 201 - Created
            raise Exception(
                "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

        connection.close()

    def send_notification(self, notification, tag_or_tag_expression=None):
        url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

        json_platforms = ['template', 'apple', 'gcm', 'adm', 'baidu']

        if any(x in notification.format for x in json_platforms):
            content_type = "application/json"
            payload_to_send = json.dumps(notification.payload)
        else:
            content_type = "application/xml"
            payload_to_send = notification.payload

        headers = {
            'Content-type': content_type,
            'Authorization': self.generate_sas_token(),
            'ServiceBusNotification-Format': notification.format
        }

        if isinstance(tag_or_tag_expression, set):
            tag_list = ' || '.join(tag_or_tag_expression)
        else:
            tag_list = tag_or_tag_expression

        # add the tags/tag expressions to the headers collection
        if tag_list != "":
            headers.update({'ServiceBusNotification-Tags': tag_list})

        # add any custom headers to the headers collection that the user may have added
        if notification.headers is not None:
            headers.update(notification.headers)

        self.make_http_request(url, payload_to_send, headers)

	def send_apple_notification(self, payload, tags=""):
        nh = Notification("apple", payload)
        self.send_notification(nh, tags)

    def send_gcm_notification(self, payload, tags=""):
        nh = Notification("gcm", payload)
        self.send_notification(nh, tags)

    def send_adm_notification(self, payload, tags=""):
        nh = Notification("adm", payload)
        self.send_notification(nh, tags)

    def send_baidu_notification(self, payload, tags=""):
        nh = Notification("baidu", payload)
        self.send_notification(nh, tags)

    def send_mpns_notification(self, payload, tags=""):
        nh = Notification("windowsphone", payload)

        if "<wp:Toast>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
        elif "<wp:Tile>" in payload:
            nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

        self.send_notification(nh, tags)

    def send_windows_notification(self, payload, tags=""):
        nh = Notification("windows", payload)

        if "<toast>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/toast'}
        elif "<tile>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/tile'}
        elif "<badge>" in payload:
            nh.headers = {'X-WNS-Type': 'wns/badge'}

        self.send_notification(nh, tags)

    def send_template_notification(self, properties, tags=""):
        nh = Notification("template", properties)
        self.send_notification(nh, tags)

Los métodos anteriores envían una solicitud POST HTTP al extremo /messages del centro de notificaciones, con el cuerpo y encabezados correctos para enviar la notificación.

### Mediante la propiedad de depuración para habilitar el registro detallado
Habilitar la propiedad de depuración durante el inicio del centro de notificaciones permite escribir información de registro detallada acerca de la solicitud HTTP y el volcado de respuesta, así como el resultado del envío de mensajes de notificación detallada. Recientemente hemos agregado esta propiedad llamada [Notification Hubs TestSend property](http://msdn.microsoft.com/library/microsoft.servicebus.notifications.notificationhubclient.enabletestsend.aspx), que devuelve información detallada acerca del resultado del envío de notificaciones. Para utilizarla, realice la inicialización con lo siguiente:

	hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)

La URL HTTP de la solicitud de envío del centro de notificación se anexa con una cadena de consulta de "prueba" como resultado.

##<a name="complete-tutorial"></a>Finalización del tutorial
Ahora puede completar el tutorial de introducción mediante el envío de notificaciones desde un back-end de Python.

Inicialice el cliente de Centros de notificaciones (reemplace la cadena de conexión y el nombre del centro tal como se indica en el [tutorial introductorio]):

	hub = NotificationHub("myConnectionString", "myNotificationHubName")

Después, agregue el código de envío dependiendo de la plataforma móvil de destino. Este ejemplo también agrega métodos de nivel superior para habilitar el envío de notificaciones basado en la plataforma como, por ejemplo, send\_windows\_notification para Windows; send\_apple\_notification (para Apple), etc.

### Tienda Windows y Windows Phone 8.1 (no Silverlight)

	wns_payload = """<toast><visual><binding template="ToastText01"><text id="1">Test</text></binding></visual></toast>"""
	hub.send_windows_notification(wns_payload)

### Silverlight para Windows Phone 8.0 y 8.1

	hub.send_mpns_notification(toast)

### iOS

	alert_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_apple_notification(alert_payload)

### Android
	gcm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_gcm_notification(gcm_payload)

### Kindle Fire
	adm_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_adm_notification(adm_payload)

### Baidu
	baidu_payload = {
	    'data':
	        {
	            'msg': 'Hello!'
	        }
	}
	hub.send_baidu_notification(baidu_payload)

La ejecución del código Python debe generar una notificación que se mostrará en el dispositivo de destino.

## Ejemplos:

### Habilitar la propiedad de depuración
Al habilitar el indicador de depuración al inicializar NotificationHub, se mostrará el volcado de respuesta y solicitud HTTP detallado, así como NotificationOutcome de la manera siguiente donde podrá identificar qué encabezados HTTP se pasan en la solicitud y respuesta HTTP se recibió desde el centro de notificaciones: ![][1]

El resultado detallado del centro de notificaciones se mostrará, por ejemplo,

- cuando el mensaje se envíe correctamente al servicio de notificaciones de inserción. 
	
		<Outcome>The Notification was successfully sent to the Push Notification System</Outcome>

- Si no se encuentra ningún destino para alguna notificación de inserción, probablemente se muestre lo siguiente en la respuesta (lo que indica que no se encontraron registros para entregar la notificación probablemente debido a que los registros tenían algunas etiquetas que no coincidentes)

		'<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'

### Difusión de notificación del sistema en Windows 

Observe que los encabezados se envían cuando se envía una notificación del sistema de difusión al cliente de Windows.

	hub.send_windows_notification(wns_payload)

![][2]

### Enviar una notificación que especifica una etiqueta (o expresión de etiqueta)

Observe que el encabezado HTTP de etiquetas se agrega a la solicitud HTTP (en el ejemplo siguiente, se envía la notificación solo a los registros con la carga "deportes").

	hub.send_windows_notification(wns_payload, "sports")

![][3]

### Enviar notificación que especifica varias etiquetas

Observe que el encabezado HTTP de etiquetas cambia cuando se envían varias etiquetas.
	
	tags = {'sports', 'politics'}
	hub.send_windows_notification(wns_payload, tags)

![][4]

### Notificación de plantilla

Observe que el encabezado HTTP de formato y el cuerpo de la carga se envían como parte del cuerpo de la solicitud HTTP:

**Cliente: plantilla registrada**

		var template =
		                @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
            
**Servidor: envío de carga**
		
		template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
		hub.send_template_notification(template_payload)

![][5]


## Pasos siguientes
En este tema hemos visto cómo crear a un cliente REST de Python sencillo para centros de notificaciones. Desde aquí puede:

* Descargar el [ejemplo de contenedor REST de Python] completo, que contiene el código anterior.
* Continuar aprendiendo sobre la característica de etiquetado de Centros de notificaciones en el [tutorial de noticias de última hora].
* Continuar aprendiendo acerca de la característica de plantillas de los Centros de notificaciones en el [tutorial de localización de noticias].

<!-- URLs -->
[ejemplo de contenedor REST de Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[ejemplo de contenedor de REST de Python]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[tutorial de introducción]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[tutorial introductorio]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[tutorial de noticias de última hora]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[tutorial de localización de noticias]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
 

<!---HONumber=AcomDC_0302_2016-->