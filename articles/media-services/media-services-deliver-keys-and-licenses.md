<properties 
	pageTitle="Uso de Servicios multimedia de Azure para entregar licencias de DRM a claves de AES" 
	description="Este artículo describe cómo puede utilizar Servicios de multimedia de Azure (AMS) para proporcionar licencias de PlayReady y Widevine y claves de AES, pero realizar el resto (codificación, cifrado y streaming) con los servidores locales." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="juliako"/>


#Uso de Servicios multimedia de Azure para entregar licencias de DRM a claves de AES

Servicios de multimedia de Azure (AMS) le permite introducir, codificar, agregar protección de contenido y transmitir el contenido (consulte [este](media-services-protect-with-drm.md) artículo para ver los detalles). Sin embargo, hay clientes que solo desean usar AMS para entregar licencias y claves, y realizar la codificación, el cifrado y el streaming mediante sus servidores locales. Este artículo describe cómo puede usar AMS para entregar licencias de PlayReady y Widevine, pero realizar el resto con los servidores locales.


## Información general

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady y Widevine y claves de AES-128. Servicios multimedia también proporciona API que permiten configurar los derechos y las restricciones que desee aplicar en tiempo de ejecución de DRM cuando un usuario reproduzca contenido protegido de DRM. Cuando un usuario solicita contenido protegido, la aplicación del reproductor solicitará una licencia del servicio de licencias de AMS. El servicio de licencias de AMS emitirá la licencia al reproductor, si está autorizado. Las licencias de PlayReady y Widevine contienen la clave de descifrado que puede usar el reproductor cliente para descifrar y transmitir el contenido.

Servicios multimedia admite varias formas de autorizar a los usuarios que realizan solicitudes de licencias o claves. Puede configurar la directiva de autorización de claves de acceso, que podría tener una o más restricciones: abrir o restricción de token. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT).


El siguiente diagrama muestra los pasos principales que debe llevar a cabo con el fin de usar AMS para entregar licencias de PlayReady y Widevine, pero realizar el resto con los servidores locales.

![Protección con PlayReady](./media/media-services-deliver-keys-and-licenses/media-services-diagram1.png)

##Descarga de un ejemplo

