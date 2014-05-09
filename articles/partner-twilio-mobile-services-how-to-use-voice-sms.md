<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Использование Twilio для поддержки голосовых возможностей и SMS | Центр разработчиков для мобильных устройств" metaKeywords="" description="Узнайте, как выполнять общие задачи с использованием API Twilio для мобильных служб Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Использования Twilio для поддержки голосовых возможностей и SMS в мобильных службах" authors="twilio" solutions="" manager="" editor="" />


<h1>Использование Twilio для поддержки голосовых вызовов и SMS в мобильных службах</h1>

В этом разделе показано, как выполнять типовые задачи с использованием API Twilio для мобильных служб Azure. В этом учебнике рассказывается, как создавать пользовательские скрипты, использующие API Twilio для выполнения телефонного звонка и отправки текстового сообщения. 

<h2><a id="WhatIs"></a>Что такое Twilio?</h2>
Twilio создает новые возможности для бизнес-коммуникаций, позволяя разработчикам встраивать в приложения функции голосовой связи, VoIP и обмена сообщениями. Они виртуализируют всю необходимую инфраструктуру в облачной глобальной среде с предоставлением доступа через коммуникационную API платформу Twilio. Приложения отличаются простотой создания и масштабирования. Оцените гибкость повременной оплаты, а также преимущества, связанные с надежностью облачных решений.

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет выполнять VoIP звонки с любого телефона, планшета или из браузера, а также поддерживает WebRTC.

<h2><a id="Pricing"></a>Цены и специальные предложения Twilio</h2>
Клиентам Azure доступно [специальное предложение][special_offer]: кредит Twilio в размере 10 долл. США при обновлении учетной записи Twilio. Этот кредит Twilio применяется к любым сценариям использования Twilio (кредит в размере 10 $ позволяет отправить 1000 SMS-сообщений или получать входящие голосовые вызовы продолжительностью до 1000 минут в зависимости от расположения телефонного номера, а также от направления отправки сообщения или совершения звонка). Получите этот кредит Twilio и приступите к работе на [ahoy.twilio.com/azure][special_offer].

Twilio представляет собой службу с оплатой по мере использования. Стартовые платежи отсутствуют, а учетную запись можно закрыть в любое время. Дополнительные сведения см. в разделе [Цены на Twilio][twilio_pricing].  

<h2><a id="Concepts"></a>Основные понятия</h2>
Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки API Twilio] [twilio_libraries].  Также доступны учебники по работе с Twilio в приложениях Azure, написанных на [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] и [Ruby][azure_twilio_howto_ruby].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

<h3><a id="Verbs"></a>Команды Twilio</h3>
В API используются команды Twilio: например, команда **&lt;Say&gt;** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова. 

Ниже приведен список команд Twilio.  Дополнительные сведения о других командах и возможностях см. в [документации по языку разметки Twilio](http://www.twilio.com/docs/api/twiml).

* **&lt;Dial&gt;**: подключение вызывающего абонента к другому телефону.
* **&lt;Gather&gt;**: сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;**: окончание вызова.
* **&lt;Play&gt;**: воспроизведение звукового файла.
* **&lt;Pause&gt;**: бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Record&gt;**: запись голоса вызывающего абонента и возврат URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;**: передача управления для вызова или SMS в TwiML по другому URL-адресу.
* **&lt;Reject&gt;**: отклонение входящего вызова на ваш номер Twilio без выставления счета
* **&lt;Say&gt;**: преобразование текста в речь в вызове.
* **&lt;Sms&gt;**: отправка SMS-сообщения.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML — это набор инструкций на основе XML и с использованием команд Twilio, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Когда приложение вызывает Twilio API, одним из параметров API является URL-адрес, который возвращает ответ TwiML. Для целей разработки можно использовать URL-адреса из Twilio для предоставления ответов TwiML, используемых приложениями. Также может разместить свои собственные URL-адреса для получения ответов TwiML; другой вариант — использовать объект **TwiMLResponse**.

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. в разделе [TwiML][twiml]. Дополнительные сведения об API Twilio см. в разделе [API Twilio][twilio_api].

<h2><a id="CreateAccount"></a>Создание учетной записи Twilio</h2>
После подготовки к получению учетной записи Twilio зарегистрируйтесь на странице [Попробуйте Twilio][try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio, вы получите идентификатор учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор учетной записи и маркер проверки подлинности отображаются на [странице учетной записи Twilio][twilio_account] в полях **КОД БЕЗОПАСНОСТИ УЧЕТНОЙ ЗАПИСИ** и **МАРКЕР ПРОВЕРКИ ПОДЛИННОСТИ** соответственно.

<h2><a id="VerifyPhoneNumbers"></a>Проверка телефонных номеров</h2>
Различные номера телефонов для вашей учетной записи должны быть проверены с помощью Twilio. Например, если вы хотите совершить исходящие телефонные звонки, номер телефона должен проверяться в Twilio как идентификатор объекта, выполняющего исходящий вызов. Аналогичным образом, если требуется номер телефона для получения SMS-сообщений, номер получающего телефона должен быть проверен в Twilio. Сведения о проверке номера телефона см. в разделе [Управление номерами] [verify_phone]. В следующем коде указываются номера телефонов, которые нужно будет проверить в Twilio.

Вместо использования для приложений существующего номера можно приобрести телефонный номер Twilio. Сведения о приобретении телефонного номера Twilio см. в разделе [Справка по телефонным номерам Twilio](https://www.twilio.com/help/faq/phone-numbers)

<h2><a id="create_app"></a>Создать мобильную службу</h2>
Мобильная служба, в которой размещается приложение с поддержкой Twilio, ничем не отличается от любой другой мобильной службы. После добавления библиотеки Twilio node.js вы можете задавать ссылки на нее из пользовательских скриптов API для мобильной службы. Дополнительные сведения о создании исходной мобильной службы см. в разделе [Приступая к работе с мобильными службами](http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/get-started/).

<h2><a id="VerifyPhoneNumbers"></a>Настройка мобильной службы для работы с библиотекой Twilio Node.js</h2>
Twilio предоставляет библиотеку Node.js, содержащих различные аспекты Twilio для предоставления простых и легких способов взаимодействия с API REST Twilio и Twilio Client для создания ответов TwiML.

Чтобы использовать библиотеку Twilio node.js в своей мобильной службе, вам необходимо реализовать поддержку модуля npm для мобильной службы, для чего можно сохранить скрипты в системе управления версиями. В учебнике [Хранение скриптов в системе управления версиями](http://www.windowsazure.com/ru-ru/develop/mobile/tutorials/store-scripts-in-source-control/) приводятся инструкции по настройке системы управления версиями при первом запуске мобильных служб, а также рекомендации по хранению серверных скриптов в репозитории Git.

После того, как вы настроили систему управления версиями для мобильной службы, откройте вкладку "Настройка" на панели мониторинга мобильной службы, после чего найдите и скопируйте URL-адрес Git

Вставьте этот URL-адрес в браузер и замените имя репозитория на */DebugConsole/index.html*

Например, замените:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

на:

    https://twilioSample.scm.azure-mobile.net/DebugConsole/index.html

При появлении запроса введите учетные данные, которые использовались при настройке системы управления версиями для службы.  После входа в систему вы увидите консоль мобильных служб Azure.  

![Консоль мобильных служб](./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png)

В консоли замените каталог на папку скриптов:

    cd site\wwwroot\App_Data\config\scripts

В папке api вы можете установить модуль узла Twilio, выполнив следующую команду:

    npm install twilio

Теперь вы можете задавать ссылки и использовать библиотеку Twilio в собственных API и табличных скриптах.

<h2><a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова</h2>
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

Дополнительные сведения о параметрах, передаваемых в функцию **client.makeCall**, см. в разделе [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Вместо этого можно использовать собственный веб-сайт для предоставления ответа TwiML. Более подробные сведения см. в разделе [Предоставление ответов TwiML с собственного веб-сайта](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Практическое руководство. Отправка SMS-сообщения</h2>
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


<h2><a id="howto_provide_twiml_responses"></a>Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта</h2>

Когда приложение инициирует вызов API Twilio - например, с использованием метода client.InitiateOutboundCall - Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. В примере, показанном в разделе "Практическое руководство. Осуществление исходящего звонка" для возврата ответа используется URL-адрес http://twimlets.com, предоставляемый Twilio.

<div class="dev-callout">
<b>Примечание.</b>
<p>Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, щелкните <a href="http://twimlets.com/message">twimlet_message_url</a>, чтобы просмотреть пустой элемент &lt;Response&gt;. Также вы можете щелкнуть <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a>, чтобы просмотреть элемент &lt;Response&gt;, содержащий команду &lt;Say&gt;.</p>
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

Дополнительные сведения о TwiML см. в разделе [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

После того, как вы настроили процесс предоставления ответов TwiML, вы можете передать этот URL-адрес в метод **client.makeCall**, как показано в следующем примере кода:
    
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


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /ru-ru/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /ru-ru/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /ru-ru/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /ru-ru/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /ru-ru/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /ru-ru/develop/php/how-to-guides/twilio-voice-and-sms-service/

