<properties 
	pageTitle="Как отправлять электронную почту с помощью SendGrid из Java в развертывании Azure" 
	description="" 
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

# Как отправлять электронную почту с помощью SendGrid из Java в развертывании Azure

В следующем примере показано, как отправлять сообщения электронной почты с помощью SendGrid с веб-страницы, размещенной в Azure. В полученном приложении пользователю предлагается ввести данные электронной почты, как показано на следующем снимке экрана.

![Email form][emailform]

Итоговое сообщение электронной почты будет выглядеть примерно так.

![Email message][emailsent]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. JAR-файлы javax.mail можно получить, например, по адресу: <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Добавьте JAR-файлы в путь сборки Java.
3. Если вы используете Eclipse для создания приложения Java, включите библиотеки SendGrid в файл развертывания приложения (WAR-файл) с помощью компонента сборки развертывания Eclipse. Если среда Eclipse для создания данного приложения Java, убедитесь, что библиотеки включены в ту же роль Azure, которой принадлежит ваше Java-приложение, а также добавлены в путь к классу приложения.


Для отправки электронной почты вам также понадобится собственное имя пользователя и пароль SendGrid. Чтобы начать работу с SendGrid, см. раздел [как отправлять электронную почту с помощью SendGrid из Java](../store-sendgrid-java-how-to-send-email).

Кроме того, знакомство с разделом [Создание приложения Hello World для Azure в Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944), или с другими способами размещения Java-приложений в Azure, если вы не используете Eclipse, настоятельно рекомендуется.

## Создание веб-формы для отправки электронных сообщений

В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для отправки электронной почты. В данном контексте этот JSP-файл имеет имя **emailform.jsp**.

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

## Создание кода для отправки электронного сообщения

Следующий код, который вызывается после заполнения формы в emailform.jsp, создает сообщение электронной почты, а затем отправляет его. В данном контексте этот JSP-файл имеет имя **sendemail.jsp**.

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

Кроме отправки электронной почты, emailform.jsp предоставляет результат пользователю. Пример приведен на следующем снимке экрана:

![Send mail result][emailresult]

## Дальнейшие действия

Разверните приложение в эмуляторе вычислений, запустите emailform.jsp в браузере, введите значения в форме, нажмите кнопку **Отправить это сообщение электронной почты**, а затем просмотрите результаты в sendemail.jsp.

Этот код демонстрирует использование SendGrid для Java в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например: 

* Вместо веб-формы для сохранения адресов и электронных сообщений вы можете использовать хранилище больших двоичных объектов Azure или базу данных SQL. Сведения об использовании BLOB-объектов хранилища Azure в Java см. в разделе [использование службы хранилища больших двоичных объектов из Java](http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/). Сведения об использовании базы данных SQL в Java см. в разделе [использование базы данных SQL в Java](http://www.windowsazure.com/develop/java/how-to-guides/using-sql-azure-in-java/).
* Можно использовать `RoleEnvironment.getConfigurationSettings` для получения SendGrid имя пользователя и пароль из параметров конфигурации развертывания, вместо того чтобы использовать веб-формы для получения этих значений. Сведения о `RoleEnvironment` см. в разделе [использование библиотеки среды выполнения служб Azure в JSP](http://msdn.microsoft.com/library/windowsazure/hh690948) и документации пакета среды выполнения служб Azure в <http://dl.windowsazure.com/javadoc>.
* Дополнительные сведения об использовании SendGrid в Java см. в разделе [как отправлять электронную почту с помощью SendGrid из Java](../store-sendgrid-java-how-to-send-email).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg

<!--HONumber=47-->
