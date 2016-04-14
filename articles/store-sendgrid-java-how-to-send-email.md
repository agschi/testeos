<properties 
	pageTitle="Uso del servicio de correo electrónico SendGrid (Java) | Microsoft Azure" 
	description="Obtenga información acerca de cómo enviar correo electrónico con el servicio de correo electrónico SendGrid en Azure. Ejemplos de código escritos en Java." 
	services="" 
	documentationCenter="java" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>
# Envío de correo electrónico con SendGrid desde Java

Esta guía describe cómo realizar tareas comunes de programación con el servicio de correo electrónico SendGrid en Azure. Los ejemplos están escritos en Java. Entre los escenarios descritos se incluyen **creación de correo electrónico**, **envío de correo electrónico**, **incorporación de archivos adjuntos**, **uso de filtros** y **actualización de propiedades**. Para obtener más información sobre SendGrid y el envío de correo electrónico, consulte la sección [Pasos siguientes](#next-steps).

## ¿Qué es el servicio de correo electrónico SendGrid?

SendGrid es un [servicio de correo electrónico basado en la nube] que proporciona un sistema fiable de [entrega de correos electrónicos transaccional], escalabilidad y análisis en tiempo real junto con API flexibles que facilitan la integración personalizada. Entre los escenarios de uso de SendGrid comunes se incluyen:

-   Envío automático de recibos a los clientes
-   Administración de listas de distribución para enviar a los clientes prospectos electrónicos y ofertas especiales cada mes
-   Recopilación de diversas métricas en tiempo real, como las direcciones de correo electrónico bloqueadas y la capacidad de respuesta del cliente.
-   Generación de informes para ayudar a identificar tendencias
-   Reenvío de consultas de los clientes
- Envío de notificaciones de correo electrónico desde su aplicación

Para obtener más información, consulte <http://sendgrid.com>.

## Creación de una cuenta de SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## Uso de las bibliotecas javax.mail

Obtenga las bibliotecas javax.mail, por ejemplo desde <http://www.oracle.com/technetwork/java/javamail> e impórtelas a su código. En un alto nivel, el proceso para utilizar la biblioteca javax.mail para enviar correo electrónico a través de SMTP es el siguiente:

1.  Especifique los valores de SMTP, incluido el servidor SMTP que, para SendGrid, es smtp.sendgrid.net.
    
```
        import java.util.Properties;
        import javax.activation.*;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
	       private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
	       private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
        
		   public static void main(String[] args) throws Exception{
         	  new MyEmailer().SendMail();
           }
        
		   public void SendMail() throws Exception
           {
              Properties properties = new Properties();
           	  properties.put("mail.transport.protocol", "smtp");
           	  properties.put("mail.smtp.host", SMTP_HOST_NAME);
           	  properties.put("mail.smtp.port", 587);
           	  properties.put("mail.smtp.auth", "true");
           	  // …
```

2.  Extienda la clase *javax.mail.Authenticator* y, en su implementación del método *getPasswordAuthentication*, devuelva su nombre de usuario y contraseña de SendGrid.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Cree una sesión de correo electrónico autenticado a través de un objeto *javax.mail.Session*.

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Cree su mensaje y asigne los valores **Para**, **De**, **Asunto** y los valores de contenido. Esto se muestra en la sección [Creación de un correo electrónico](#bkmk_HowToCreateEmail).
5.  Envíe el mensaje a través de un objeto *javax.mail.Transport*. Esto se muestra en la sección [Envío de un correo electrónico](Envío de un correo electrónico).

## Creación de un correo electrónico

A continuación se muestra cómo especificar valores para un correo electrónico.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
		"<p>Hello,</p>
		<p>Your Contoso order has <b>shipped</b>.</p>
		<p>Thank you,<br>John</br></p>",
		"text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## Envío de un correo electrónico

A continuación se muestra cómo enviar un correo electrónico.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## Incorporación de un archivo adjunto

El siguiente código muestra cómo agregar un archivo adjunto.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## Uso de filtros para habilitar pies de página, seguimiento y análisis

SendGrid proporciona funcionalidad de correo electrónico adicional mediante el uso de *filtros*. Estas configuraciones se pueden agregar a un mensaje de correo electrónico para permitir una funcionalidad específica, como habilitar el seguimiento de clics, el análisis de Google, el seguimiento de las suscripciones, etc. Si desea obtener una lista completa de los filtros, consulte [Filter Settings][].

-   El siguiente código muestra cómo insertar un filtro de pie de página que hace que aparezca texto HTML en la parte inferior del correo electrónico que se envía.

        message.addHeader("X-SMTPAPI", 
			"{"filters": 
			{"footer": 
			{"settings": 
        	{"enable":1,"text/html": 
			"<html><b>Thank you</b> for your business.</html>"}}}}");

-   Otro ejemplo de un filtro es el seguimiento de clics. Digamos que el texto de su correo electrónico contiene un hipervínculo, como el siguiente, y que quiere hacer un seguimiento del número de clics:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   Para permitir el seguimiento de los clics, utilice el siguiente código:

        message.addHeader("X-SMTPAPI", 
			"{"filters": 
			{"clicktrack": 
			{"settings": 
        	{"enable":1}}}}");

## Actualización de las propiedades del correo electrónico

Es posible sobrescribir algunas propiedades de correo electrónico con **set*Property*** o anexarlas con **add*Property***.

Por ejemplo, para especificar direcciones de respuesta en **ReplyTo**, use el siguiente código:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Para agregar a un destinatario **CC**, use el siguiente código:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## Uso de servicios adicionales de SendGrid

SendGrid ofrece API basadas en web que puede utilizar para aprovechar la funcionalidad adicional de SendGrid desde su aplicación de Azure. Para obtener toda la información al respecto, consulte la [Documentación sobre la API de SendGrid][].

## Pasos siguientes

Ahora que conoce los fundamentos del servicio de correo electrónico SendGrid, siga estos vínculos para obtener más información:

* Ejemplo que muestra cómo usar SendGrid en una implementación de Azure: [Envío de correo electrónico con SendGrid desde Java en una implementación de Azure](store-sendgrid-java-how-to-send-email-example.md)
* SDK de Java de SendGrid: <https://sendgrid.com/docs/Code_Examples/java.html>
* Documentación sobre la API de SendGrid: <https://sendgrid.com/docs/API_Reference/index.html>
* Oferta especial de SendGrid para clientes de Azure: <https://sendgrid.com/windowsazure.html>

  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Documentación sobre la API de SendGrid]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [servicio de correo electrónico basado en la nube]: https://sendgrid.com/email-solutions
  [entrega de correos electrónicos transaccional]: https://sendgrid.com/transactional-email

<!----HONumber=Oct15_HO3-->