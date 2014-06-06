<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="Служба эл. почты SendGrid" pageTitle="Как использовать службу эл. почты SendGrid (.NET) – Azure" metaKeywords="Azure SendGrid, служба эл. почты Azure, Azure SendGrid .NET, эл. почта Azure .NET, Azure SendGrid C#, эл. почта Azure C#" description="Как отправлять сообщения электронной почты с помощью службы электронной почты SendGrid в Azure. Примеры кода, написанные на C# и использующие интерфейс .NET API." metaCanonical="" services="" documentationCenter=".NET" title="Как отправлять электронную почту с помощью SendGrid и Azure." authors=""  solutions="" writer="" manager="" editor=""  />





# Как отправлять электронную почту с помощью SendGrid и Azure.

В этом руководстве показано, как выполнять типовые задачи программирования для службы электронной почты SendGrid в Azure. Примеры написаны на C\# с использованием интерфейса .NET API. Описываемые здесь сценарии включают **создание сообщения эл. почты**, **отправку сообщения эл. почты**, **добавление вложений** и **использование фильтров**. Дополнительные сведения о SendGrid и отправке эл. почты см. в разделе
[Дальнейшие действия][].

<h2><a name="toc"></a>Оглавление</h2>

[Что такое служба электронной почты SendGrid?][]   
[Создание учетной записи SendGrid][]   
[Справочник по библиотеке классов SendGrid .NET][]   
[Практическое руководство. Создание сообщения эл. почты][]   
[Практическое руководство. Отправка сообщения эл. почты][]   
[Практическое руководство. Добавление вложения][]   
[Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики][]   
[Практическое руководство. Использование дополнительных служб SendGrid][]   
[Дальнейшие действия][]

<h2><a name="whatis"></a><span  class="short-header">Что такое служба электронной почты SendGrid?</span>Что такое служба электронной почты SendGrid?</h2>

SendGrid – это [облачная служба электронной почты], которая предоставляет надежные возможности
[доставки электронной почты], масштабируемости и аналитики в режиме реального времени наряду с гибкими интерфейсами API, которые облегчают пользовательскую интеграцию. Распространенные сценарии использования SendGrid включают следующие:

-   Автоматическая отправка уведомлений о получении клиентам.
-   Администрирование списков рассылки для ежемесячной отправки клиентам электронных листовок и специальных предложений.
- Сбор показателей в режиме реального времени по таким параметрам, как заблокированная электронная почта и реагирование клиентов.
-   Создание отчетов для определения тенденций.
-   Пересылка запросов клиентов.

Дополнительные сведения см. в разделе [http://sendgrid.com](http://sendgrid.com).

<h2><a name="createaccount"></a><span  class="short-header">Создание учетной записи SendGrid</span>Создание учетной записи SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a><span  class="short-header">Ссылки на библиотеку классов SendGrid .NET</span>Ссылки на библиотеку классов SendGrid .NET</h2>

Пакет SendGrid NuGet является самым простым способом получить доступ к интерфейсу API SendGrid и настроить для приложения все зависимости. NuGet является расширением Visual Studio, включенным в Microsoft Visual Studio 2012, которое упрощает установки и обновление библиотек и средств. 

<div class="dev-callout">
<b>Примечание.</b>
<p>Чтобы установить NuGet при работе с версией Visual Studio ниже Visual Studio 2012, посетите <a href="http://www.nuget.org">http://www.nuget.org</a> и нажмите кнопку <b>Установить NuGet</b>.</p>
</div>

Для установки пакета SendGrid NuGet в приложении выполните следующие действия:

1.  В окне **Обозреватель решений** щелкните правой кнопкой мыши элемент **Ссылки** и выберите команду
    **Управление пакетами NuGet**.

2.  На левой панели диалогового окна **Управление пакетами NuGet** щелкните пункт **В сети**.

3.  Выполните поиск **SendGrid** и выберите элемент **SendGrid** в списке результатов.

    ![Пакет SendGrid NuGet][SendGrid-NuGet-package]

4.  Нажмите кнопку **Установить**, чтобы +закончить установку, затем закройте это диалоговое окно.

Библиотека классов SendGrid .NET называется **SendGridMail**. В ней содержатся следующие пространства имен:

-   **SendGridMail** для создания и работы с элементами почты.
-   **SendGridMail.Transport** для отправки сообщений электронной почты с помощью протокола
    **SMTP** или протокола HTTP 1.1 с **Web/REST**.

Добавьте следующие объявления пространств имен в начало любого файла C\#, в котором вы собираетесь получать доступ к хранилищу службе электронной почте SendGrid программным способом:
**System.Net** и **System.Net.Mail** являются пространствами имен платформы .NET Framework, которые включены сюда, потому что содержат типы, часто используемые интерфейсами SendGrid API.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

<h2><a name="createemail"></a><span  class="short-header">Практическое руководство. Создание сообщения эл. почты</span>Практическое руководство. Создание сообщения эл. почты</h2>

Используйте статический метод **SendGrid.GetInstance** для создания сообщения эл. почты, имеющего тип **SendGrid**. После создания сообщения можно использовать свойства и методы **SendGrid** для определения значений, включая отправителя электронной почты, получателя электронной почты, темы и основного текста электронной почты.

В следующем примере показано, как создать полностью заполненный объект электронной почты:

    // Настройка свойств электронной почты.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Создание сообщения электронной почты, передачи восьми свойств, как аргументов.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

В следующем примере показано, как создать пустой объект электронной почты:

    // Сначала создание объекта эл. почты, затем добавление свойств.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Добавление свойств сообщения.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Добавление несколько адресов в поле "Кому".
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
    };
     
    foreach (string recipient in recipients)
    {
        myMessage.AddTo(recipient);
    }
     
    // Добавление основного текста сообщения в формате HTML.
    myMessage.Html = "<p>Hello World!</p>";

    // Добавление темы.
    myMessage.Subject = "Testing the SendGrid Library";

