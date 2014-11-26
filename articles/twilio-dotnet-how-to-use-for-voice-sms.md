<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Использование Twilio для поддержки голосовых вызовов и SMS в Azure

В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое Twilio?][Что такое Twilio?]
-   [Цены на Twilio][Цены на Twilio]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи Twilio][Создание учетной записи Twilio]
-   [Проверка телефонных номеров][Проверка телефонных номеров]
-   [Создание приложения Azure][Создание приложения Azure]
-   [Настройка приложения для использования библиотек Twilio][Настройка приложения для использования библиотек Twilio]
-   [Практическое руководство. Осуществление исходящего вызова][Практическое руководство. Осуществление исходящего вызова]
-   [Практическое руководство. Отправка SMS-сообщения][Практическое руководство. Отправка SMS-сообщения]
-   [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]
-   [Практическое руководство. Использование дополнительных служб Twilio][Практическое руководство. Использование дополнительных служб Twilio]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="WhatIs"></span></a> Что такое Twilio?

Twilio создает новые возможности для бизнес-коммуникаций, позволяя разработчикам встраивать в приложения функции голосовой связи, VoIP и обмена сообщениями. Они виртуализируют всю необходимую инфраструктуру в облачной глобальной среде с предоставлением доступа через коммуникационную API платформу Twilio. Приложения отличаются простотой создания и масштабирования. Оцените гибкость повременной оплаты, а также преимущества, связанные с надежностью облачных решений.

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет выполнять VoIP звонки с любого телефона, планшета или из браузера, а также поддерживает WebRTC.

## <span id="Pricing"></span></a>Цены и специальные предложения Twilio

Клиентам Azure доступно [специальное предложение][специальное предложение]: дополнительный кредит Twilio в размере 10 долл. США при обновлении учетной записи Twilio. Этот кредит Twilio применяется к любым сценариям использования Twilio (кредит в размере 10 $ позволяет отправить 1000 SMS-сообщений или получать входящие голосовые вызовы продолжительностью до 1000 минут в зависимости от расположения телефонного номера, а также от направления отправки сообщения или совершения звонка). Получите этот кредит Twilio и приступите к работе на [ahoy.twilio.com/azure][ahoy.twilio.com/azure].

Twilio представляет собой службу с оплатой по мере использования. Стартовые платежи отсутствуют, а учетную запись можно закрыть в любое время. Дополнительные сведения см. в разделе [Цены на Twilio][1].

## <span id="Concepts"></span></a>Основные понятия

Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки API Twilio][Библиотеки API Twilio].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

### <span id="Verbs"></span></a>Команды Twilio

В API используются команды Twilio: например, команда **Say** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова.

Ниже приведен список команд Twilio. Дополнительные сведения о других командах и возможностях см. в [документации по языку разметки Twilio][документации по языку разметки Twilio].

-   **\<Dial\>**: подключение вызывающего абонента к другому телефону.
-   **\<Gather\>**: сбор цифр, введенных на клавиатуре телефона.
-   **\<Hangup\>**: окончание вызова.
-   **\<Play\>**: воспроизведение звукового файла.
-   **\<Pause\>**: автоматическое ожидание в течение указанного времени (в секундах).
-   **\<Record\>**: запись голоса вызывающего абонента и возврат URL-адреса файла, содержащего запись.
-   **\<Redirect\>**: передача управления для вызова или SMS в TwiML по другому URL-адресу.
-   **\<Reject\>**: отклонение входящего вызова на ваш номер Twilio без выставления вам счета.
-   **\<Say\>**: преобразование текста в речь в вызове.
-   **\<Sms\>**: отправка SMS-сообщения.

### <span id="TwiML"></span></a>TwiML

