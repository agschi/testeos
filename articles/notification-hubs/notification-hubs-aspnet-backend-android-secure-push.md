<properties
	pageTitle="Envío de notificaciones push seguras a los Centros de notificaciones de Azure"
	description="Obtenga información acerca de cómo enviar notificaciones de inserción seguras en una aplicación Android desde Azure. Ejemplos de código escritos en Java y C#."
	documentationCenter="android"
    keywords="push notification,push notifications,push messages,android push notifications"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="notification-hubs"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/15/2016" 
	ms.author="wesmc"/>

#Envío de notificaciones push seguras a los Centros de notificaciones de Azure

> [AZURE.SELECTOR]
- [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-secure-push.md)
- [iOS](notification-hubs-aspnet-backend-ios-secure-push.md)
- [Android](notification-hubs-aspnet-backend-android-secure-push.md)

##Información general

> [AZURE.IMPORTANT] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

La compatibilidad con las notificaciones push en Microsoft Azure le permite tener acceso a una infraestructura de mensajes multiplataforma y de escalamiento horizontal fácil de usar, que simplifica considerablemente la implementación de notificaciones push tanto en aplicaciones de consumidor como en aplicaciones empresariales para plataformas móviles.

Debido a restricciones reguladoras o de seguridad, algunas veces una aplicación podría querer incluir algo en la notificación que no se puede trasmitir a través de la infraestructura de las notificaciones de inserción estándar. En este tutorial se describe cómo lograr la misma experiencia enviando información importante a través de una conexión segura y autenticada entre el dispositivo Android y el back-end de la aplicación.

A un alto nivel, el flujo es el siguiente:

1. El back-end de la aplicación:
	- Almacena la carga segura en la base de datos back-end.
	- Envía el identificador de esta notificación al dispositivo Android (no se envía información segura).
2. La aplicación del dispositivo, cuando recibe la información:
	- El dispositivo Android entra en contacto con el back-end que solicita la carga segura.
	- La aplicación puede mostrar la carga como una notificación en el dispositivo.

Es importante tener en cuenta que en el flujo anterior (y en este tutorial), asumimos que el dispositivo almacena un token de autenticación localmente y, después, el usuario inicia sesión. Esto garantiza una experiencia sin ningún problema, ya que el dispositivo puede recuperar la carga segura de la notificación usando este token. Si la aplicación no almacena tokens de autenticación en el dispositivo, o si estos tokens pueden haber caducado, la aplicación del dispositivo, al recibir la notificación push, debe mostrar una notificación genérica pidiendo al usuario que inicie la aplicación. Después, la aplicación autentica al usuario y muestra la carga de la notificación.

Este tutorial muestra cómo enviar notificaciones push seguras. Se basa en el tutorial sobre [notificar a los usuarios](notification-hubs-aspnet-backend-android-notify-users.md), por lo que debe completar los pasos de ese tutorial primero si no lo ha hecho todavía.

> [AZURE.NOTE] En este tutorial se supone que se ha creado y configurado el Centro de notificaciones tal como se describe en [Introducción a los Centros de notificaciones (Android)](notification-hubs-android-get-started.md).

[AZURE.INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## Modificación del proyecto Android

Una vez modificado el back-end de la aplicación para enviar solamente el *identificador* de una notificación push, deberá modificar la aplicación Android para que administre dicha notificación y devuelva la llamada a su back-end para recuperar el mensaje seguro que se debe mostrar. Para lograr este objetivo, tiene que asegurarse de que la aplicación Android sabe cómo autenticarse a sí misma con el back-end cuando recibe las notificaciones de inserción.

A continuación, modificaremos el flujo de *inicio de sesión* para guardar el valor de encabezado de autenticación en las preferencias compartidas de la aplicación. Se pueden usar mecanismos similares para almacenar cualquier token de autenticación (por ejemplo tokens OAuth) que la aplicación tendrá que usar sin solicitar credenciales de usuario.

1. En el proyecto de la aplicación Android, agregue las siguientes constantes en la parte superior de la clase **MainActivity**:

		public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
		public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";

2. Todavía en la clase **MainActivity**, actualice el método `getAuthorizationHeader()` para que contenga el siguiente código:

		private String getAuthorizationHeader() throws UnsupportedEncodingException {
			EditText username = (EditText) findViewById(R.id.usernameText);
    		EditText password = (EditText) findViewById(R.id.passwordText);
    		String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
    		basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

    		SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

    		return basicAuthHeader;
		}

3. Agregue la siguientes instrucciones `import` en la parte superior del archivo **MainActivity**:

		import android.content.SharedPreferences;

Ahora cambiaremos el controlador al que se llama cuando se recibe la notificación.

4. En la clase **MyHandler**, cambie el método `OnReceive()` para que contenga:

		public void onReceive(Context context, Bundle bundle) {
	    	ctx = context;
	    	String secureMessageId = bundle.getString("secureId");
	    	retrieveNotification(secureMessageId);
		}

5. Después, agregue el método `retrieveNotification()`, reemplazando el marcador de posición `{back-end endpoint}` con el extremo back-end obtenido mientras se implementa su back-end:

		private void retrieveNotification(final String secureMessageId) {
			SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
    		final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

			new AsyncTask<Object, Object, Object>() {
				@Override
				protected Object doInBackground(Object... params) {
					try {
						HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
						request.addHeader("Authorization", "Basic "+authorizationHeader);
						HttpResponse response = new DefaultHttpClient().execute(request);
						if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
							Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
							throw new RuntimeException("Error retrieving secure notification");
						}
						String secureNotificationJSON = EntityUtils.toString(response.getEntity());
						JSONObject secureNotification = new JSONObject(secureNotificationJSON);
						sendNotification(secureNotification.getString("Payload"));
					} catch (Exception e) {
						Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
						return e;
					}
					return null;
				}
			}.execute(null, null, null);
		}


Este método llama al back-end de la aplicación para recuperar el contenido de la notificación usando las credenciales almacenadas en las preferencias compartidas y lo muestra como una notificación normal. El aspecto de la notificación para el usuario de la aplicación es exactamente el mismo que cualquier otra notificación de inserción.

Tenga en cuenta que es preferible administrar los casos de propiedad de encabezado de autenticación ausente o rechazo por el backend. La administración específica de estos casos depende principalmente de la experiencia del usuario de destino. Una opción es mostrar una notificación con un mensaje genérico para el usuario con el fin de que se autentique para recuperar la notificación real.

## Ejecución de la aplicación

Para ejecutar la aplicación, realice las siguientes tareas:

1. Asegúrese de que **AppBackend** se ha implementado en Azure. Si usa Visual Studio, ejecute la aplicación de API web **AppBackend**. Se mostrará una página web ASP.NET.

2. En Eclipse, ejecute la aplicación en un dispositivo Android físico o en el emulador.

3. En la interfaz de usuario de la aplicación Android, escriba un nombre de usuario y contraseña. Esta información puede ser cualquier cadena, pero deben tener el mismo valor.

4. En la interfaz de usuario de la aplicación Android, haga clic en **Log in** (Iniciar sesión). A continuación, haga clic en **Send push** (Enviar inserción).

<!---HONumber=AcomDC_0218_2016-->