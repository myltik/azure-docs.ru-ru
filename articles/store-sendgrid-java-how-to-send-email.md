<properties urlDisplayName="SendGrid Email Service" pageTitle="Как использовать службу электронной почты SendGrid (Java) - Azure" metaKeywords="Azure SendGrid, электронная почта Azure, Azure SendGrid Java, служба электронной почты Azure Java" description="Learn how send email with the SendGrid email service on Azure. Code samples written in Java." metaCanonical="" services="" documentationCenter="Java" title="How to Send Email Using SendGrid from Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Как отправлять электронную почту с помощью SendGrid из Java

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры кода написаны на Java. Описываемые здесь сценарии включают в себя **создание электронного сообщения**,**отправку электронного сообщения**, **добавление вложений**, **использование фильтров** и**обновление свойств**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе [Дальнейшие действия][].

## Оглавление

-   [Что такое служба электронной почты SendGrid?][]
-   [Создание учетной записи SendGrid][]
-   [Практическое руководство. Использование библиотек javax.mail][]
-   [Практическое руководство. Создание электронного сообщения][]
-   [Практическое руководство. Отправка электронного сообщения][]
-   [Практическое руководство. Добавление вложения][]
-   [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]
-   [Практическое руководство. Обновление свойств электронной почты][]
-   [Практическое руководство. Использование дополнительных служб SendGrid][]
-   [Дальнейшие действия][]

## <a name="bkmk_WhatIsSendGrid"> </a>Что такое служба электронной почты SendGrid?

SendGrid - это [облачная служба электронной почты], обеспечивающая надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространены следующие сценарии использования SendGrid:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для отправки клиентам ежемесячно
    электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и 
    реагирование на запросы клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
- Уведомления от приложения по электронной почте

Дополнительные сведения см. на веб-сайте <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Практическое руководство. Использование библиотек javax.mail

Получите библиотеки javax.mail, например по на странице
<http://www.oracle.com/technetwork/java/javamail> и импортируйте их в
свой код. На высоком уровне, чтобы с помощью библиотеки javax.mail
отправить электронную почту через SMTP, выполните следующее.

1.  Укажите данные для SMTP, в том числе SMTP-сервер, который для
    SendGrid имеет значение smtp.sendgrid.net.

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
           	  // ...

2.  Расширьте <span class="auto-style1"> класс javax.mail.Authenticator</span>
    , и в реализации
    метода <span class="auto-style1">getPasswordAuthentication</span> 
 верните имя пользователя и пароль SendGrid.  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  Создайте сеанс проверки электронной почты с помощью 
    объекта <span class="auto-style1">javax.mail.Session</span>.  

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  Создайте сообщение и присвойте значения **Кому**, **От**, **Тема** и
. Выполнение этих действий описано в разделе [Практическое руководство. Создайте раздел ](#bkmk_HowToCreateEmail)Электронное сообщение.
5.  Отправьте сообщение с помощью 
    объекта <span class="auto-style1">javax.mail.Session</span>. Выполнение
 этих действий описано в разделе [Практическое руководство. Отправка сообщения электронной почты][How to: Send an Email]
    .

## <a name="bkmk_HowToCreateEmail"> </a>Практическое руководство. Создание электронного сообщения

Ниже показано, как задать значения для сообщения электронной почты.

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

## <a name="bkmk_HowToSendEmail"> </a>Практическое руководство. Отправка электронного сообщения

Ниже показано, как отправить сообщение электронной почты.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>Практическое руководство. Добавление вложения

В следующем коде показано, как добавить вложение.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики

SendGrid предоставляет дополнительные функциональные возможности работы с электронной почтой благодаря использованию *фильтров*. Это параметры, которые могут быть добавлены в сообщение электронной почты для включения определенных функций, например включения отслеживания щелчков, аналитики Google, отслеживания подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

-   Ниже показано, как вставить фильтр нижнего колонтитула, в результате
    чего в нижней части отправляемого сообщения электронной почты отобразится текст HTML.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-     Еще одним примером фильтра является отслеживание щелчков. Предположим, текст электронного сообщения содержит гиперссылку, похожую на приведенную ниже, и нужно отследить частоту переходов по ней: 

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   To enable the click tracking, use the following code:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Практическое руководство. Обновление свойств электронной почты

Некоторые свойства электронной почты могут быть перезаписаны с помощью **set*Property*** или добавлены с помощью **add*Property***.

Например, для указания адресов **Получатель** используйте следующий код:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Чтобы добавить получателя в поле **Копия**, используйте следующий код:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Практическое руководство. Использование дополнительных служб SendGrid

SendGrid поддерживает различные веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid, доступные в приложении Azure. Дополнительные сведения см. в [документации по API-интерфейсу SendGrid][].

## <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы получили основные сведения о службе доставки электронной почты SendGrid. Дополнительные сведения можно получить по следующим ссылкам.

* Пример, в котором показано использование SendGrid в развертывании Azure: [Как отправлять электронную почту с помощью SendGrid из Java в развертывании Azure](../store-sendgrid-java-how-to-send-email-example/)
* SendGrid Java SDK: <https://sendgrid.com/docs/Code_Examples/java.html>
* 	Документация по интерфейсу SendGrid API: <https://sendgrid.com/docs/API_Reference/index.html>
* Специальное предложение SendGrid для клиентов Azure: <https://sendgrid.com/windowsazure.html>

  [Дальнейшие действия]: #bkmk_NextSteps
  [Что такое служба электронной почты SendGrid?]: #bkmk_WhatIsSendGrid
  [Создание учетной записи SendGrid]: #bkmk_CreateSendGridAcct
  [Практическое руководство. Использование библиотек javax.mail]: #bkmk_HowToUseJavax
  [Практическое руководство. Создание электронного сообщения]: #bkmk_HowToCreateEmail
  [Практическое руководство. Отправка сообщения электронной почты]: #bkmk_HowToSendEmail
  [Практическое руководство. Добавление вложения]: #bkmk_HowToAddAttachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #bkmk_HowToUseFilters
  [Практическое руководство. Обновление свойств электронной почты]: #bkmk_HowToUpdateEmail
  [Практическое руководство. Использование дополнительных служб SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: https://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
  [http://sendgrid.com/features]: https://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Параметры фильтра]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [Документация по интерфейсу SendGrid API]: https://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
  [облачная служба электронной почты]: https://sendgrid.com/email-solutions
  [доставка электронной почты]: https://sendgrid.com/transactional-email
