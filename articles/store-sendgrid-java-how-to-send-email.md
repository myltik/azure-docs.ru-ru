<properties linkid="dev-java-how-to-access-control" urlDisplayName="Служба электронной почты SendGrid" pageTitle="Как использовать службу электронной почты SendGrid (Java)" metaKeywords="Azure SendGrid, служба электронной почты Azure, Azure SendGrid Java, служба электронной почты Azure Java" description="Узнайте, как отправлять сообщения электронной почты с помощью службы электронной почты SendGrid в Azure. Примеры кода написаны на Java." metaCanonical="" services="" documentationCenter="Java" title="Как отправлять электронную почту с помощью SendGrid из Java" authors="waltpo" solutions="" manager="" editor="mollybos" />





# Как отправлять электронную почту с помощью SendGrid из Java

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты
SendGrid в Azure. Примеры кода написаны на
Java. Описываемые здесь сценарии включают **создание сообщения электронной почты**,
**отправку сообщения электронной почты**, **добавление вложений**, **использование фильтров** и **обновление
свойств**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе
[Дальнейшие действия][].

## Оглавление

-   [Что такое служба электронной почты SendGrid?][]
-   [Создание учетной записи SendGrid][]
-   [Практическое руководство. Использование библиотек javax.mail][]
-   [Практическое руководство. Создание сообщения эл. почты][]
-   [Практическое руководство. Отправка сообщения эл. почты][]
-   [Практическое руководство. Добавление вложения][]
-   [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]
-   [Практическое руководство. Обновление свойств электронной почты][]
-   [Практическое руководство. Использование дополнительных служб SendGrid][]
-   [Следующие шаги][]

## <a name="bkmk_WhatIsSendGrid"> </a>Что такое служба электронной почты SendGrid?

SendGrid – это [облачная служба электронной почты], которая предоставляет надежные возможности
[доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам
-   Администрирование списков рассылки для ежемесячной
    отправки клиентам электронных листовок и специальных предложений
-   Сбор показателей в режиме реального времени по таким параметрам,
как заблокированная электронная почта и реагирование клиентов
-   Создание отчетов для определения тенденций
-   Пересылка запросов клиентов
- Уведомления от приложения по электронной почте

Дополнительные сведения см. в разделе <http://sendgrid.com>.

## <a name="bkmk_CreateSendGridAcct"> </a>Создание учетной записи SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>Практическое руководство. Использование библиотек javax.mail

Получите библиотеки javax.mail, например, по адресу
<http://www.oracle.com/technetwork/java/javamail>, и импортируйте их
в код. Высокоуровневое представление отправки сообщения электронной почты
с помощью SMTP, используя библиотеку javax.mail, выглядит следующим образом:

1.  Укажите значения SMTP, включая SMTP-сервер, для которого
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
           	  // â€¦

2.  Разверните класс <span class="auto-style1">javax.mail.Authenticator</span>
    , а затем в реализации
    метода <span class="auto-style1">getPasswordAuthentication</span> возвратите имя пользователя и пароль SendGrid .  

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

4.  Создайте сообщение и назначьте значения **Кому**, **От**, **Тема** и содержимое. Это показано в разделе [Практическое руководство. Создание сообщения электронной почты](#bkmk_HowToCreateEmail).
5.  Отправьте сообщение через
    объект <span class="auto-style1">javax.mail.Transport</span>. Это
    показано в разделе [Практическое руководство. Отправка сообщения электронной почты][How to: Send an Email].
    .

## <a name="bkmk_HowToCreateEmail"> </a>Практическое руководство. Создание сообщения электронной почты

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

## <a name="bkmk_HowToSendEmail"> </a>Практическое руководство. Отправка сообщения электронной почты

Ниже показано, как отправить сообщение электронной почты

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

SendGrid предоставляет дополнительные функциональные возможности электронной почты благодаря использованию
*фильтров*. Это параметры, которые могут быть добавлены в сообщение электронной почты для включения определенных функций, например включения отслеживания щелчков, отслеживание подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

-   Ниже показано, как вставить фильтр нижнего колонтитула, который выводит HTML-текст, отображаемый в нижней части отправляемого сообщения электронной почты.

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   Еще одним примером фильтра является отслеживание щелчков. Предположим, что текст вашего сообщения
    электронной почты содержит гиперссылку, похожую на приведенную ниже, и вы хотите
    отслеживать частоту ее выбора:

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   Чтобы включить отслеживание щелчков, используйте следующий код:

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>Практическое руководство. Обновление свойств электронной почты

Некоторые свойства электронной почты могут быть перезаписаны с помощью операторов **set*свойство*** или добавлены с помощью **add*свойство***.

Например, для указания адреса **ReplyTo** используйте следующий код:

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

Чтобы добавить получателя **СК**, используйте следующий код:

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>Практическое руководство. Использование дополнительных служб SendGrid

SendGrid предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid из вашего приложения Azure. Исчерпывающие сведения см. в документации по [интерфейсу SendGrid API][].

## <a name="bkmk_NextSteps"> </a>Дальнейшие действия

Вы узнали основные сведения о службе доставки электронной почты SendGrid.
Для получения дополнительных сведений используйте следующие ссылки.

* Пример, демонстрирующий использование SendGrid в развертывании Azure: [Практическое руководство. Отправка сообщения электронной почты из Java с помощью SendGrid в развертывании Azure](/ru-ru/develop/java/how-to-guides/sendgrid-sample/)
* Сведения о SendGrid Java: <http://sendgrid.com/docs/Code_Examples/java.html>
* Документация по интерфейсу SendGrid API: <http://sendgrid.com/docs/API_Reference/index.html>
* Специальное предложение SendGrid для клиентов Azure: <http://sendgrid.com/azure.html>

  [Следующие шаги]: #bkmk_NextSteps
  [Что такое служба электронной почты SendGrid?]: #bkmk_WhatIsSendGrid
  [Создание учетной записи SendGrid]: #bkmk_CreateSendGridAcct
  [Практическое руководство. Использование библиотек javax.mail]: #bkmk_HowToUseJavax
  [Практическое руководство. Создание сообщения эл. почты]: #bkmk_HowToCreateEmail
  [Практическое руководство. Отправка сообщения эл. почты]: #bkmk_HowToSendEmail
  [Практическое руководство. Добавление вложения]: #bkmk_HowToAddAttachment
  [Практическое руководство. использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #bkmk_HowToUseFilters
  [Практическое руководство. Обновление свойств электронной почты]: #bkmk_HowToUpdateEmail
  [Практическое руководство. Использование дополнительных служб SendGrid]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: http://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: http://www.sendgrid.com/azure.html
  [http://sendgrid.com/features]: http://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [Параметры фильтров]: http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [интерфейсу SendGrid API]: http://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: http://sendgrid.com/azure.html
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставки электронной почты]: http://sendgrid.com/transactional-email

