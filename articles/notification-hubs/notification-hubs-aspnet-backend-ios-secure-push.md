<properties
	pageTitle="Inserción segura de los Centros de notificaciones de Azure"
	description="Obtenga información acerca de cómo enviar notificaciones de inserción seguras a una aplicación iOS desde Azure. Ejemplos de código escritos en Objective-C y C#."
	documentationCenter="ios"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="wesmc"/>

#Inserción segura de los Centros de notificaciones de Azure

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-secure-push.md)
- [iOS](notification-hubs-aspnet-backend-ios-secure-push.md)
- [Android](notification-hubs-aspnet-backend-android-secure-push.md)


##Información general

La compatibilidad con las notificaciones push en Microsoft Azure le permite tener acceso a una infraestructura multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones push tanto en aplicaciones de consumidor como en aplicaciones empresariales para plataformas móviles.

Debido a restricciones reguladoras o de seguridad, algunas veces una aplicación podría querer incluir algo en la notificación que no se puede trasmitir a través de la infraestructura de las notificaciones de inserción estándar. En este tutorial se describe cómo lograr la misma experiencia enviando información importante a través de una conexión segura y autenticada entre el dispositivo cliente y el back-end de la aplicación.

A un alto nivel, el flujo es el siguiente:

1. El back-end de la aplicación:
	- Almacena la carga segura en la base de datos back-end.
	- Envía el identificador de esta notificación al dispositivo (no se envía información segura).
2. La aplicación del dispositivo, cuando recibe la información:
	- El dispositivo entra en contacto con el back-end que solicita la carga segura.
	- La aplicación puede mostrar la carga como una notificación en el dispositivo.

Es importante tener en cuenta que en el flujo anterior (y en este tutorial), asumimos que el dispositivo almacena un token de autenticación localmente y, después, el usuario inicia sesión. Esto garantiza una experiencia sin ningún problema, ya que el dispositivo puede recuperar la carga segura de la notificación usando este token. Si la aplicación no almacena tokens de autenticación en el dispositivo, o si estos tokens han expirado, la aplicación del dispositivo, al recibir la notificación, debe mostrar una notificación genérica pidiendo al usuario que inicie la aplicación. Después, la aplicación autentica al usuario y muestra la carga de la notificación.

Este tutorial Inserción segura muestra cómo enviar una notificación de inserción de forma segura. El tutorial se basa en el tutorial [Notificar a los usuarios](notification-hubs-aspnet-backend-ios-notify-users.md), por lo que debe completar los pasos de ese tutorial primero.

> [AZURE.NOTE] Este tutorial asume que ha creado y configurado el centro de notificaciones tal como se describe en [Introducción a los Centros de notificaciones (iOS)](notification-hubs-ios-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificación del proyecto iOS

Una vez modificado el back-end de la aplicación para enviar solamente el *id* de una notificación, deberá modificar la aplicación iOS para que administre dicha notificación y devuelva la llamada a su back-end para recuperar el mensaje seguro que se debe mostrar.

Para lograr este objetivo, tenemos que escribir la lógica para recuperar el contenido seguro del back-end de la aplicación.

1. En **AppDelegate.m**, asegúrese de que la aplicación se registra para notificaciones silenciosas de manera a procesar el identificador de notificación enviado desde el back-end. Agregue la opción **UIRemoteNotificationTypeNewsstandContentAvailability** en didFinishLaunchingWithOptions:

		[[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];

2. En su **AppDelegate.m**, agregue una sección de implementación en la parte superior con la siguiente declaración:

		@interface AppDelegate ()
		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		@end

3. Después, en la sección de implementación, agregue el siguiente código, sustituyendo el marcador de posición `{back-end endpoint}` por el extremo para el back-end obtenido anteriormente:

```
		NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

		- (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
		{
		    // check if authenticated
		    ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
		    NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
		    if (!authenticationHeader) return;


		    NSURLSession* session = [NSURLSession
		                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
		                             delegate:nil
		                             delegateQueue:nil];


		    NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
		    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
		    [request setHTTPMethod:@"GET"];
		    NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
		    [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

		    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
		        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
		        if (!error && httpResponse.statusCode == 200)
		        {
		            NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

		            NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

		            completion([json objectForKey:@"Payload"], nil);
		        }
		        else
		        {
		            NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
		            if (error)
		                completion(nil, error);
		            else {
		                completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
		            }
		        }
		    }];
		    [dataTask resume];
		}
```

	This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

4. Ahora tenemos que administrar la notificación entrante y usar el método anterior para recuperar el contenido para mostrar. Primero, tenemos que habilitar la aplicación iOS para que se ejecute en segundo plano cuando reciba una notificación de inserción. En **XCode**, seleccione el proyecto de aplicación en el panel izquierdo y, a continuación, haga clic en el destino de la aplicación principal en la sección **Targets** (Destinos) del panel central.

5. A continuación, haga clic en la pestaña **Capabilities** (Capacidades) situada en la parte superior del panel central y active la casilla **Remote Notifications** (Notificaciones remotas).

	![][IOS1]


6. En **AppDelegate.m**, agregue el método siguiente para administrar las notificaciones de inserción:

		-(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
		{
		    NSLog(@"%@", userInfo);

		    [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
		        if (!error) {
		            // show local notification
		            UILocalNotification* localNotification = [[UILocalNotification alloc] init];
		            localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
		            localNotification.alertBody = payload;
		            localNotification.timeZone = [NSTimeZone defaultTimeZone];
		            [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

		            completionHandler(UIBackgroundFetchResultNewData);
		        } else {
		            completionHandler(UIBackgroundFetchResultFailed);
		        }
		    }];

		}

	Tenga en cuenta que es preferible administrar los casos de propiedad de encabezado de autenticación ausente o rechazo por el backend. La administración específica de estos casos depende principalmente de la experiencia del usuario de destino. Una opción es mostrar una notificación con un mensaje genérico para el usuario con el fin de que se autentique para recuperar la notificación real.

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1. En XCode, ejecute la aplicación en un dispositivo iOS físico (las notificaciones de inserción no funcionarán en el simulador).

2. En la interfaz de usuario de la aplicación iOS, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

3. En la interfaz de usuario de la aplicación iOS, haga clic en **Log in** (Iniciar sesión). A continuación, haga clic en **Send push** (Enviar inserción). Debe ver la notificación segura mostrada en el centro notificaciones.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png

<!---HONumber=AcomDC_0302_2016-->