<properties urlDisplayName="SendGrid Email Service" pageTitle="Использование службы электронной почты SendGrid (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Узнайте, как отправить электронное сообщение с помощью службы электронной почты SendGrid в Azure. Примеры написаны на языке C# и используют API .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="erikre" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/24/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />





# Как отправлять электронную почту с помощью SendGrid и Azure

Последнее обновление: 24 октября 2014 г.

В этом руководстве показано, как в Azure выполнять типовые задачи с помощью службы электронной почты SendGrid. Примеры написаны на языке C# и используют интерфейс API .NET. Описываемые здесь сценарии включают **создание сообщения эл. почты**, **отправку сообщения эл. почты**, **добавление вложений** и **использование фильтров**. Дополнительные сведения о SendGrid и отправке эл. почты см. в разделе [Дальнейшие действия][].

<h2><a name="toc"></a>Оглавление</h2>

[Что такое служба электронной почты SendGrid?][]   
[Создание учетной записи SendGrid][]   
[Справочник по библиотеке классов SendGrid .NET][]   
[Практическое руководство. Создание электронного сообщения][]   
[Практическое руководство. Отправка электронного сообщения][]   
[Практическое руководство. Добавление вложения][]   
[Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]   
[Практическое руководство. Использование дополнительных служб SendGrid][]   
[Дальнейшие действия][]

<h2><a name="whatis"></a>Что такое служба электронной почты SendGrid?</h2>

SendGrid - это [облачная служба электронной почты], которая обеспечивает надежную
[доставку сервисных сообщений], масштабируемость и аналитику в режиме реального времени с гибкими интерфейсами API,
облегчающими пользовательскую интеграцию. Распространенные сценарии использования SendGrid
включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам.
-   Администрирование списков рассылки для ежемесячной отправки клиентам
    электронных листовок и специальных предложений.
-   Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и
    реагирование клиентов.
-   Создание отчетов для определения тенденций.
-   Пересылка запросов клиентов.

Дополнительные сведения см. на веб-сайте [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Создание учетной записи SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Справочник по библиотеке классов SendGrid .NET</h2>

[Пакет NuGet для SendGrid](https://www.nuget.org/packages/Sendgrid) является самым простым способом получить доступ к интерфейсу API SendGrid и настроить для приложения все зависимости. Пакет NuGet - это расширение Visual Studio, включенное в Microsoft Visual Studio 2012, которое упрощает установку и обновление библиотек и инструментов. 

<div class="dev-callout">
<b>Примечание.</b>
<p>Чтобы
установить пакет NuGet, если вы используете версию Visual Studio ниже Visual Studio 2012, посетите веб-сайт <a href="http://www.nuget.org">http://www.nuget.org</a> и нажмите кнопку <b>Установить
NuGet</b>.</p>
</div>

Для установки пакета SendGrid NuGet в приложении выполните следующие действия:

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки**, затем выберите команду
    **Управление пакетами NuGet**.

2.  На левой панели диалогового окна **Управление пакетами NuGet** щелкните пункт **В сети**.

3.  Запустите поиск **SendGrid** и из списка результатов выберите элемент **SendGrid**
    .

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Нажмите кнопку **Установить**, чтобы закончить установку, а затем закройте это
    диалоговое окно.

Библиотека классов SendGrid's .NET называется **SendGridMail**. Она содержит
следующие пространства имен:

-   **SendGridMail** - для создания элементов почты и работы с ними.
-   **SendGridMail.Transport** - для отправки электронных сообщений с помощью
    протокола **SMTP** или протокола HTTP 1.1 с **Web/REST**.

Добавьте следующие объявления пространств имен в начало любого файла C\#, в котором вы собираетесь получать доступ к хранилищу службе электронной почте SendGrid программным способом: **System.Net** и **System.Net.Mail** являются пространствами имен .NET. Они включены в список, поскольку содержат часто используемые типы для интерфейсов API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Практическое руководство. Создание электронного сообщения</h2>

Используйте статический метод **SendGrid.GetInstance** для создания электронного сообщения типа **SendGrid**. После создания сообщения с помощью свойств и методов **SendGrid** можно задать различные значения, включая отправителя и получателя электронного сообщения, а также его темы и содержимого.

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

Дополнительную информацию обо всех свойствах и методах, поддерживаемых типом **SendGrid**, см. в разделе [sendgrid-csharp][] на веб-сайте GitHub.

<h2><a name="sendemail"></a>Практическое руководство. Отправка электронного сообщения</h2>

После создания электронного сообщения его можно отправить с помощью веб-интерфейса API, предоставленного SendGrid.. Также можно воспользоваться встроенной библиотекой .NET](https://sendgrid.com/docs/Code_Examples/csharp.html).

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

The following examples show how to send a message using the Web API.

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

Можно добавлять вложения в сообщение, вызывая метод **AddAttachment** и указывая имя и путь к файлу, который следует вложить. Можно включить несколько вложений, вызвав этот метод по одному разу для каждого вкладываемого файла. В следующем примере показано, как добавлять вложения в сообщение.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
Также для добавления вложений можно воспользоваться **потоковой передачей** данных. Для этого вызовите указанный выше метод, выберите **AddAttachment**, но при этом передайте в него поток данных, а затем укажите имя файла, которое нужно показать в виде сообщения.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

<h2><a name="usefilters"></a>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики</h2>

SendGrid поддерживает дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это определенные параметры, которые можно добавить в электронное сообщение для включения определенных функций, таких как включения отслеживания щелчков, аналитики Google, отслеживание подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][].

Фильтры могут применяться к электронным сообщениям **SendGrid** с помощью методов, реализованных в рамках класса **SendGrid**.

В следующих примерах показаны фильтры нижних колонтитулов и отслеживания щелчков.

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

SendGrid поддерживает различные веб-API, с помощью которых вы можете использовать дополнительные функции SendGrid, доступные в приложении Azure. Дополнительные сведения см. в [документации по интерфейсу API SendGrid][].

<h2><a name="nextsteps"></a>Дальнейшие действия</h2>

Вы познакомились с основами службы доставки электронной почты SendGrid. Для получения дополнительных сведений перейдите по следующим ссылкам:

* Репозиторий библиотеки C# для SendGrid: [sendgrid-csharp][]
*   Документация по интерфейсу API SendGrid: <https://sendgrid.com/docs>
*   Специальное предложение SendGrid для клиентов Azure: [https://sendgrid.com](https://sendgrid.com)

  [Дальнейшие действия]: #nextsteps
  [Что такое служба электронной почты SendGrid?]: #whatis
  [Создание учетной записи SendGrid]: #createaccount
  [Справочник по библиотеке классов SendGrid .NET]: #reference
  [Практическое руководство. Создание электронного сообщения]: #createemail
  [Практическое руководство. Отправка сообщения электронной почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  
  
  [специальное предложение]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
