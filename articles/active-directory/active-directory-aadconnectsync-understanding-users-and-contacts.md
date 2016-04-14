<properties
	pageTitle="Sincronización de Azure AD Connect: descripción de usuarios y contactos | Microsoft Azure"
	description="Explica los usuarios y los contactos en Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: descripción de usuarios y contactos

Hay diversas razones por las que podría interesarle tener varios bosques de Active Directory y hay diversas topologías de implementación. Los modelos comunes incluyen una implementación cuenta-recurso y bosques sincronizados de lista global de direcciones tras una fusión y adquisición. Sin embargo, aunque existan modelos puros, los modelos híbridos también son comunes. La configuración predeterminada de Azure AD Connect no da por supuesto ningún modelo en particular, pero en función de cómo se haya seleccionado la coincidencia de usuarios en la guía de instalación, pueden observarse comportamientos diferentes.

En este tema, veremos cómo se comporta la configuración predeterminada en ciertas topologías. Además analizaremos la configuración, que puede examinarse mediante el Editor de reglas de sincronización.

Existen varias reglas generales que la configuración da por supuesto:

- Independientemente del orden en el que importemos los directorios de Active Directory de origen, el resultado final siempre debe ser el mismo.
- Una cuenta activa siempre aporta información de inicio de sesión, **userPrincipalName** y **sourceAnchor** incluidos.
- Una cuenta deshabilitada aportará userPrincipalName y sourceAnchor, a menos que sea un buzón vinculado si no hay ninguna cuenta activa a encontrar.
- Una cuenta con un buzón vinculado nunca se utilizará para userPrincipalName y sourceAnchor. Se da por supuesto que más adelante se encontrará una cuenta activa.
- Podría aprovisionarse un objeto de contacto en Azure AD como contacto o como usuario. No se sabe con seguridad hasta que se han procesado todos los bosques de Active Directory de origen.

## Contactos

Tras una fusión o una adquisición donde la solución GALSync actúa como puente entre dos o más bosques de Exchange, es habitual que los contactos representen a un usuario en un bosque diferente. El objeto de contacto siempre se une desde el espacio del conector al metaverso mediante el atributo de correo. Si ya existe un objeto de contacto o un objeto de usuario con la misma dirección de correo, los objetos se unen. Esto se configura en la regla **In from AD – Contact Join**. Existe también una regla llamada **In from AD – Contact Common** con un flujo de atributos al atributo de metaverso **sourceObjectType** con la constante **Contact**. Esta regla tiene una precedencia muy baja, por lo que si algún objeto de usuario se une al mismo objeto de metaverso, la regla **In from AD – User Common** aportará el valor User a este atributo. Con esta regla, este atributo tendrá el valor Contact si no se ha unido ningún usuario y el valor User si se ha encontrado por lo menos un usuario.

Para aprovisionar un objeto en Azure AD, la regla saliente **Out to AAD – Contact Join** creará un objeto de contacto si el atributo de metaverso **sourceObjectType** se establece en **Contact**. En cambio, si este atributo se establece en **User**, la regla **Out to AAD – User Join** creará un objeto de usuario. Es posible que un objeto se promueva de Contact a User si se importan o sincronizan más directorios de Active Directory de origen.

Por ejemplo, en una topología GALSync encontraremos objetos de contacto para todos los usuarios en el segundo bosque cuando importemos el primer bosque. Esto llevará a cabo nuevos objetos de contacto en el Conector AAD. Cuando más adelante importemos y sincronicemos el segundo bosque, encontraremos los usuarios reales y los uniremos a los objetos de metaverso existentes. Entonces eliminaremos el objeto de contacto en AAD y crearemos en su lugar un objeto de usuario nuevo.

Si en su topología los usuarios están representados como contactos, asegúrese de que selecciona la coincidencia de usuario en el atributo de correo en la guía de instalación. Si selecciona otra opción, tendrá una configuración que dependerá del orden. Los objetos de contacto siempre se unirán en el atributo de correo, mientras que los objetos de usuario solo se unirán en el atributo de correo si se seleccionó esta opción en la guía de instalación. Como resultado, puede tener dos objetos diferentes en el metaverso con el mismo atributo de correo si el objeto de contacto se importó antes del objeto de usuario. Durante la exportación a Azure AD, se producirá un error. Este comportamiento se debe a cuestiones de diseño, e indica que los datos son incorrectos o que la topología no se identificó correctamente durante la instalación.

## Cuentas deshabilitadas

Las cuentas deshabilitadas se sincronizan también en Azure AD. Las cuentas deshabilitadas suelen representar recursos en Exchange, por ejemplo salas de conferencias. La excepción son los usuarios con un buzón vinculado; como ya se indicó anteriormente, nunca aprovisionarán una cuenta en Azure AD.

Esto se basa en la suposición de que si se encuentra una cuenta de usuario deshabilitada, más adelante no se encontrará otra cuenta activa, y el objeto se aprovisiona en Azure AD con los valores encontrados para userPrincipalName y sourceAnchor. En caso de que otra cuenta activa se una al mismo objeto de metaverso, se usarán sus valores para userPrincipalName y sourceAnchor.

## Cambiar sourceAnchor

Si se ha exportado un objeto a Azure AD, ya no se permite cambiar el valor de sourceAnchor. Una vez exportado un objeto, el atributo de metaverso **cloudSourceAnchor** se establece con el valor de **sourceAnchor** que acepta Azure AD. Si se cambia **sourceAnchor** y no coincide con **cloudSourceAnchor**, la regla **Out to AAD – User Join** producirá el error **El atributo sourceAnchor ha cambiado**. En este caso habrá que corregir la configuración o los datos, de modo que el mismo valor de sourceAnchor esté presente en el metaverso para que el objeto pueda sincronizarse de nuevo.

## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0218_2016-->