Puede descargar el ejemplo descrito en este artículo [aquí](https://github.com/Azure/media-services-dotnet-deliver-drm-licenses).

##Ejemplo de código .NET

El ejemplo de código de este tema muestra cómo crear una clave de contenido común y obtener direcciones URL de adquisición de licencias PlayReady o Widevine. Necesitará obtener los siguientes fragmentos de información de AMS y configurar el servidor local: **clave de contenido**, **identificador de clave**, **URL de adquisición de licencias**. Una vez que configure su servidor local, podría transmitir desde su propio servidor de streaming. Puesto que la transmisión cifrada apunta al servidor de licencias de AMS, el reproductor solicitará una licencia de AMS. Si elige la autenticación de token, el servidor de licencias de AMS validará el token enviado a través de HTTPS y, si es válido, devolverá la licencia a su reproductor. (El ejemplo de código solo muestra cómo crear una clave de contenido común y obtener direcciones URL de adquisición de licencias PlayReady o Widevine. Si desea proporcionar las claves de AES-128, debe crear una clave de contenido del sobre y obtener una dirección URL de adquisición de claves; en [este](media-services-protect-with-aes128.md) artículo se muestra cómo hacerlo).
	
	
	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.IO;
	using System.Linq;
	using System.Threading;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
	using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
	using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
	using Newtonsoft.Json;
	
	
	namespace DeliverDRMLicenses
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        private static readonly Uri _sampleIssuer =
	            new Uri(ConfigurationManager.AppSettings["Issuer"]);
	        private static readonly Uri _sampleAudience =
	            new Uri(ConfigurationManager.AppSettings["Audience"]);
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            bool tokenRestriction = true;
	            string tokenTemplateString = null;
	
	
	            IContentKey key = CreateCommonTypeContentKey();
	
	            // Print out the key ID and Key in base64 string format
	            Console.WriteLine("Created key {0} with key value {1} ", 
	                key.Id, System.Convert.ToBase64String(key.GetClearKeyValue()));
	
	            Console.WriteLine("PlayReady License Key delivery URL: {0}", 
	                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
	
	            Console.WriteLine("Widevine License Key delivery URL: {0}",
	                key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine));
	
	            if (tokenRestriction)
	                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
	            else
	                AddOpenAuthorizationPolicy(key);
	
	            Console.WriteLine("Added authorization policy: {0}", 
	                key.AuthorizationPolicyId);
	            Console.WriteLine();
	            Console.ReadLine();
	        }
	
	        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
	        {
	
	            // Create ContentKeyAuthorizationPolicy with Open restrictions 
	            // and create authorization policy          
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = 
	                new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction
	                {
	                    Name = "Open",
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
	                    Requirements = null
	                }
	            };
	
	            // Configure PlayReady and Widevine license templates.
	            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, PlayReadyLicenseTemplate);
	
	            IContentKeyAuthorizationPolicyOption WidevinePolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("",
	                    ContentKeyDeliveryType.Widevine,
	                    restrictions, WidevineLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with no restrictions").
	                        Result;
	
	
	            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
	            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
	            // Associate the content key authorization policy with the content key.
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	        }
	
	        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
	        {
	            string tokenTemplateString = GenerateTokenRequirements();
	
	            List<ContentKeyAuthorizationPolicyRestriction> restrictions = 
	                new List<ContentKeyAuthorizationPolicyRestriction>
	            {
	                new ContentKeyAuthorizationPolicyRestriction
	                {
	                    Name = "Token Authorization Policy",
	                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
	                    Requirements = tokenTemplateString,
	                }
	            };
	
	            // Configure PlayReady and Widevine license templates.
	            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();
	
	            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();
	
	            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	                    ContentKeyDeliveryType.PlayReadyLicense,
	                        restrictions, PlayReadyLicenseTemplate);
	
	            IContentKeyAuthorizationPolicyOption WidevinePolicy =
	                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
	                    ContentKeyDeliveryType.Widevine,
	                        restrictions, WidevineLicenseTemplate);
	
	            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
	                        ContentKeyAuthorizationPolicies.
	                        CreateAsync("Deliver Common Content Key with token restrictions").
	                        Result;
	
	            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
	            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
	
	            // Associate the content key authorization policy with the content key
	            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
	            contentKey = contentKey.UpdateAsync().Result;
	
	            return tokenTemplateString;
	        }

	        static private string GenerateTokenRequirements()
	        {
	            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
	
	            template.PrimaryVerificationKey = new SymmetricVerificationKey();
	            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
	            template.Audience = _sampleAudience.ToString();
	            template.Issuer = _sampleIssuer.ToString();
	            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
	
	            return TokenRestrictionTemplateSerializer.Serialize(template);
	        }

	        static private string ConfigurePlayReadyLicenseTemplate()
	        {
	            // The following code configures PlayReady License Template using .NET classes
	            // and returns the XML string.
	
	            //The PlayReadyLicenseResponseTemplate class represents the template 
	            //for the response sent back to the end user. 
	            //It contains a field for a custom data string between the license server 
	            //and the application (may be useful for custom app logic) 
	            //as well as a list of one or more license templates.
	
	            PlayReadyLicenseResponseTemplate responseTemplate = 
	                new PlayReadyLicenseResponseTemplate();
	
	            // The PlayReadyLicenseTemplate class represents a license template 
	            // for creating PlayReady licenses
	            // to be returned to the end users. 
	            // It contains the data on the content key in the license 
	            // and any rights or restrictions to be 
	            // enforced by the PlayReady DRM runtime when using the content key.
	            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
	
	            // Configure whether the license is persistent 
	            // (saved in persistent storage on the client) 
	            // or non-persistent (only held in memory while the player is using the license).  
	            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;
	
	            // AllowTestDevices controls whether test devices can use the license or not.  
	            // If true, the MinimumSecurityLevel property of the license
	            // is set to 150.  If false (the default), 
	            // the MinimumSecurityLevel property of the license is set to 2000.
	            licenseTemplate.AllowTestDevices = true;
	
	            // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
	            // It grants the user the ability to playback the content subject to the zero or more restrictions 
	            // configured in the license and on the PlayRight itself (for playback specific policy). 
	            // Much of the policy on the PlayRight has to do with output restrictions 
	            // which control the types of outputs that the content can be played over and 
	            // any restrictions that must be put in place when using a given output.
	            // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
	            //then the DRM runtime will only allow the video to be displayed over digital outputs 
	            //(analog video outputs won’t be allowed to pass the content).
	
	            // IMPORTANT: These types of restrictions can be very powerful 
	            // but can also affect the consumer experience. 
	            // If the output protections are configured too restrictive, 
	            // the content might be unplayable on some clients. 
	            // For more information, see the PlayReady Compliance Rules document.
	
	            // For example:
	            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);
	
	            responseTemplate.LicenseTemplates.Add(licenseTemplate);
	
	            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
	        }
	
	
	        private static string ConfigureWidevineLicenseTemplate()
	        {
	            var template = new WidevineMessage
	            {
	                allowed_track_types = AllowedTrackTypes.SD_HD,
	                content_key_specs = new[]
	                {
	                    new ContentKeySpecs
	                    {
	                        required_output_protection = 
	                            new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
	                        security_level = 1,
	                        track_type = "SD"
	                    }
	                },
	                policy_overrides = new
	                {
	                    can_play = true,
	                    can_persist = true,
	                    can_renew = false
	                }
	            };
	
	            string configuration = JsonConvert.SerializeObject(template);
	            return configuration;
	        }
	
	
	        static public IContentKey CreateCommonTypeContentKey()
	        {
	            // Create envelope encryption content key
	            Guid keyId = Guid.NewGuid();
	            byte[] contentKey = GetRandomBuffer(16);
	
	            IContentKey key = _context.ContentKeys.Create(
	                                    keyId,
	                                    contentKey,
	                                    "ContentKey",
	                                    ContentKeyType.CommonEncryption);
	
	            return key;
	        }
	
	
	
	        static private byte[] GetRandomBuffer(int length)
	        {
	            var returnValue = new byte[length];
	
	            using (var rng =
	                new System.Security.Cryptography.RNGCryptoServiceProvider())
	            {
	                rng.GetBytes(returnValue);
	            }
	
	            return returnValue;
	        }
	
	
	    }
	}
	

##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##Consulte también

[Uso de cifrado dinámico común de PlayReady o Widevine](media-services-protect-with-drm.md)

[Uso del cifrado dinámico AES-128 y del servicio de entrega de claves](media-services-protect-with-aes128.md)

[Uso de partners para entregar licencias de Widevine a Servicios multimedia de Azure](media-services-licenses-partner-integration.md)

<!---HONumber=AcomDC_0302_2016-->