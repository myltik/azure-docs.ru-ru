<properties linkid="store-requestform-preview" urlDisplayName="Запрос интеграции магазина Azure" pageTitle="Отправка электронной почты с помощью SendGrid из Java в развертывании Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Отправка электронной почты с помощью SendGrid из Java в развертывании Azure" authors="waltpo" solutions="" manager="" editor="mollybos" videoId="" scriptId="" />





# Отправка электронной почты с помощью SendGrid из Java в развертывании Azure

В следующем примере показано, как отправлять сообщения электронной почты с веб-страницы, размещенной в Azure, с помощью SendGrid. В полученном приложении пользователю предлагается ввести нужные данные электронной почты, как показано на следующем снимке экрана.

![Формат адреса электронной почты][emailform]

Итоговое сообщение электронной почты будет выглядеть примерно так.

![Сообщение электронной почты][emailsent]

Чтобы использовать код, представленный в этом разделе, выполните следующие действия:

1. Получите JAR-файлы javax.mail, например, по адресу <http://www.oracle.com/technetwork/java/javamail/index.html>.
2. Добавьте JAR-файлы в путь построения Java.
3. Если вы используете Eclipse для создания Java-приложения, включите библиотеки SendGrid в файл развертывания приложения (WAR) с помощью компонента сборки развертывания Eclipse. Если среда Eclipse не используется, включите эти библиотеки в ту же роль Azure, которой принадлежит ваше Java-приложение, после чего добавьте в приложение путь к классу.


Для отправки электронной почты вам также понадобится собственное имя пользователя и пароль SendGrid. Чтобы начать работу с SendGrid, см. раздел [Как отправлять электронную почту с помощью SendGrid из Java](../store-sendgrid-java-how-to-send-email).

Кроме того, если вы не используете среду Eclipse, мы настоятельно рекомендуем вам ознакомиться с разделом [Создание приложения "Hello World" для Azure в Eclipse](http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690944), а также другими способами размещения Java-приложений в Azure.

## Создание веб-формы для отправки сообщения электронной почты

В следующем коде показано, как создать веб-форму, позволяющую извлечь данные пользователя для отправки электронной почты. В рамках данных материалов этот JSP-файл имеет имя **emailform.jsp**.

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

## Создание кода для отправки сообщения электронной почты

Следующий код, который вызывается после заполнения формы в emailform.jsp, создает сообщение электронной почты, а затем отправляет его. В рамках данных материалов этот JSP-файл имеет имя **sendemail.jsp**.

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
	     // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
	
	     // Uncomment the following if you want to enable click tracking.
	     // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
	     
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

![Результат отправки почты][emailresult]

## Дальнейшие действия

Разверните приложение в эмуляторе вычислений, запустите emailform.jsp в браузере, введите значения в форме, нажмите кнопку **Отправить это сообщение электронной почты**, а затем просмотрите результаты в sendemail.jsp.

Этот код был представлен для того, чтобы показать вам, как использовать SendGrid для языка Java в Azure. Возможно, перед развертыванием в рабочей среде Azure потребуется добавить в него дополнительные обработчики ошибок и другие функции. Например: 

* Вместо веб-формы для адресов и сообщений электронной почты можно использовать хранилище BLOB-объектов Azure или базу данных SQL. Дополнительные сведения об использовании BLOB-объектов Azure в Java см. в разделе [Использование службы хранилища BLOB-объектов Azure из Java](http://www.windowsazure.com/ru-ru/develop/java/how-to-guides/blob-storage/). Дополнительные сведения об использовании базы данных SQL в Java см. в разделе [Использование базы данных SQL в Java](http://www.windowsazure.com/ru-ru/develop/java/how-to-guides/using-sql-azure-in-java/).
* Для получения имени пользователя и пароля SendGrid из параметров конфигурации развертывания вместо веб-формы можно использовать `RoleEnvironment.getConfigurationSettings`. Дополнительные сведения о классе `RoleEnvironment` см. в разделе [Использование библиотеки среды выполнения служб Azure в JSP](http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690948), а также в документации по пакету среды выполнения служб Azure по адресу <http://dl.windowsazure.com/javadoc>.
* Дополнительные сведения об использовании SendGrid в Java см. в разделе [Как отправлять электронную почту с помощью SendGrid из Java](../store-sendgrid-java-how-to-send-email).

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg

