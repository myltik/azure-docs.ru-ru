<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />

# Использование Twilio для поддержки голосовых вызовов и SMS в мобильных службах

В этом разделе показано, как выполнять типовые задачи с использованием API Twilio для мобильных служб Azure. В этом учебнике рассказывается, как создавать пользовательские скрипты, использующие API Twilio для выполнения телефонного звонка и отправки текстового сообщения.

## <span id="WhatIs"></span></a> Что такое Twilio?

Twilio создает новые возможности для бизнес-коммуникаций, позволяя разработчикам встраивать в приложения функции голосовой связи, VoIP и обмена сообщениями. Они виртуализируют всю необходимую инфраструктуру в облачной глобальной среде с предоставлением доступа через коммуникационную API платформу Twilio. Приложения отличаются простотой создания и масштабирования. Оцените гибкость повременной оплаты, а также преимущества, связанные с надежностью облачных решений.

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет выполнять VoIP звонки с любого телефона, планшета или из браузера, а также поддерживает WebRTC.

## <span id="Pricing"></span></a>Цены и специальные предложения Twilio

Клиентам Azure доступно [специальное предложение][специальное предложение]: дополнительный кредит Twilio в размере 10 долл. США при обновлении учетной записи Twilio. Этот кредит Twilio применяется к любым сценариям использования Twilio (кредит в размере 10 $ позволяет отправить 1000 SMS-сообщений или получать входящие голосовые вызовы продолжительностью до 1000 минут в зависимости от расположения телефонного номера, а также от направления отправки сообщения или совершения звонка). Получите этот кредит Twilio и приступите к работе на [ahoy.twilio.com/azure][специальное предложение].

Twilio представляет собой службу с оплатой по мере использования. Стартовые платежи отсутствуют, а учетную запись можно закрыть в любое время. Дополнительные сведения см. в разделе [Цены на Twilio][Цены на Twilio].

## <span id="Concepts"></span></a>Основные понятия

Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки API Twilio][Библиотеки API Twilio]. Также доступны учебники по работе с Twilio в приложениях Azure, написанных на [.NET][.NET], [node.js][node.js], [Java][Java], [PHP][PHP], [Python][Python] и [Ruby][Ruby].

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

## <span id="create_app"></span></a>Создать мобильную службу

Мобильная служба, в которой размещается приложение с поддержкой Twilio, ничем не отличается от любой другой мобильной службы. После добавления библиотеки Twilio node.js вы можете задавать ссылки на нее из пользовательских скриптов API для мобильной службы. Дополнительные сведения о создании исходной мобильной службы см. в разделе [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <span id="VerifyPhoneNumbers"></span></a>Настройка мобильной службы для работы с библиотекой Twilio Node.js

Twilio предоставляет библиотеку Node.js, содержащих различные аспекты Twilio для предоставления простых и легких способов взаимодействия с API REST Twilio и Twilio Client для создания ответов TwiML.

Чтобы использовать библиотеку Twilio node.js в своей мобильной службе, вам необходимо реализовать поддержку модуля npm для мобильной службы, для чего можно сохранить скрипты в системе управления версиями. В учебнике [Хранение скриптов в системе управления версиями][Хранение скриптов в системе управления версиями] приводятся инструкции по настройке системы управления версиями при первом запуске мобильных служб, а также рекомендации по хранению серверных скриптов в репозитории Git.

После того, как вы настроили систему управления версиями для мобильной службы, откройте вкладку "Настройка" на панели мониторинга мобильной службы, после чего найдите и скопируйте URL-адрес Git

Вставьте этот URL-адрес в браузер и замените имя репозитория на */DebugConsole/index.html*

Например, замените:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

на:

    https://twilioSample.scm.azure-mobile.net/DebugConsole

При появлении запроса введите учетные данные, которые использовались при настройке системы управления версиями для службы. После входа в систему вы увидите консоль мобильных служб Azure.

![Консоль мобильных служб][Консоль мобильных служб]

В консоли замените каталог на папку скриптов:

    cd site\wwwroot\App_Data\config\scripts

В папке api вы можете установить модуль узла Twilio, выполнив следующую команду:

    npm install twilio

Теперь вы можете задавать ссылки и использовать библиотеку Twilio в собственных API и табличных скриптах.

## <span id="howto_make_call"></span></a>Практическое руководство. Осуществление исходящего вызова

Следующий скрипт демонстрирует выполнение исходящего звонка из мобильной службы с помощью функции **makeCall**. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

Дополнительные сведения о параметрах, передаваемых в функцию **client.makeCall**, см. по адресу [][]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Вместо этого можно использовать собственный веб-сайт для предоставления ответа TwiML. Дополнительные сведения см. в разделе [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]

## <span id="howto_send_sms"></span></a>Практическое руководство. Отправка SMS-сообщения

В следующем коде показано, как отправить SMS-сообщение с использованием функции **sendSms**. С целью отправки SMS-сообщений для пробных учетных записей номер **From** (От) предоставляется Twilio. Номер **To** (Кому) для учетной записи Twilio необходимо проверить перед выполнением кода.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {

            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };

## <span id="howto_provide_twiml_responses"></span></a>Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта

Когда приложение инициирует вызов API Twilio (например, с использованием метода client.InitiateOutboundCall), Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. Пример находится в практическом руководстве: осуществление исходящего звонка для возврата ответа использует предоставленный Twilio URL-адрес <http://twimlets.com/message>.

<div class="dev-callout">
<b>Примечание.</b>
<p>Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, нажмите <a href="http://twimlets.com/message">twimlet_message_url</a>, чтобы просмотреть пустой элемент &lt;Response&gt;. Также вы можете нажать <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a>, чтобы просмотреть элемент &lt;Response&gt;, содержащий команду &lt;Say&gt;.</p>
</div>

Вместо того, чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный URL-адрес для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает HTTP-ответы. В этом разделе предполагается, что URL-адрес будет размещаться из универсального обработчика ASP.NET.

Следующий скрипт создает ответ TwiML, который позволяет произнести сообщение "Hello World" при получении звонка.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Дополнительные сведения о TwiML см. на странице [][1]<https://www.twilio.com/docs/api/twiml></a>.

После того как вы настроили процесс предоставления ответов TwiML, вы можете передать этот URL-адрес в метод **client.makeCall**, как показано в следующем примере кода:

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]

  [специальное предложение]: http://ahoy.twilio.com/azure
  [Цены на Twilio]: http://www.twilio.com/pricing
  [Библиотеки API Twilio]: https://www.twilio.com/docs/libraries
  [.NET]: /ru-ru/develop/net/how-to-guides/twilio-voice-and-sms-service/
  [node.js]: /ru-ru/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
  [Java]: /ru-ru/develop/java/how-to-guides/twilio-voice-and-sms-service/
  [PHP]: /ru-ru/develop/php/how-to-guides/twilio-voice-and-sms-service/
  [Python]: /ru-ru/develop/python/how-to-guides/twilio-voice-and-sms-service/
  [Ruby]: /ru-ru/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
  [документации по языку разметки Twilio]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Попробуйте Twilio]: https://www.twilio.com/try-twilio
  [странице учетной записи Twilio]: https://www.twilio.com/user/account
  [Управление номерами]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Справка по телефонным номерам Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [Приступая к работе с мобильными службами]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started/
  [Хранение скриптов в системе управления версиями]: http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/store-scripts-in-source-control/
  [Консоль мобильных служб]: ./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png
  []: http://www.twilio.com/docs/api/rest/making-calls
  [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]: #howto_provide_twiml_responses
  [1]: https://www.twilio.com/docs/api/twiml
