<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C | Microsoft Azure"
	description="Creación de una aplicación de escritorio de Windows que incluye tareas de registro, inicio de sesión y administración de perfiles mediante Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Versión preliminar de Azure AD B2C: creación de una aplicación de escritorio de Windows


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Con Azure Active Directory (Azure AD) B2C, puede agregar eficaces características de administración de identidades autoservicio a sus aplicaciones de escritorio en pocos pasos. En este artículo se le mostrará cómo crear una aplicación de "lista de tareas pendientes" de Windows Presentation Foundation (WPF) para .NET que incluya tareas de registro, inicio de sesión y administración de perfiles de usuarios. La aplicación permitirá registrarse e iniciar sesión mediante un nombre de usuario o un correo electrónico. También será posible registrarse e iniciar sesión con cuentas sociales, como Facebook y Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, las aplicaciones, los grupos, etc. Si aún no tiene uno, [cree un directorio B2C](active-directory-b2c-get-started.md) antes de continuar con esta guía.

## Creación de una aplicación

A continuación, debe crear una aplicación en su directorio B2C. Esto proporciona a Azure AD la información que necesita para comunicarse de forma segura con la aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de:

- Incluir un **cliente nativo** en la aplicación.
- Copiar el **URI de redireccionamiento** `urn:ietf:wg:oauth:2.0:oob`. Es la dirección URL predeterminada para este ejemplo de código.
- Copiar el **identificador de aplicación** asignado a la aplicación. Lo necesitará más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Crear sus directivas
LA VELA AMEO LA VELA AMEO AMEOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO AMEOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
En Azure AD B2C, cada experiencia del usuario se define mediante una [directiva](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y edición de perfil. Tendrá que crear una directiva de cada tipo, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Cuando cree las tres directivas, asegúrese de:

- Elegir **Registro con id. de usuario** o **Registro con correo electrónico** en la hoja de proveedores de identidades.
- Seleccionar **Nombre para mostrar** y otros atributos de registro en la directiva de registro.
- Elegir las notificaciones **Nombre para mostrar** e **Id. de objeto** como notificaciones de aplicación en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará estos nombres de directiva más adelante.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando haya creado correctamente las tres directivas, estará listo para crear su aplicación.

## Descargar el código

El código de este tutorial [se mantiene en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). También puede clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

La aplicación completada también estará [disponible como archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo .sln de Visual Studio para empezar. Hay dos proyectos en la solución: un proyecto `TaskClient` y un proyecto `TaskService`. `TaskClient` es la aplicación de escritorio de WPF con la que el usuario interactúa. `TaskService` es la API web del back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario. En este caso, tanto `TaskClient` como `TaskService` se representan mediante un único identificador de aplicación, porque conforman una aplicación lógica.

## Configurar el servicio de tarea

Cuando `TaskService` recibe una solicitud de `TaskClient`, busca un token de acceso válido para autenticar la solicitud. Para validar el token de acceso, debe proporcionar a `TaskService` información sobre la aplicación. En el proyecto `TaskService`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Para saber de qué forma una API web autentica de forma segura las solicitudes con Azure AD B2C, consulte nuestro [artículo de introducción a la API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Ejecución de directivas
Cuando `TaskService` esté listo para autenticar las solicitudes, puede implementar `TaskClient`. La aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación de HTTP que especifican la directiva que desean ejecutar como parte de la solicitud. Para aplicaciones de escritorio .NET, puede usar la Biblioteca de autenticación de Active Directory (AAL) para enviar mensajes de autenticación de OAuth 2.0, ejecutar directivas y obtener tokens para llamar a las API web.

### Instalación de AAL
Agregue AAL al proyecto `TaskClient` mediante la Consola del Administrador de paquetes de Visual Studio.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### Escribir los detalles de B2C
Abra el archivo `Globals.cs` y reemplace cada uno de los valores de propiedad por lo suyos propios. Esta clase se usa en todo `TaskClient` para hacer referencia a valores de uso frecuente.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### Creación de una clase AuthenticationContext
La clase principal de ADAL es `AuthenticationContext`. Esta clase representa la conexión de su aplicación con su directorio B2C. Cuando se inicie la aplicación, cree una instancia de `AuthenticationContext` en `MainWindow.xaml.cs`. Esta puede usarse en toda la ventana.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### Inicio de un flujo de registro
Cuando un usuario decide registrarse, quiere iniciar un flujo de registro que usa la directiva de registro que ha creado. Con ADAL, llama simplemente a `authContext.AcquireTokenAsync(...)`. Los parámetros que pasan a `AcquireTokenAsync(...)` determinan el token que recibe, la directiva usada en la solicitud de autenticación, etc.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}

			MessageBox.Show(message);
		}

		return;
	}
}
```

### Inicio de un flujo de inicio de sesión
Puede iniciar un flujo de inicio de sesión de la misma manera que inicia un flujo de registro. Cuando un usuario inicia sesión, realiza la misma llamada a ADAL, pero esta vez usando la directiva de inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### Inicio de un flujo de edición de perfiles
De nuevo, puede ejecutar una directiva de edición de perfiles de la misma manera:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

En todos estos casos, ADAL devuelve un token en `AuthenticationResult` o produce una excepción. Cada vez que reciba un token de ADAL, puede usar el objeto `AuthenticationResult.UserInfo` para actualizar los datos de usuario en la aplicación, como la interfaz de usuario. ADAL también almacena en caché el token para usarlo en otras partes de la aplicación.

## Llamar a API
Ya hemos usado ADAL para ejecutar directivas y obtener tokens. En muchos casos, sin embargo, le interesará comprobar si existe un token almacenado en caché sin ejecutar una directiva. Uno de estos casos se da cuando la aplicación intenta obtener la lista de tareas pendientes de un usuario de `TaskService`. Puede usar el mismo método `authContext.AcquireTokenAsync(...)` para ello, de nuevo usando `clientId` como parámetro de ámbito, pero esta vez también con `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}
			MessageBox.Show(message);
		}

		return;
	}
	...
