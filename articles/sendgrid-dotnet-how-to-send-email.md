<properties 
	pageTitle="Использование почтовой службы SendGrid (.NET) - Azure" 
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

Последнее обновление: 24 февраля 2015 г.

<h2><a name="overview"></a><span  class="short-header">Обзор</span></h2>

В этом руководстве показано, как выполнять типовые задачи программирования с помощью
службы электронной почты SendGrid в Azure. Примеры написаны на языке C# и используют интерфейс API .NET. Описываемые здесь сценарии включают в себя **создание сообщения электронной почты**, **его отправку**, **добавление вложений** и **использование фильтров**. Дополнительные сведения о SendGrid и отправке электронной почты см. в разделе
[Дальнейшие действия][].

<h2><a name="whatis"></a><span  class="short-header">Общие сведения о почтовой службе SendGrid</span></h2>

SendGrid - это [облачная служба электронной почты], которая предоставляет надежные возможности [доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Ниже перечислены наиболее распространенные сценарии использования SendGrid.

-   Автоматическая отправка уведомлений о получении клиентам.
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений.
-   Сбор показателей в реальном времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов.
-   Создание отчетов для определения тенденций.
-   Пересылка запросов клиентов.
-   Обработка входящих сообщений электронной почты.

Дополнительные сведения см. на веб-сайте [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Создание учетной записи SendGrid</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Справочник по библиотеке классов .NET SendGrid</h2>

[Пакет SendGrid NuGet](https://www.nuget.org/packages/Sendgrid) - это самый простой способ получить интерфейс API службы SendGrid и настроить свое приложение с учетом всех зависимостей. NuGet - это
расширение Visual Studio, которое входит в состав программы Microsoft Visual Studio 2012 и упрощает установку и обновление библиотек и средств. 

> [AZURE.NOTE] Чтобы установить пакет NuGet при использовании версии Visual Studio, предшествующей Visual Studio 2012, посетите веб-сайт [http://www.nuget.org](http://www.nuget.org) и нажмите кнопку **Установить NuGet**.

Для установки пакета SendGrid NuGet в приложении выполните следующие действия:

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки**, а затем выберите команду **Управление пакетами NuGet**.

2.  На левой панели диалогового окна **Управление пакетами NuGet** щелкните пункт **В сети**.

3.  Найдите пакет **SendGrid** и выберите пункт **SendGrid** в списке результатов (текущая версия - 5.0.0).

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Нажмите кнопку **Установить**, чтобы выполнить установку, а затем закройте диалоговое окно.

Библиотека классов SendGrid .NET называется **SendGridMail**. Она содержит такие пространства имен:

-   **SendGridMail** для создания элементов почты и работы с ними.
-   **SendGridMail.Transport** для отправки сообщений с помощью протокола **SMTP** или протокола HTTP 1.1 с использованием **Web/REST**.

Добавьте указанные ниже объявления пространств имен кода в начало любого файла C\#, в котором необходимо получать программный доступ к почтовой службе SendGrid.
**System.Net** и **System.Net.Mail** являются предопределенными пространствами имен .NET Framework, так как содержат типы, обычно используемые в интерфейсах API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Практическое руководство. Создание сообщения электронной почты</h2>

Для создания сообщения электронной почты используется объект **SendGridMessage**. После создания объекта сообщения можно задать свойства и методы, включая отправителя электронной почты, получателя электронной почты, тему и текст сообщения.

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

Дополнительные сведения обо всех свойствах и методах, поддерживаемых типом
**SendGrid**, см. в разделе [sendgrid-csharp][] на GitHub.

<h2><a name="sendemail"></a>Практическое руководство. Отправка сообщения электронной почты</h2>

После создания сообщения электронной почты его можно отправить с помощью протокола Web API, предоставленного службой SendGrid. Кроме того, можно [воспользоваться  встроенной библиотекой.NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

Чтобы отправить электронную почту, необходимо указать свои
учетные данные SendGrid (имя пользователя и пароль). С помощью приведенного ниже кода продемонстрирован способ переноса учетных данных в оболочку объекта **NetworkCredential**.
    
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

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

<h2><a name="addattachment"></a>Практическое руководство. Добавление вложения</h2>

Можно добавлять вложения в сообщение путем вызова метода **AddAttachment**, а также указания имени и пути необходимого файла.
Можно добавить несколько вложений, поочередно вызвав этот метод для каждого необходимого файла. На приведенном ниже примере продемонстрировано добавление вложения в сообщение.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Кроме того, можно добавлять вложения с **потока** данных. Это можно сделать путем вызова описанного выше метода **AddAttachment**, но при этом передайте в него поток данных, а затем укажите имя файла, который необходимо добавить в сообщение. В таком случае потребуется добавить библиотеку System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">Практическое руководство. Использование приложения для включения нижних колонтитулов, отслеживания и аналитики</span></h2>

SendGrid обеспечивает дополнительные преимущества электронной почты благодаря использованию приложений. Это определенные параметры, которые можно добавлять в сообщения для включения определенных функций, например отслеживания щелчков, аналитики Google, отслеживания подписок и т. п. Полный список приложений приведен в разделе
[Параметры приложений][].

Приложения можно использовать в сообщениях **SendGrid** с помощью методов, реализованных в рамках класса **SendGrid**.

На приведенных ниже примерах продемонстрирована работа фильтров нижних колонтитулов и отслеживания щелчков.

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
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>Практическое руководство. Использование дополнительных служб SendGrid</h2>

SendGrid поддерживает веб-интерфейсы API и методы WebHook, с помощью которых можно использовать дополнительные преимущества службы SendGrid в своем приложении Azure. Дополнительные сведения см. в [документации пр интерфейсу API SendGrid][].

<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Ознакомившись с основами почтовой службы SendGrid, воспользуйтесь приведенными ниже ссылками, чтобы узнать больше.

* Репозиторий библиотеки SendGrid C\#: [Параметры приложений][]
*   Документация по интерфейсу SendGrid API: <https://sendgrid.com/docs>
*   Специальное предложение SendGrid для клиентов Azure: [https://sendgrid.com](https://sendgrid.com)

  [Дальнейшие действия]: #nextsteps
  [Общие сведения о почтовой службе SendGrid]: #whatis
  [Создание учетной записи SendGrid]: #createaccount
  [Справочник по библиотеке классов SendGrid .NET]: #reference
  [Практическое руководство. Создание сообщения электронной почты]: #createemail
  [Практическое руководство. Отправка сообщения электронной почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  
  
  [специальное предложение]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [Параметры приложений]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP и веб-интерфейс API]: https://sendgrid.com/docs/Integrate/index.html
  [Параметры приложения]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Документация по интерфейсу API SendGrid]: https://sendgrid.com/docs
  
  [облачная служба электронной почты]: https://sendgrid.com/email-solutions
  [доставки электронной почты]: https://sendgrid.com/transactional-email

<!--HONumber=49-->