<properties 
	pageTitle="Store-sendgrid-Java-How-to-Send-email-example" 
	description="Envío de correo electrónico con SendGrid desde Java en una implementación de Azure" 
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
	ms.author="vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com"/>

# Envío de correo electrónico con SendGrid desde Java en una implementación de Azure

En el ejemplo siguiente se muestra cómo puede utilizar SendGrid para enviar correos electrónicos desde una página web hospedada en Azure. La aplicación resultante solicitará al usuario valores de correo electrónico, tal como se muestra en la siguiente captura de pantalla.

![Formulario de correo electrónico][emailform]

El correo electrónico resultante debería ser similar a la siguiente captura de pantalla.

![Mensaje de correo electrónico][emailsent]

Tendrá que hacer lo siguiente para utilizar el código de este tema:

1. Obtenga los JAR javax.mail, por ejemplo, desde <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Agregue los JAR a la ruta de acceso de la compilación Java.
3. Si está utilizando Eclipse para crear esta aplicación Java, puede incluir las bibliotecas SendGrid en el archivo de implementación de aplicación (WAR) utilizando la característica de ensamblado de implementación de Eclipse. Si no está utilizando Eclipse para crear esta aplicación Java, asegúrese de que las bibliotecas se incluyen en el mismo rol de Azure que la aplicación Java y que se agregan a la ruta de acceso de clase de la aplicación.


También debe tener su propio nombre de usuario y contraseña de SendGrid para poder enviar el correo electrónico. Para comenzar con SendGrid, consulte [Envío de correo electrónico con SendGrid desde Java](store-sendgrid-java-how-to-send-email.md).

Además, se recomienda estar familiarizado con la información que encontrará en [Creación de una aplicación Hello World para Azure en Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944) o con otras técnicas para hospedar aplicaciones Java en Azure si no está usando Eclipse.

## Creación de un formulario web para el envío del correo electrónico

El código siguiente muestra cómo crear un formulario web para recuperar datos de usuario para el envío de correo electrónico. Para los fines de este contenido, el archivo JSP se denomina **emailform.jsp**.

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email form</title>
	</head>
	<body>
	 <p>Fill in all fields and click <b>Send this email</b>.</p>
	 <br/>
	  <form action="sendemail.jsp" method="post">
	   <table>
	     <tr>
	       <td>To:</td>
	       <td><input type="text" size=50 name="emailTo">
	       </td>
	     </tr>
	     <tr>
	       <td>From:</td>
	       <td><input type="text" size=50 name="emailFrom">
	       </td>
	     </tr>
	     <tr>
	       <td>Subject:</td>
	       <td><input type="text" size=100 name="emailSubject" value="My email subject">
	       </td>
	     </tr>
	     <tr>
	       <td>Text:</td>
	       <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
	       </td>
	     </tr>
	     <tr>
	       <td>SendGrid user name:</td>
	       <td><input type="text" name="sendGridUser">
	       </td>
	     </tr>
	     <tr>
	       <td>SendGrid password:</td>
	       <td><input type="password" name="sendGridPassword">
	       </td>
	     </tr>
	     <tr>
	       <td colspan=2><input type="submit" value="Send this email">
	       </td>
	     </tr>
	   </table>
	 </form>
	 <br/>
	</body>
	</html>

## Creación del código para enviar el correo electrónico