TwiML — это набор инструкций на основе XML и с использованием команд Twilio, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Когда приложение вызывает Twilio API, одним из параметров API является URL-адрес, который возвращает ответ TwiML. Для целей разработки можно использовать URL-адреса из Twilio для предоставления ответов TwiML, используемых приложениями. Также может разместить свои собственные URL-адреса для получения ответов TwiML; другой вариант — использовать объект **TwiMLResponse**.

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. в разделе [TwiML][документации по языку разметки Twilio]. Дополнительные сведения об API Twilio см. в разделе [API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Создание учетной записи Twilio

После подготовки к получению учетной записи Twilio зарегистрируйтесь на странице [Попробуйте Twilio][Попробуйте Twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio, вы получите идентификатор учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор учетной записи и маркер проверки подлинности отображаются на [странице учетной записи Twilio][странице учетной записи Twilio] в полях **КОД БЕЗОПАСНОСТИ УЧЕТНОЙ ЗАПИСИ** и **МАРКЕР ПРОВЕРКИ ПОДЛИННОСТИ** соответственно.

## <span id="VerifyPhoneNumbers"></span></a>Проверка телефонных номеров

Различные номера телефонов для вашей учетной записи должны быть проверены с помощью Twilio. Например, если вы хотите совершить исходящие телефонные звонки, номер телефона должен проверяться в Twilio как идентификатор объекта, выполняющего исходящий вызов. Аналогичным образом если требуется номер телефона для получения SMS-сообщений, номер получающего телефона должен быть проверен в Twilio. Сведения о том, как проверить номер телефона, см. в разделе [Управление номерами][Управление номерами]. В следующем коде указываются номера телефонов, которые нужно будет проверить в Twilio.

Вместо использования для приложений существующего номера можно приобрести телефонный номер Twilio. Сведения о приобретении телефонного номера Twilio см. в разделе [Справка по телефонным номерам Twilio][Справка по телефонным номерам Twilio]

## <span id="create_app"></span></a>Создание приложения Azure

Приложение Azure, на котором размещается приложение с поддержкой Twilio, ничем не отличается от любого другого приложения Azure. Необходимо просто добавить библиотеку Twilio .NET и настроить роль для использования библиотек Twilio .NET.
 Сведения о создании начального проекта Azure см. в разделе [Создание проекта Azure с помощью Visual Studio][Создание проекта Azure с помощью Visual Studio].

## <span id="configure_app"></span></a>Настройка приложения для использования библиотек Twilio

Twilio предоставляет набор вспомогательных библиотек .NET, содержащих различные аспекты Twilio для предоставления простых и легких способов взаимодействия с Twilio REST API и Twilio Client для создания ответов TwiML.

Twilio предоставляет пять библиотек для разработчиков .NET:

<table border="1">

<tr>

<th>
Библиотека

</th>

<th>
Описание

</th>

</tr>

<tr>

<td>
Twilio.API

</td>

<td>
Основная библиотека Twilio, реализующая интерфейс API REST Twilio в виде понятной библиотеки .NET. Эта библиотека доступна для .NET, Silverlight и Windows Phone 7.

</td>

</tr>

<tr>

<td>
Twilio.TwiML

</td>

<td>
Позволяет создавать разметку TwiML удобным для .NET способом.

</td>

</tr>

<tr>

<td>
Twilio.MVC

</td>

<td>
Для разработчиков, использующих ASP.NET MVC, эта библиотека включает в себя TwilioController, TwiML ActionResult и атрибут проверки запроса.

</td>

</tr>

<tr>

<td>
Twilio.WebMatrix

</td>

<td>
Для разработчиков, использующих бесплатное средство разработки WebMatrix от Microsoft, эта библиотека содержит помощники синтаксиса Razor для выполнения различных действий Twilio.

</td>

</tr>

<tr>

<td>
Twilio.Client.Capability

</td>

<td>
Содержит генератор маркера "Возможность" для использования с Twilio Client JavaScript SDK.

</td>

</tr>

</table>
</p>
Обратите внимание, что для всех библиотек требуется .NET 3.5, Silverlight 4 и Windows Phone 7 или более поздней версии.

Примеры, приведенные в этом руководстве, используют библиотеку Twilio.API.

Библиотеки могут быть [установлены с помощью расширения диспетчера пакетов NuGet][установлены с помощью расширения диспетчера пакетов NuGet] для Visual Studio 2010 и 2012. Исходный код находится на [GitHub][GitHub], который включает в себя вики-узел, содержащий полную документацию по использованию библиотек.

По умолчанию Microsoft Visual Studio 2010 устанавливает NuGet версии 1.2. Для установки библиотек Twilio требуется версия NuGet 1.6 или выше. Дополнительные сведения об установке или обновлении NuGet см. на сайте [][]<http://nuget.org/></a>.

<div class="dev-callout">
<b>Примечание.</b>
<p>Чтобы установить последнюю версию NuGet, сначала необходимо удалить загруженную версию, используя диспетчер расширений Visual Studio. Для этого необходимо запустить Visual Studio от имени администратора. В противном случае кнопка &laquo;Удалить&raquo; будет неактивна.</p>
</div>

### <span id="use_nuget"></span></a>Чтобы добавить библиотеки Twilio в проект Visual Studio:

1.  Откройте решение в Visual Studio.
2.  Щелкните правой кнопкой мыши **References** (Ссылки).
3.  Щелкните **Manage NuGet Packages...** (Управление пакетами Nuget...)
4.  Щелкните **Online** (В сети).
5.  В поле поиска online введите *twilio*.
6.  Щелкните **Install** (Установить) на пакете Twilio.

## <span id="howto_make_call"></span></a>Практическое руководство. Осуществление исходящего вызова

Далее показано, как осуществлять исходящий вызов с использованием класса **TwilioRestClient**. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

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

Дополнительные сведения о параметрах, передаваемых в метод **client.InitiateOutboundCall**, см. по адресу [][2]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Вместо этого можно использовать собственный веб-сайт для предоставления ответа TwiML. Дополнительные сведения см. в разделе [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]

## <span id="howto_send_sms"></span></a>Практическое руководство. Отправка SMS-сообщения

На следующем снимке экрана показано, как отправить SMS-сообщение с использованием класса **TwilioRestClient**. С целью отправки SMS-сообщений для пробных учетных записей номер **From** (От) предоставляется Twilio. Номер **To** (Кому) для учетной записи Twilio необходимо проверить перед выполнением кода.

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

## <span id="howto_provide_twiml_responses"></span></a>Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта

Когда приложение инициирует вызов API Twilio — например, с использованием метода **client.InitiateOutboundCall** — Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. Пример находится в [Практическом руководстве: Осуществление исходящего звонка][Практическое руководство. Осуществление исходящего вызова] для возврата ответа использует предоставленный Twilio URL-адрес [][3]<http://twimlets.com/message></a>.

<div class="dev-callout">
<b>Примечание.</b>
<p>Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, щелкните [http://twimlets.com/message](http://twimlets.com/message) для просмотра пустого элемента &lt;Response&gt;; в качестве другого примера щелкните [http://twimlets.com/message?Message%5B0%5D=Hello%20World](http://twimlets.com/message?Message%5B0%5D=Hello%20World) для просмотра элемента &lt;Response&gt;, содержащего элемент &lt;Say&gt;.</p>
</div>

Вместо того, чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный URL-адрес для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает HTTP-ответы. В этом разделе предполагается, что URL-адрес будет размещаться из универсального обработчика ASP.NET.

Следующий обработчик ASP.NET создает ответ TwiML **Hello World** на вызов.

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

Дополнительные сведения о TwiML см. на странице [][4]<https://www.twilio.com/docs/api/twiml></a>.

После настройки способа предоставления ответов TwiML можно передать этот URL-адрес в метод **client.InitiateOutboundCall**. Например, если у вас есть веб-приложение с именем MyTwiML, развернутое в облачной службе Azure, а обработчик ASP.NET использует имя mytwiml.ashx, URL-адрес может быть передан в **client.InitiateOutboundCalll**, как показано в следующем примере кода:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://your_hosted_service.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);

Дополнительные сведения об использовании Twilio в Azure с ASP.NET см. в разделе [Осуществление телефонных звонков с использованием Twilio в веб-роли Azure][Осуществление телефонных звонков с использованием Twilio в веб-роли Azure].

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [Дальнейшие действия]: #NextSteps
  [Цены на Twilio]: #Pricing
  [Основные понятия]: #Concepts
  [Создание учетной записи Twilio]: #CreateAccount
  [Проверка телефонных номеров]: #VerifyPhoneNumbers
  [Создание приложения Azure]: #create_app
  [Настройка приложения для использования библиотек Twilio]: #configure_app
  [Практическое руководство. Осуществление исходящего вызова]: #howto_make_call
  [Практическое руководство. Отправка SMS-сообщения]: #howto_send_sms
  [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]: #howto_provide_twiml_responses
  [Практическое руководство. Использование дополнительных служб Twilio]: #AdditionalServices
  [специальное предложение]: http://www.twilio.com/azure
  [ahoy.twilio.com/azure]: http://ahoy.twilio.com/azure
  [1]: http://www.twilio.com/voice/pricing
  [Библиотеки API Twilio]: https://www.twilio.com/docs/libraries
  [документации по языку разметки Twilio]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Попробуйте Twilio]: https://www.twilio.com/try-twilio
  [странице учетной записи Twilio]: https://www.twilio.com/user/account
  [Управление номерами]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Справка по телефонным номерам Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Создание проекта Azure с помощью Visual Studio]: http://msdn.microsoft.com/ru-ru/library/windowsazure/ee405487.aspx
  [установлены с помощью расширения диспетчера пакетов NuGet]: http://www.twilio.com/docs/csharp/install
  [GitHub]: https://github.com/twilio/twilio-csharp
  []: http://nuget.org/
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://twimlets.com/message
  [4]: https://www.twilio.com/docs/api/twiml
  [Осуществление телефонных звонков с использованием Twilio в веб-роли Azure]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/
