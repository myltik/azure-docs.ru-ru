<properties 
	pageTitle="Использование службы электронной почты SendGrid (.NET) | Microsoft Azure" 
	description="Узнайте, как отправить электронное сообщение с помощью службы электронной почты SendGrid в Azure. Примеры написаны на языке C# и используют API .NET." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="erikre"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>





# Как отправлять электронную почту с помощью SendGrid и Azure

Последнее обновление: 24 февраля 2015 г.

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

Дополнительные сведения см. на веб-сайте [https://sendgrid.com](https://sendgrid.com).

## Создание учетной записи SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## Справочник по библиотеке классов SendGrid .NET

[Пакет SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) — это самый простой способ получить интерфейс API службы SendGrid и настроить свое приложение с учетом всех зависимостей. Пакет NuGet — это расширение Visual Studio, включенное в Microsoft Visual Studio 2012, которое упрощает установку и обновление библиотек и инструментов.

> [AZURE.NOTE]Чтобы установить пакет NuGet, если вы используете версию Visual Studio ниже Visual Studio 2012, посетите веб-сайт [http://www.nuget.org](http://www.nuget.org) и нажмите кнопку **Установить NuGet**.

Для установки пакета SendGrid NuGet в приложении выполните следующие действия:

1.  В **обозревателе решений** щелкните правой кнопкой мыши пункт **Ссылки**, а затем выберите команду **Управление пакетами NuGet**.

2.  На левой панели диалогового окна **Управление пакетами NuGet** щелкните пункт **В сети**.

3.  Найдите пакет **SendGrid** и выберите пункт **SendGrid** в списке результатов (текущая версия — 5.0.0).

    ![Пакет SendGrid NuGet][SendGrid-NuGet-package]

4.  Нажмите кнопку **Установить**, чтобы выполнить установку, а затем закройте диалоговое окно.

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

Чтобы отправить электронное сообщение, нужно указать учетные данные учетной записи SendGrid (имя пользователя и пароль). Следующий код демонстрирует, как поместить учетные данные в оболочку объекта **NetworkCredential**:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

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

* Репозиторий библиотеки C# для SendGrid: [sendgrid-csharp][]
*   Документация по интерфейсу API SendGrid: <https://sendgrid.com/docs>
*   Специальное предложение SendGrid для клиентов Azure: [https://sendgrid.com](https://sendgrid.com)

  [Дальнейшие действия]: #nextsteps
  [What is the SendGrid Email Service?]: #whatis
  [Create a SendGrid Account]: #createaccount
  [Reference the SendGrid .NET Class Library]: #reference
  [How to: Create an Email]: #createemail
  [How to: Send an Email]: #sendemail
  [How to: Add an Attachment]: #addattachment
  [How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
  [How to: Use Additional SendGrid Services]: #useservices
  
  
  [special offer]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Параметры приложений]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [документации по интерфейсу API SendGrid]: https://sendgrid.com/docs
  
  [облачная служба электронной почты]: https://sendgrid.com/email-solutions
  [доставки электронной почты]: https://sendgrid.com/transactional-email
 

<!---HONumber=Oct15_HO2-->