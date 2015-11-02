<properties 
	pageTitle="Использование службы электронной почты SendGrid (.NET) | Microsoft Azure" 
	description="Узнайте, как отправить электронное сообщение с помощью службы электронной почты SendGrid в Azure. Примеры написаны на языке C# и используют API .NET." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="team-pi@sendgrid.com"/>





# Как отправлять электронную почту с помощью SendGrid и Azure


## Обзор

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры написаны на языке C# и используют интерфейс API .NET. Здесь описываются сценарии **создания сообщения электронной почты**, **его отправки**, **добавления вложений** и **использования фильтров**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе [Дальнейшие действия][].

## Общие сведения о почтовой службе SendGrid

SendGrid — это [облачная служба электронной почты], которая предоставляет надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Ниже перечислены наиболее распространенные сценарии использования SendGrid.

-   Автоматическая отправка уведомлений о получении клиентам.
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений.
-   Сбор показателей в реальном времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов.
-   Создание отчетов для определения тенденций.
-   Пересылка запросов клиентов.
-   Обработка входящих сообщений электронной почты.

Дополнительные сведения см. на сайте [https://sendgrid.com](https://sendgrid.com) или в нашей [библиотеке для C#][sendgrid-csharp].

## Создание учетной записи SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## Справочник по библиотеке классов SendGrid .NET

[Пакет SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) — это самый простой способ получить интерфейс API службы SendGrid и настроить свое приложение с учетом всех зависимостей. Пакет NuGet — это расширение Visual Studio, включенное в Microsoft Visual Studio 2015, которое упрощает установку и обновление библиотек и инструментов.

> [AZURE.NOTE]Чтобы установить пакет NuGet, если вы используете версию Visual Studio, предшествующую Visual Studio 2015, посетите сайт [http://www.nuget.org](http://www.nuget.org) и нажмите кнопку **Установить NuGet**.

Для установки пакета SendGrid NuGet в приложении выполните следующие действия:

1.  Создайте новый проект.

    ![Создание нового проекта][create-new-project]

2.  Выберите шаблон.

    ![Выберите шаблон][select-a-template]

3.  В **обозревателе решений** щелкните правой кнопкой мыши пункт **Ссылки**, а затем выберите команду **Управление пакетами NuGet**.

4.  Выполните поиск **SendGrid** и выберите элемент **SendGrid** из списка результатов.

    ![Пакет SendGrid NuGet][SendGrid-NuGet-package]

5.  Нажмите кнопку **Установить**, чтобы выполнить установку, а затем закройте диалоговое окно.

Библиотека классов SendGrid .NET называется **SendGridMail**. Она содержит такие пространства имен:

-   **SendGridMail**: предназначено для создания элементов почты и работы с ними.
-   **SendGridMail.Transport**: предназначено для электронной почты по протоколу **SMTP** или HTTP 1.1 с использованием **Web/REST**.

Добавьте следующие объявления пространств имен кода в начало любого файла C#, в котором вы собираетесь получать доступ к хранилищу службы электронной почты SendGrid программным способом. **System.Net** и **System.Net.Mail** являются предопределенными пространствами имен .NET Framework, так как содержат типы, обычно используемые в интерфейсах API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## Практическое руководство. Создание сообщения эл. почты

Для создания сообщения электронной почты используется объект **SendGridMessage**. После создания объекта сообщения можно задать свойства и методы, включая отправителя и получателя электронной почты, тему и текст сообщения.

В следующем примере показано, как создать полностью заполненный объект электронной почты.

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Дополнительные сведения о всех свойствах и методах, поддерживаемых типом **SendGrid**, см. в разделе [sendgrid-csharp][] на портале GitHub.

## Практическое руководство. Отправка сообщения эл. почты

После создания сообщения электронной почты его можно отправить с помощью протокола Web API, предоставленного службой SendGrid. Также можно [воспользоваться встроенной библиотекой .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

Чтобы отправить электронное сообщение, нужно указать учетные данные учетной записи SendGrid (имя пользователя и пароль) или ключ API SendGrid. Ключ API является предпочтительным. Если требуются сведения о том, как настроить ключи API, ознакомьтесь с нашей [документацией](https://sendgrid.com/docs/Classroom/Send/api_keys.html).

Вы можете сохранить эти учетные данные с помощью портала Azure, щелкнув «НАСТРОЙКА» и добавив пары ключ-значение в разделе «Параметры приложения».

 ![Параметры приложения Azure][azure_app_settings]

 После этого обращаться к ним можно следующим образом.
    
    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

С помощью учетных данных:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

С помощью ключа API:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


В следующих примерах показано, как отправить сообщение с помощью веб-API.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## Практическое руководство. Добавление вложения

Чтобы добавить вложение в сообщение, вызовите метод **AddAttachment**, а также укажите имя и путь к файлу, который следует вложить. Можно включить несколько вложений, вызвав этот метод по одному разу для каждого вкладываемого файла. На приведенном ниже примере продемонстрировано добавление вложения в сообщение.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Также для добавления вложений можно воспользоваться **потоковой передачей** данных. Для этого вызовите указанный выше метод, выберите **AddAttachment**, но при этом передайте в него поток данных, а затем укажите имя файла, которое нужно показать в виде сообщения. В таком случае потребуется добавить библиотеку System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## Практическое руководство. Использование приложений для включения нижних колонтитулов, отслеживания и аналитики

SendGrid обеспечивает дополнительные преимущества электронной почты благодаря использованию приложений. Это определенные параметры, которые можно добавить в электронное сообщение для включения определенных функций, таких как включения отслеживания щелчков, аналитики Google, отслеживание подписок и т. д. Полный список приложений приведен в разделе [Параметры приложений][].

Приложения можно использовать в сообщениях электронной почты **SendGrid** с помощью методов, реализованных в рамках класса **SendGrid**.

В следующих примерах показаны фильтры нижних колонтитулов и отслеживания щелчков:

### Нижний колонтитул

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Отслеживание щелчков

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href="http://www.example.com">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## Практическое руководство. Использование дополнительных служб SendGrid

SendGrid поддерживает веб-интерфейсы API и методы WebHook, с помощью которых можно использовать дополнительные преимущества службы SendGrid в своем приложении Azure. Дополнительные сведения см. в [документации по интерфейсу API SendGrid][].

## Дальнейшие действия

Вы получили основные сведения о службе доставки электронной почты SendGrid. Дополнительные сведения вы найдете по следующим ссылкам.

*   Репозиторий библиотеки C# для SendGrid: [sendgrid-csharp][]
*   Документация по интерфейсу API SendGrid: <https://sendgrid.com/docs>
*   Специальное предложение SendGrid для клиентов Azure: [https://sendgrid.com](https://sendgrid.com)

  [Дальнейшие действия]: #next-steps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  [create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
  [select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
  [azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Параметры приложений]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [документации по интерфейсу API SendGrid]: https://sendgrid.com/docs
  
  [облачная служба электронной почты]: https://sendgrid.com/email-solutions
  [доставки электронной почты]: https://sendgrid.com/transactional-email
 

<!---HONumber=Oct15_HO4-->