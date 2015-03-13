<properties 
	pageTitle="Использование Twilio для поддержки голосовых вызовов и SMS (.NET) — Azure" 
	description="Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры программного кода написаны в .NET." 
	services="" 
	documentationCenter=".net" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="MicrosoftHelp@twilio.com"/>




<h1>Использование Twilio для поддержки голосовых вызовов и SMS в Azure</h1>

В этом руководстве демонстрируется выполнение типовых задач программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и сообщения SMS в приложениях см. в разделе [Дальнейшие действия](#NextSteps).

<h2>Оглавление</h2>
* [Что такое Twilio?](#WhatIs)
* [Цены на Twilio](#Pricing)
* [Основные понятия](#Concepts)
* [Создание учетной записи Twilio](#CreateAccount)
* [Проверка телефонных номеров](#VerifyPhoneNumbers)
* [Создание приложения Azure](#create_app)
* [Настройка приложения для использования библиотек Twilio](#configure_app)
* [Практическое руководство. Осуществление исходящего вызова](#howto_make_call)
* [Практическое руководство. Отправка SMS-сообщения](#howto_send_sms)
* [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта](#howto_provide_twiml_responses)
* [Практическое руководство. Использование дополнительных служб Twilio](#AdditionalServices)
* [Дальнейшие действия](#NextSteps)

<h2><a id="WhatIs"></a>Что такое Twilio?</h2>
Twilio создает новые возможности для бизнес-коммуникаций, позволяя разработчикам встраивать в приложения функции голосовой связи, VoIP и обмена сообщениями. Они виртуализируют всю необходимую инфраструктуру в облачной глобальной среде с предоставлением доступа через коммуникационную API платформу Twilio. Приложения отличаются простотой создания и масштабирования. Оцените гибкость повременной оплаты, а также преимущества, связанные с надежностью облачных решений.

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Клиент Twilio** позволяет выполнять VoIP звонки с любого телефона, планшета или из браузера, а также поддерживает WebRTC.

<h2><a id="Pricing"></a>Цены и специальные предложения Twilio</h2>
Клиентам Azure доступно [специальное предложение](http://www.twilio.com/azure): дополнительный кредит Twilio в размере 10 долл. США при обновлении учетной записи Twilio. Этот кредит Twilio применяется к любым сценариям использования Twilio (кредит в размере 10 $ позволяет отправить 1000 SMS-сообщений или получать входящие голосовые вызовы продолжительностью до 1000 минут в зависимости от расположения телефонного номера, а также от направления отправки сообщения или совершения звонка). Получите этот кредит Twilio и приступайте к работе на [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio представляет собой службу с повременной оплатой. Стартовые платежи отсутствуют, а учетную запись можно закрыть в любое время. Дополнительные сведения см. в разделе [Цены на Twilio](http://www.twilio.com/voice/pricing).  

<h2><a id="Concepts"></a>Основные понятия</h2>
Twilio API - это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки интерфейса API Twilio] [twilio_libraries].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

<h3><a id="Verbs"></a>Команды Twilio</h3>
API-интерфейс использует команды Twilio: например, команда **&lt;Say&gt;** предписывает Twilio воспроизвести сообщение при звонке. 

Ниже приведен список команд Twilio.  Дополнительные сведения о других командах и возможностях см. в [документации по языку разметки Twilio](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: подключение вызывающего абонента к другому телефону.
* **&lt;Gather&gt;**: сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;**: окончание вызова.
* **&lt;Play&gt;**: воспроизведение звукового файла.
* **&lt;Pause&gt;**: бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Record&gt;**: запись голоса вызывающего абонента и возврат URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;**: передача управления для вызова или SMS в TwiML по другому URL-адресу.
* **&lt;Reject&gt;**: отклонение входящего вызова на ваш номер Twilio без выставления счета.
* **&lt;Say&gt;**: преобразование текста в речь в вызове.
* **&lt;Sms&gt;**: отправка SMS-сообщения.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML - это набор инструкций на основе XML и с использованием команд Twilio, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Когда приложение вызывает API-интерфейс Twilio, одним из параметров API является URL-адрес, который возвращает ответ TwiML. Для целей разработки можно использовать URL-адреса из Twilio для предоставления ответов TwiML, используемых приложениями. Также можно разместить ваши собственные URL-адреса для получения ответов TwiML. Другой вариант - использовать объект **TwiMLResponse**.

Для получения дополнительных сведений о командах Twilo, их атрибутах и TwiML, см. [TwiML] [twiml]. Для получения дополнительных сведений об интерфейсах API Twilio, см. [Twilio API] [twilio_api].

<h2><a id="CreateAccount"></a>Создание учетной записи Twilio</h2>
Как только вы будете готовы создать учетную запись Twilio, зарегистрируйтесь на [Try Twilio] [try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio вы получите идентификатор учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Ваш идентификатор учетной записи и маркер проверки подлинности можно просмотреть на странице [Учетной записи Twilio] [twilio_account]в поле под названием **ИДЕНТИФИКАТОР БЕЗОПАСНОСТИ УЧЕТНОЙ ЗАПИСИ** и **МАРКЕР ПРОВЕРКИ ПОДЛИННОСТИ** соответственно.

<h2><a id="VerifyPhoneNumbers"></a>Проверка телефонных номеров</h2>
Различные номера телефонов для вашей учетной записи должны быть проверены с помощью Twilio. Например, если вы хотите совершить исходящие телефонные звонки, номер телефона должен проверяться в Twilio как идентификатор объекта, выполняющего исходящий вызов. Аналогичным образом если требуется номер телефона для получения SMS-сообщений, номер получающего телефона должен быть проверен в Twilio. Сведения о проверке номера телефона см. в разделе [Управление номерами] [verify_phone]. В следующем коде указываются номера телефонов, которые нужно будет проверить в Twilio.

Вместо использования для приложений существующего номера можно приобрести телефонный номер Twilio. Сведения о приобретении телефонного номера Twilio см. в разделе [Справка по телефонным номерам Twilio](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Создание приложения Azure</h2>
Приложение Azure, на котором размещается приложение с поддержкой Twilio, ничем не отличается от любого другого приложения Azure. Просто добавьте библиотеку Twilio .NET и настройте в роли использование библиотек Twilio .NET.
Сведения о создании начального проекта Azure см. в разделе [Создание нового проекта Windows Azure с помощью][vs_project].

<h2><a id="configure_app"></a>Настройка приложения для использования библиотек Twilio</h2>
Twilio предоставляет набор вспомогательных библиотек .NET, содержащих различные аспекты Twilio для предоставления простых и легких способов взаимодействия с Twilio REST API и Twilio Client для создания ответов TwiML.

Twilio предоставляет пять библиотек для разработчиков .NET:
<table border="1">
    <tr>
        <th>Библиотека</th>
        <th>Описание</th>
    </tr>
    <tr>
        <td>Twilio.API</td>
        <td>Основная библиотека Twilio, реализующая интерфейс API REST Twilio в виде понятной библиотеки .NET. Эта библиотека доступна для .NET, Silverlight и Windows Phone 7.</td>
    </tr>
    <tr>
        <td>Twilio.TwiML</td>
        <td>Позволяет создавать разметку TwiML удобным для .NET способом.</td>
    </tr>
    <tr>
        <td>Twilio.MVC</td>
        <td>Для разработчиков, использующих ASP.NET MVC, эта библиотека включает в себя TwilioController, TwiML ActionResult и атрибут проверки запроса.</td>
    </tr>
    <tr>
        <td>Twilio.WebMatrix</td>
        <td>Для разработчиков, использующих бесплатное средство разработки WebMatrix от Microsoft, эта библиотека содержит помощники синтаксиса Razor для выполнения различных действий Twilio.</td>
    </tr>
    <tr>
        <td>Twilio.Client.Capability</td>
        <td>Содержит генератор маркера "Возможность" для использования с Twilio Client JavaScript SDK.</td>
    </tr>
</table>

Обратите внимание, что для всех библиотек требуется .NET 3.5, Silverlight 4 и Windows Phone 7 или более поздней версии.

В примерах, приведенных в этом руководстве, используется библиотека Twilio.API.

Библиотеки можно [установить с помощью расширения диспетчера пакетов NuGet](http://www.twilio.com/docs/csharp/install), доступного для версий Visual Studio 2010 и 2012.  Исходный код расположен по адресу: [GitHub][twilio_github_repo], там же есть ссылка на Википедию с полной документацией по использованию библиотек.

По умолчанию Microsoft Visual Studio 2010 устанавливает NuGet версии 1.2. Для установки библиотек Twilio требуется версия NuGet 1.6 или выше. Дополнительные сведения об установке или обновлении NuGet см. по адресу: [http://nuget.org/][nuget].

<div class="dev-callout">
<b>Примечание.</b>
<p>Чтобы установить последнюю версию NuGet, сначала необходимо удалить загруженную версию, используя диспетчер расширений Visual Studio. Для этого необходимо запустить Visual Studio от имени администратора. В противном случае кнопка "Удалить" будет неактивна.</p>
</div>

<h3><a id="use_nuget"></a>Чтобы добавить библиотеки Twilio в проект Visual Studio:</h3>

1.  Откройте решение в Visual Studio.
2.  Щелкните правой кнопкой мыши **Ссылки**.
3.   Щелкните **Управление пакетами NuGet...**
4.  Щелкните **Онлайн**.
5.  В поле поиска онлайн введите *twilio*.
6.  Щелкните **Установить** на пакете Twilio.


<h2><a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова</h2>
Далее показано, как сделать исходящий звонок с помощью класса **TwilioRestClient**. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

Для получения дополнительных сведений о параметрах, передаваемых в метод **client.InitiateOutboundCall**, см. раздел [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Как уже упоминалось, в этом коде также используется предоставляемый Twilio сайт для возврата ответа на языке TwiML. Вместо этого можно использовать собственный веб-сайт для предоставления ответа TwiML. Дополнительные сведения см. в разделе [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Практическое руководство. Отправка SMS-сообщения</h2>
На следующем снимке экрана показано, как отправить SMS-сообщение с помощью класса **TwilioRestClient**. С целью отправки SMS-сообщений для пробных учетных записей номер **From** (От) предоставляется Twilio. Необходимо, чтобы номер **Кому** был проверен для учетной записи Twilio до выполнения кода.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

<h2><a id="howto_provide_twiml_responses"></a>Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта</h2>
Когда приложение инициирует вызов API-интерфейса Twilio (например, с помощью метода **client.InitiateOutboundCall**), Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. Пример находится в [Практическом руководстве: При выполнении исходящего звонка](#howto_make_call) для возвращения ответа используется URL-адрес [http://twimlets.com/message][twimlet_message_url], предоставленный Twilio. 

<div class="dev-callout">
<b>Примечание.</b>
<p>Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, нажмите [http://twimlets.com/message](twimlet_message_url) чтобы увидеть элемент пустой &lt;Response&gt;. Другой пример: нажмите [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world), чтобы увидеть элемент &lt;Response&gt;, содержащий элемент &lt;Say&gt;.</p>
</div>

Вместо того чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный URL-адрес для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает HTTP-ответы. В этом разделе предполагается, что URL-адрес будет размещаться из универсального обработчика ASP.NET.

Следующий обработчик ASP.NET создает ответ TwiML, который отвечает **Hello World** на вызов.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Как видно из приведенного выше примера, ответ TwiML будет представлять собой простой XML-документ. Библиотека Twilio.TwiML содержит классы, которые создадут для вас TwiML. В приведенном ниже примере выдается такой же ответ, как и в предыдущем примере, однако с использованием класса TwilioResponse.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Дополнительные сведения о TwiML см. по адресу: [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

После настройки способа предоставления ответов TwiML можно передать этот URL-адрес в метод **client.InitiateOutboundCall**. Например, если у вас есть веб-приложение с именем MyTwiML, развертывание которого выполнено в облачной службе Azure, а обработчик ASP.NET использует имя mytwiml.ashx, URL-адрес может быть передан в **client.InitiateOutboundCall**, как показано в следующем примере кода:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);


Дополнительные сведения об использовании Twilio в Azure с ASP.NET см. в разделе [Осуществление телефонных звонков с использованием Twilio в веб-роли на Azure][howto_phonecall_dotnet].

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]





[howto_phonecall_dotnet]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/



[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp



[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

<!--HONumber=46--> 