```

Cuando la llamada a `AcquireTokenAsync(...)` se realiza correctamente y se encuentra un token en la memoria caché, puede agregarlo al encabezado `Authorization` de la solicitud HTTP. De este modo, `TaskService` puede autenticar la solicitud para leer la lista de tareas pendientes del usuario:

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

Puede usar este mismo patrón cada vez que desee comprobar la caché de tokens para ver los tokens sin solicitar a un usuario que inicie sesión. Por ejemplo, cuando se inicia la aplicación, es aconsejable comprobar todos los token existentes en `FileCache`. De este modo, la sesión del usuario de inicio de sesión se mantiene cada vez que la aplicación se ejecuta. Puede ver el mismo código en el caso `OnInitialized` de `MainWindow`. `OnInitialized` administra este caso de primera ejecución.

## Cierre de la sesión del usuario
Puede usar ADAL para finalizar la sesión de un usuario en la aplicación cuando el usuario selecciona **Cerrar sesión**. Con ADAL, esto se logra borrando todos los tokens de la caché de tokens:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## Ejecutar la aplicación de ejemplo

Por último, compile y ejecute `TaskClient` y `TaskService`. Regístrese en la aplicación con una dirección de correo electrónico o un nombre de usuario. Cierre la sesión y vuelva a iniciarla como el mismo usuario. Edite el perfil de ese usuario. Cierre la sesión y regístrese con otro usuario diferente.

## Agregar IDP sociales

Actualmente, la aplicación admite solo registros e inicios de sesión de usuarios mediante **cuentas locales**. Se trata de cuentas almacenadas en el directorio B2C que utilizan un nombre de usuario y una contraseña. Con Azure AD B2C, puede agregar compatibilidad con otros proveedores de identidades (IDP) sin cambiar el código.

Para agregar proveedores de identidades sociales a su aplicación, comience siguiendo las instrucciones detalladas en estos artículos. Para cada proveedor de identidades que desee admitir, necesitará registrar una aplicación en ese sistema y obtener un identificador de cliente.

- [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md)

Después de agregar los proveedores de identidades a su directorio B2C, tendrá que editar cada una de las tres directivas para incluir los nuevos proveedores de identidades, como se describe en el [artículo de referencia de las directivas](active-directory-b2c-reference-policies.md). Después de guardar las directivas, vuelva a ejecutar la aplicación. Debería ver los proveedores de identidades nuevos agregados como opciones de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con las directivas y observar los efectos en la aplicación de ejemplo. Agregue o quite proveedores de identidades, manipule notificaciones de aplicación o cambie atributos de registro. Experimente para ver cómo se combinan directivas, solicitudes de autenticación y ADAL.

Como referencia, el ejemplo completo [se proporciona como un archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). También puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->