<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Как отправлять электронную почту с помощью SendGrid и Azure

Последнее обновление: 21 августа 2014 г.

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры написаны на языке C# и используют API .NET. Описываемые здесь сценарии включают в себя **создание электронного сообщения**, **отправку электронного сообщения**, **добавление вложений** и **использование фильтров**. Дополнительные сведения о SendGrid и отправке эл. почты см. в разделе [Дальнейшие действия][Дальнейшие действия].

## <a name="toc"></a>Оглавление

[Что такое служба электронной почты SendGrid?][Что такое служба электронной почты SendGrid?]
[Создание учетной записи SendGrid][Создание учетной записи SendGrid]
[Справочник по библиотеке классов SendGrid .NET][Справочник по библиотеке классов SendGrid .NET]
[Практическое руководство. Создание электронного сообщения][Практическое руководство. Создание электронного сообщения]
[Практическое руководство. Отправка электронного сообщения][Практическое руководство. Отправка электронного сообщения]
[Практическое руководство. Добавление вложения][Практическое руководство. Добавление вложения]
[Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]
[Практическое руководство. Использование дополнительных служб SendGrid][Практическое руководство. Использование дополнительных служб SendGrid]
[Дальнейшие действия][Дальнейшие действия]

## <a name="whatis"></a><span class="short-header">Что такое служба электронной почты SendGrid?</span>

SendGrid — это [облачная служба электронной почты][облачная служба электронной почты], обеспечивающая надежные возможности [доставки электронной почты][доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам.
-   Администрирование списков рассылки для ежемесячной
    отправки клиентам электронных рекламных листовок и специальных предложений.
-   Сбор данных метрик в режиме реального времени по таким параметрам, как заблокированная электронная почта и
    реагирование клиентов.
-   Создание отчетов для определения тенденций.
-   Пересылка запросов клиентов.

Дополнительные сведения см. на веб-сайте [][]<http://sendgrid.com></a>.

## <a name="createaccount"></a><span class="short-header">Создание учетной записи SendGrid</span>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a><span class="short-header">Справочник по библиотеке классов SendGrid .NET</span>Справочник по библиотеке классов SendGrid .NET

[Пакет NuGet для SendGrid][Пакет NuGet для SendGrid] является самым простым способом получить доступ к интерфейсу API SendGrid и настроить для приложения все зависимости. Пакет NuGet является расширением Visual Studio, включенным в Microsoft Visual Studio 2012, которое упрощает установку и обновление библиотек и инструментов.

<div class="dev-callout">
<b>Примечание.</b>
<p>Чтобы
установить пакет NuGet, если вы используете версию Visual Studio ниже Visual Studio 2012, посетите веб-сайт <a href="http://www.nuget.org">http://www.nuget.org</a> и нажмите кнопку <b>Установить NuGet</b>.</p>
</div>

Для установки пакета SendGrid NuGet в приложении выполните следующие действия:

1.  В окне **Обозреватель решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите команду **Управление пакетами NuGet**.

2.  На левой панели диалогового окна **Управление пакетами NuGet** щелкните пункт **В сети**.

3.  Выполните поиск **SendGrid** и выберите элемент **SendGrid** в списке результатов.

    ![Пакет SendGrid NuGet][Пакет SendGrid NuGet]

4.  Нажмите кнопку **Установить**, чтобы завершить установку, а затем закройте это диалоговое окно.

Библиотека классов SendGrid .NET называется **SendGridMail**. Она содержит следующие пространства имен:

-   **SendGridMail**: предназначено для создания элементов почты и работы с ними.
-   **SendGridMail.Transport**: предназначено для отправки электронных сообщений с помощью протокола
    **SMTP** или протокола HTTP 1.1 с **Web/REST**.

Добавьте следующие объявления пространств имен в начало кода любого файла, созданного на языке C#, в котором следует обеспечить программный доступ к почтовой службе SendGrid. **System.Net** и **System.Net.Mail** являются пространствами имен платформы .NET Framework. Они включены в библиотеку классов, потому что содержат типы, часто используемые интерфейсами API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header">Практическое руководство. Создание электронного сообщения</span>Практическое руководство. Создание электронного сообщения

Используйте статический метод **SendGrid.GetInstance** для создания электронного сообщения типа **SendGrid**. После создания сообщения с помощью свойств и методов **SendGrid** можно задать различные значения, включая отправителя и получателя электронного сообщения, а также его темы и содержимого.

В следующем примере показано, как создать полностью заполненный объект электронной почты:

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

Дополнительную информацию о всех свойствах и методах, поддерживаемых типом **SendGrid**, см. в разделе [sendgrid-csharp][sendgrid-csharp] на веб-сайте GitHub.

## <a name="sendemail"></a><span class="short-header">Практическое руководство. Отправка электронного сообщения</span>

После создания электронного сообщения его можно отправить с помощью веб-API, предоставленного SendGrid. Также можно [воспользоваться встроенной библиотекой .NET][воспользоваться встроенной библиотекой .NET].

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
    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

## <a name="addattachment"></a><span class="short-header">Практическое руководство. Добавление вложения</span>Практическое руководство. Добавление вложения

Чтобы добавить вложения в сообщение, вызовите метод **AddAttachment** и укажите имя нужного файла и путь к нему. Для добавления нескольких вложений этот метод следует вызывать для каждого из файлов, которые требуется добавить. В следующем примере показано, как добавить вложение в сообщение:

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

## <a name="usefilters"></a><span class="short-header">Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики</span>

SendGrid поддерживает дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это определенные параметры, которые могут быть добавлены в электронное сообщение для включения определенных функций, таких как включения отслеживания щелчков, аналитики Google, отслеживание подписок и т. д. Полный список фильтров см. в разделе [Параметры фильтров][Параметры фильтров].

Фильтры могут применяться к электронным сообщениям **SendGrid** с помощью методов, реализованных в рамках класса **SendGrid**.

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
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="useservices"></a><span class="short-header">Практическое руководство. Использование дополнительных служб SendGrid</span>

SendGrid поддерживает различные веб-API, с помощью которых вы можете использовать дополнительные функции SendGrid, доступные в приложении Azure. Дополнительную информацию см. в [документации по интерфейсу API SendGrid][документации по интерфейсу API SendGrid].

## <a name="nextsteps"></a><span class="short-header">Дальнейшие действия </span>Дальнейшие действия

Вы познакомились с основами службы доставки электронной почты SendGrid. Для получения дополнительных сведений используйте следующие ссылки.

-   Репозиторий библиотеки C# для SendGrid: [sendgrid-csharp][sendgrid-csharp]
-   Документация по интерфейсу SendGrid API: <http://docs.sendgrid.com/documentation/api/>
-   Специальное предложение SendGrid для клиентов Azure: [][]<http://sendgrid.com></a>

  [Дальнейшие действия]: #nextsteps
  [Создание учетной записи SendGrid]: #createaccount
  [Справочник по библиотеке классов SendGrid .NET]: #reference
  [Практическое руководство. Создание электронного сообщения]: #createemail
  [Практическое руководство. Отправка электронного сообщения]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставки электронной почты]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [Пакет NuGet для SendGrid]: https://www.nuget.org/packages/Sendgrid
  [Пакет SendGrid NuGet]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [воспользоваться встроенной библиотекой .NET]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [Параметры фильтров]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [документации по интерфейсу API SendGrid]: http://docs.sendgrid.com/documentation/api/
