<properties 
   pageTitle="Interfaz de usuario de Azure Mobile Engagement: configuración" 
   description="Obtenga información acerca de cómo administrar la configuración global de la aplicación mediante Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="11/29/2015"
   ms.author="piyushjo"/>

# Cómo administrar la configuración global de la aplicación

Las opciones del menú **Configuración** disponibles para una aplicación varían, dependiendo de la plataforma de la aplicación y de los permisos que se le hayan concedido para la aplicación. En la configuración se incluyen lo siguiente: Detalles, Proyectos, Inserción nativa, Velocidad de inserción, Etiquetas de información de la aplicación y Presión comercial. La opción de menú Etiquetas de información de la aplicación de la sección de Configuración puede administrarse mediante la aplicación (con el SDK) o mediante el back-end (con la API del dispositivo).


>[AZURE.NOTE]Muchas de las secciones de la interfaz de usuario del portal **Mobile Engagement** contienen el botón **MOSTRAR AYUDA**. Pulse este botón para obtener más información contextual sobre una sección.

## Detalles

Le permite cambiar el nombre y la descripción de la aplicación, ver el propietario de la aplicación y los permisos de rol.

La configuración de análisis permite ver o cambiar el día en que empieza la semana y el tiempo de retención en días.
 
  ![settings1][46]
 
## Proyectos

Le permite seleccionar todos los proyectos en los que desea que aparezca la aplicación.

También puede buscar un proyecto y ver el nombre, descripción, propietario y los permisos del rol de cualquier proyecto del que forme parte la aplicación.

Para obtener más información, consulte [Documentación de la interfaz de usuario - Inicio][Link 13].
 
  ![settings3][48]

## Inserción nativa:

Permite registrar un nuevo certificado o la eliminación de un certificado existente para usar con la inserción nativa. Inserción nativa permite a Azure Mobile Engagement insertar en su aplicación en cualquier momento, aunque no se esté ejecutando.

Después de proporcionar las credenciales o certificados para al menos un servicio de inserción nativa, se puede seleccionar "Cualquier hora" al crear campañas de cobertura y también usar el parámetro "notifier" en la API de INSERCIÓN.



### Servicio de notificaciones de inserción de Apple (APNS)

Para habilitar la inserción nativa mediante el servicio de notificación de inserción de Apple deberá registrar su certificado. Deberá especificar el tipo de certificado como de desarrollo (DEV) o de producción (PROD). A continuación, necesitará cargar su certificado y la contraseña.

Para obtener más información, consulte [Documentación del SDK - iOS - Cómo preparar la aplicación para las notificaciones de inserción de Apple][Link 5]
 
![settings4][49]
 
### Servicio de notificación de inserción de Windows (WPNS)

Para habilitar la inserción nativa mediante el servicio de notificación de Windows, debe proporcionar las credenciales de la aplicación. Necesitará el identificador de seguridad del paquete (SID) y la clave secreta.
 
![settings5][50]
 
### Mensajería en la nube de Google para Android (GCM)

Para habilitar la inserción nativa mediante GCM, deberá seguir las instrucciones de Google. A continuación, debe pegar una clave de API simple de servidor configurada sin restricciones de IP. Requiere la integración con el SDK de Android v1.12.0 +.

Para más información, consulte:

- [Documentación del SDK - Android - Cómo integrar GCM][Link 5]
- [Guía sobre GCM de Google Developers](http://developer.android.com/guide/google/gcm/gs.html)
 
### Mensajería de dispositivos de Amazon para Android (ADM)

Para habilitar la inserción nativa con ADM, debe proporcionar <OAuth credentials> de Amazon compuesta de un identificador de cliente y el secreto del cliente (requiere la integración con el SDK de Android v2.1.0+).

Para más información, consulte:

- [Documentación del SDK - Android - Cómo integrar ADM][Link 5]
- [Documentación sobre ADM para desarrolladores de Amazon](https://developer.amazon.com/sdk/adm/credentials.html#Getting)
 
![settings6][51]

## Velocidad de inserción

Muestra la velocidad de inserción actual de la aplicación y le permite definir la velocidad de inserción de la aplicación.
 
  ![settings7][52]

## Etiqueta de información de aplicación

![settings11][56]
  
## Presión comercial


![settings12][57]


## Consulte también

- [Conceptos][Link 6]
- [Guía de solución de problemas de servicios][Link 24]

 

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 

<!---HONumber=AcomDC_1203_2015-->