Дополнительные сведения обо всех свойствах и методах, поддерживаемых типом
**SendGrid** см. в разделе [sendgrid-csharp][] на GitHub.

<h2><a name="sendemail"></a><span  class="short-header">Практическое руководство. Отправка сообщения эл. почты</span>Практическое руководство. Отправка сообщения эл. почты</h2>

После создания сообщения электронной почты, можно отправить его с помощью SMTP или Web API, предоставленных SendGrid Дополнительные сведения о преимуществах и недостатках каждого интерфейса API см. в разделе [SMTP и веб-интерфейс API][] в документации по SendGrid.

Отправка сообщения эл. почты по любому из этих протоколов нуждается в предоставлении учетных данных учетной записи SendGrid (имени пользователя и пароля). Следующий код демонстрирует, как поместить учетные данные в оболочку объекта **NetworkCredential**:

    // Создание сетевых учетных данных для доступа к учетной записи SendGrid.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

Для отправки сообщения эл. почты используйте метод **Deliver** для класса
**SMTP**, который использует протокол SMTP, или для класса транспорта **REST**, который вызывает веб-интерфейс SendGrid. На следующих примерах показано, как отправить сообщение с использованием и SMTP, и веб-интерфейса API.

### SMTP

    // Сначала создание объекта эл. почты, затем добавление свойств.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Создание учетных данных с указанием имени пользователя и пароля.
    var credentials = new NetworkCredential("username", "password");

    // Создание транспорта SMTP для отправки сообщения эл. почты.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Отправка сообщения эл. почты.
    transportSMTP.Deliver(myMessage);

### Веб-интерфейс API

    // Сначала создание объекта эл. почты, затем добавление свойств.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Создание учетных данных с указанием имени пользователя и пароля.
    var credentials = new NetworkCredential("username", "password");

    // Создание транспорта REST для отправки сообщения эл. почты.
    var transportREST = Web.GetInstance(credentials);

    // Отправка сообщения эл. почты.
    transportREST.Deliver(myMessage);

<h2><a name="addattachment"></a><span  class="short-header">Практическое руководство. Добавление вложения</span>Практическое руководство. Добавление вложения</h2>

Можно добавлять вложения в сообщение, вызывая метод **AddAttachment**
и указывая имя и путь к файлу, который следует вложить.
Можно включить несколько вложений, вызвав этот метод по одному разу для каждого вкладываемого файла. В следующем примере демонстрируется добавление вложения в сообщение.

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

<h2><a name="usefilters"></a><span  class="short-header">Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики</span>Практическое руководство. Использование фильтров для включения нижних колонтитулов, отслеживания и аналитики</h2>

SendGrid предоставляет дополнительные функциональные возможности электронной почты благодаря использованию фильтров. Это параметры, которые могут быть добавлены в сообщение электронной почты для включения определенных функций, например включения отслеживания щелчков, отслеживание подписок и т. д. Полный список фильтров см. в разделе
[Параметры фильтров][].

Фильтры могут применяться к сообщениям эл. почты **SendGrid** с использованием методов, реализованных в рамках класса **SendGrid**. Перед включением фильтров для сообщения эл. почты необходимо сначала инициализировать список доступных фильтров, вызвав метод **InitalizeFilters**.

В следующих примерах показаны фильтры нижних колонтитулов и отслеживания щелчков.

### Нижний колонтитул

    // Сначала создание объекта эл. почты, затем добавление свойств.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Добавление нижнего колонтитула в сообщение.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Отслеживание щелчков

    // Сначала создание объекта эл. почты, затем добавление свойств.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true указывает, что ссылки в простом тексте сообщения эл. почты
	// также будут перезаписаны в целях отслеживания ссылок. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a><span  class="short-header">Практическое руководство. Использование служб SendGrid</span>Практическое руководство. Использование служб SendGrid</h2>

SendGrid предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции SendGrid из вашего приложения Azure. Исчерпывающие сведения см. в документации по [интерфейсу SendGrid API][].

<h2><a name="nextsteps"></a><span  class="short-header">Дальнейшие действия</span>Дальнейшие действия</h2>

Вы узнали основные сведения о службе доставки электронной почты SendGrid. Для получения дополнительных сведений используйте следующие ссылки.

* Репозиторий библиотеки SendGrid C\#: [sendgrid-csharp][]
*   Документация по интерфейсу SendGrid API: <http://docs.sendgrid.com/documentation/api/>
*   Специальное предложение SendGrid для клиентов Azure: [http://sendgrid.com](http://sendgrid.com)

  [Дальнейшие действия]: #nextsteps
  [Что такое служба электронной почты SendGrid?]: #whatis
  [Создание учетной записи SendGrid]: #createaccount
  [Справочник по библиотеке классов SendGrid .NET]: #reference
  [Практическое руководство. Создание сообщения эл. почты]: #createemail
  [Практическое руководство. Отправка сообщения эл. почты]: #sendemail
  [Практическое руководство. Добавление вложения]: #addattachment
  [Практическое руководство. использование фильтров для включения нижних колонтитулов, отслеживания и аналитики]: #usefilters
  [Практическое руководство. Использование дополнительных служб SendGrid]: #useservices
  
  
  [специальное предложение]: http://www.sendgrid.com/azure.html
  
  
  
  [SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP и веб-интерфейс API]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [Параметры фильтров]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [интерфейсу SendGrid API]: http://docs.sendgrid.com/documentation/api/
  
  [облачная служба электронной почты]: http://sendgrid.com/solutions
  [доставки электронной почты]: http://sendgrid.com/transactional-email