El código siguiente, que se llama cuando completa el formulario en emailform.jsp, crea el mensaje de correo electrónico y lo envía. Para los fines de este contenido, el archivo JSP se denomina **sendemail.jsp**.

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email processing happens here</title>
	</head>
	<body>
	    <b>This is my send mail page.</b><p/>
	 <%
	 
	 final String sendGridUser = request.getParameter("sendGridUser");
	 final String sendGridPassword = request.getParameter("sendGridPassword");
	 
	 class SMTPAuthenticator extends Authenticator
	 {
	   public PasswordAuthentication getPasswordAuthentication()
	   {
	        String username = sendGridUser;
	        String password = sendGridPassword;
	      
	        return new PasswordAuthentication(username, password);   
	   }
	 }
	 try
	 {
	     
	     // The SendGrid SMTP server.
	     String SMTP_HOST_NAME = "smtp.sendgrid.net";
	
	     Properties properties;
	    
	     properties = new Properties();
	     
	     // Specify SMTP values.
	     properties.put("mail.transport.protocol", "smtp");
	     properties.put("mail.smtp.host", SMTP_HOST_NAME);
	     properties.put("mail.smtp.port", 587);
	     properties.put("mail.smtp.auth", "true");
	     
	     // Display the email fields entered by the user. 
	     out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
	     out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
	     out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
	     out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
	
	     // Create the authenticator object.
	     Authenticator authenticator = new SMTPAuthenticator();
	     
	     // Create the mail session object.
	     Session mailSession;
	     mailSession = Session.getDefaultInstance(properties, authenticator);
	     
	     // Display debug information to stdout, useful when using the
	     // compute emulator during development.
	     mailSession.setDebug(true);
	
	     // Create the message and message part objects.
	     MimeMessage message;
	     Multipart multipart;
	     MimeBodyPart messagePart; 
	     
	     message = new MimeMessage(mailSession);
	     
	     multipart = new MimeMultipart("alternative");
	     messagePart = new MimeBodyPart();
	     messagePart.setContent(request.getParameter("emailText"), "text/html");
	     multipart.addBodyPart(messagePart);            
	
	     // Specify the email To, From, Subject and Content. 
	     message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
	     message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
	     message.setSubject(request.getParameter("emailSubject")); 
	     message.setContent(multipart);
	     
	     // Uncomment the following if you want to add a footer.
	     // message.addHeader("X-SMTPAPI", "{"filters": {"footer": {"settings": {"enable":1,"text/html": "<html>This is my <b>email footer</b>.</html>"}}}}");
	
	     // Uncomment the following if you want to enable click tracking.
	     // message.addHeader("X-SMTPAPI", "{"filters": {"clicktrack": {"settings": {"enable":1}}}}");
	     
	     Transport transport;
	     transport = mailSession.getTransport();
	     // Connect the transport object.
	     transport.connect();
	     // Send the message.
	     transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
	     // Close the connection.
	     transport.close();
	 
	    out.println("<p>Email processing completed.</p>");
	     
	 }
	 catch (Exception e)
	 {
	     out.println("<p>Exception encountered: " + 
	                        e.getMessage()     +
	                        "</p>");   
	 }
	%>
	
	</body>
	</html>

Además de enviar el correo electrónico, emailform.jsp ofrece un resultado al usuario; un ejemplo es la siguiente captura de pantalla:

![Resultado del envío del correo][emailresult]

## Pasos siguientes

Implemente la aplicación en el emulador de proceso y, en un explorador, ejecute emailform.jsp, introduzca los valores en el formulario, haga clic en **Send this email (Enviar este correo electrónico)** y consulte los resultados en sendemail.jsp.

Este código se incluye para mostrar cómo utilizar SendGrid de Java en Azure. Antes de implementarlo en Azure en producción, es posible que desee agregar más controles de errores u otras características. Por ejemplo:

* Puede utilizar los blogs de almacenamiento de Azure o la Base de datos SQL para almacenar direcciones de correo electrónico y mensajes de correo electrónico, en lugar de utilizar un formulario web. Para obtener más información acerca de cómo usar los blobs de Almacenamiento de Azure en Java, consulte [Uso del servicio de almacenamiento de blobs desde Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/). Para obtener más información acerca de cómo usar Base de datos SQL en Java, consulte [Uso de Base de datos SQL en Java](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Puede usar `RoleEnvironment.getConfigurationSettings` para recuperar el nombre de usuario y la contraseña de SendGrid de la configuración de la implementación, en lugar de usar el formulario web para recuperar estos valores. Para obtener información sobre la clase `RoleEnvironment`, consulte [Uso de la biblioteca de tiempo de ejecución del servicio de Azure en JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) y la documentación del paquete de tiempo de ejecución del servicio de Azure en <http://dl.windowsazure.com/javadoc>.
* Para obtener más información acerca de cómo usar SendGrid en Java, consulte [Envío de correo electrónico con SendGrid desde Java](store-sendgrid-java-how-to-send-email.md).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg

<!---HONumber=AcomDC_0128_2016-->