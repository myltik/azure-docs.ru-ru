<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Использование Twilio для поддержки голосовых возможностей и SMS в PHP

В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое Twilio?][Что такое Twilio?]
-   [Цены на Twilio][Цены на Twilio]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи Twilio][Создание учетной записи Twilio]
-   [Проверка телефонных номеров][Проверка телефонных номеров]
-   [Создание приложения PHP][Создание приложения PHP]
-   [Настройка приложения для использования библиотек Twilio][Настройка приложения для использования библиотек Twilio]
-   [Практическое руководство. Осуществление исходящего вызова][Практическое руководство. Осуществление исходящего вызова]
-   [Практическое руководство. Отправка SMS-сообщения][Практическое руководство. Отправка SMS-сообщения]
-   [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]

## <span id="WhatIs"></span></a> Что такое Twilio?

Twilio создает новые возможности для бизнес-коммуникаций, позволяя разработчикам встраивать в приложения функции голосовой связи, VoIP и обмена сообщениями. Они виртуализируют всю необходимую инфраструктуру в облачной глобальной среде с предоставлением доступа через коммуникационную API платформу Twilio. Приложения отличаются простотой создания и масштабирования. Оцените гибкость повременной оплаты, а также преимущества, связанные с надежностью облачных решений.

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать текстовые сообщения. **Twilio Client** позволяет выполнять VoIP звонки с любого телефона, планшета или из браузера, а также поддерживает WebRTC.

## <span id="Pricing"></span></a>Цены и специальные предложения Twilio

Клиенты Azure получают [специальные предложения][<http://www.twilio.com/azure>]: дополнительный кредит Twilio в размере 10 долл. США при обновлении учетной записи Twilio. Этот кредит Twilio применяется к любым сценариям использования Twilio (кредит в размере 10 $ позволяет отправить 1000 SMS-сообщений или получать входящие голосовые вызовы продолжительностью до 1000 минут в зависимости от расположения телефонного номера, а также от направления отправки сообщения или совершения звонка). Получите этот кредит Twilio и приступите к работе на [][]<http://ahoy.twilio.com/azure></a>.

Twilio представляет собой службу с оплатой по мере использования. Стартовые платежи отсутствуют, а учетную запись можно закрыть в любое время. Дополнительные сведения см. в разделе [Цены на Twilio][1].

## <span id="Concepts"></span></a>Основные понятия

Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки API Twilio][Библиотеки API Twilio].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

### <span id="Verbs"></span></a>Команды Twilio

В API используются команды Twilio: например, команда **Say** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова.

Ниже приведен список команд Twilio. Дополнительные сведения о других командах и возможностях см. в [документации по языку разметки Twilio][<http://www.twilio.com/docs/api/twiml>].

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

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. в разделе [TwiML][TwiML]. Дополнительные сведения об API Twilio см. в разделе [API Twilio][API Twilio].

## <span id="CreateAccount"></span></a>Создание учетной записи Twilio

После подготовки к получению учетной записи Twilio зарегистрируйтесь на странице [Попробуйте Twilio][Попробуйте Twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio, вы получите идентификатор учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор учетной записи и маркер проверки подлинности отображаются на [странице учетной записи Twilio][странице учетной записи Twilio] в полях **КОД БЕЗОПАСНОСТИ УЧЕТНОЙ ЗАПИСИ** и **МАРКЕР ПРОВЕРКИ ПОДЛИННОСТИ** соответственно.

## <span id="VerifyPhoneNumbers"></span></a>Проверка телефонных номеров

Различные номера телефонов для вашей учетной записи должны быть проверены с помощью Twilio. Например, если вы хотите совершить исходящие телефонные звонки с использованием существующего номера телефона в качестве идентификатора объекта, выполняющего исходящий вызов, номер телефона должен проверяться в Twilio. Аналогичным образом, пока не будет выполнено обновление, номер телефона, на который вы хотите отправлять SMS-сообщения, должен проверяться в Twilio. После обновления SMS-сообщения можно отправлять на любой номер, не выполняя проверку. Сведения о том, как проверить номер телефона, см. в разделе [Управление номерами][Управление номерами]. В следующем коде указываются номера телефонов, которые нужно будет проверить в Twilio.

Вместо использования для приложений существующего номера можно приобрести телефонный номер Twilio. Сведения о приобретении телефонного номера Twilio см. в разделе [Справка по телефонным номерам Twilio][Справка по телефонным номерам Twilio]

## <span id="create_app"></span></a>Создание приложения PHP

Приложение PHP, которое использует службу Twilio и выполняется в Azure, не отличается от других приложений PHP, которые используют службу Twilio. Хотя службы Twilio поддерживают интерфейс REST и могут вызываться из PHP несколькими способами, в этой статье основное внимание уделяется использованию служб Twilio с [библиотекой Twilio для PHP из Github][библиотекой Twilio для PHP из Github]. Дополнительные сведения об использовании библиотеки Twilio для PHP см. на странице [][2]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>.

Подробные инструкции по построению и разработке приложения Twilio/PHP в Azure приводятся в разделе [Осуществление телефонных звонков с использованием Twilio в PHP-приложении Azure][Осуществление телефонных звонков с использованием Twilio в PHP-приложении Azure].

## <span id="configure_app"></span></a>Настройка приложения для использования библиотек Twilio

Настройку приложения для работы с библиотекой Twilio для PHP можно выполнить двумя способами:

1.  Загрузите библиотеку Twilio для PHP с сайта Github ([][библиотекой Twilio для PHP из Github]<https://github.com/twilio/twilio-php></a>) и добавьте каталог **Службы** для вашего приложения.

    -ИЛИ-

2.  Установите библиотеку Twilio для PHP в виде пакета PEAR. Для установки можно использовать следующие команды:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

После установки библиотеки Twilio для PHP вы можете добавлять в начало PHP-файлов инструкцию **require\_once**, задающую ссылку на эту библиотеку:

        require_once 'Services/Twilio.php';

Дополнительные сведения см. в разделе [][3]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.

## <span id="howto_make_call"></span></a>Практическое руководство. Осуществление исходящего вызова

Далее показано, как осуществлять исходящий вызов с использованием класса **Services\_Twilio**. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Можно использовать собственный веб-сайт для предоставления ответа TwiML; дополнительные сведения содержатся в разделе [Предоставление ответов TwiML с собственного веб-сайта][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта].

-   **Примечание**. Рекомендации по устранению ошибок, связанных с проверкой SSL-сертификата, см. в разделе [][4]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>

## <span id="howto_send_sms"></span></a>Практическое руководство. Отправка SMS-сообщения

Ниже показано, как отправить SMS-сообщение с использованием класса **Services\_Twilio**. С целью отправки SMS-сообщений для пробных учетных записей номер **From** (От) предоставляется Twilio. Номер **To** (Кому) для учетной записи Twilio необходимо проверить перед выполнением кода.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта

Когда приложение инициирует вызов API Twilio, Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. В приведенном выше примере используется URL-адрес, предоставляемый Twilio: [][5]<http://twimlets.com/message></a>. (Хотя TwiML предназначается для использования службой Twilio, его можно также просмотреть в браузере. Например, выберите [][5]<http://twimlets.com/message></a> для просмотра пустого `<Response>` элемента; в качестве другого примера нажмите [][6]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a> для просмотра элемента `<Response>`, в котором содержится элемент `<Say>`).

Вместо того, чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный сайт для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает XML-ответы; в этом разделе предполагается, что для создания TwiML будет использоваться язык PHP.

Следующая страница PHP создает ответ TwiML **Hello World** на вызов.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Как видно из приведенного выше примера, ответ TwiML будет представлять собой простой XML-документ. Библиотека Twilio для PHP содержит классы, которые создадут для вас TwiML. В следующем примере возвращается аналогичный предыдущему ответ, однако используется класс **Services\_Twilio\_Twiml** из библиотеки Twilio для PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Дополнительные сведения о TwiML см. на странице [][7]<https://www.twilio.com/docs/api/twiml></a>.

После настройки PHP-страницы на предоставление ответов TwiML используйте URL страницы PHP как URL, передаваемый в метод `Services_Twilio->account->calls->create`. Например, если у вас есть веб-приложение с именем **MyTwiML**, развернутая в размещенной службе Azure, а PHP-страница использует имя **mytwiml.php**, URL-адрес может быть передан в класс **Services\_Twilio-\>account-\>calls-\>create**, как показано в следующем примере:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Дополнительные сведения об использовании Twilio в Azure с PHP см. в разделе [Осуществление телефонных звонков с использованием Twilio в PHP-приложении Azure][Осуществление телефонных звонков с использованием Twilio в PHP-приложении Azure].

## <span id="AdditionalServices"></span></a>Практическое руководство. Использование дополнительных служб Twilio

Помимо примеров, перечисленных здесь, Twilio предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции Twilio из вашего приложения Azure. Дополнительные сведения см. в [документации по интерфейсу API Twilio][API Twilio].

## <span id="NextSteps"></span></a> Дальнейшие действия

Вы узнали основные сведения о службе Twilio. Для получения дополнительных сведений используйте следующие ссылки.

-   [Рекомендации по безопасности Twilio][Рекомендации по безопасности Twilio]
-   [Практические руководства и примеры кода Twilio][Практические руководства и примеры кода Twilio]
-   [Краткие учебники по Twilio][Краткие учебники по Twilio]
-   [Twilio на GitHub][Twilio на GitHub]
-   [Обращение в службу поддержки Twilio][Обращение в службу поддержки Twilio]

  [Дальнейшие действия]: #NextSteps
  [Цены на Twilio]: #Pricing
  [Основные понятия]: #Concepts
  [Создание учетной записи Twilio]: #CreateAccount
  [Проверка телефонных номеров]: #VerifyPhoneNumbers
  [Создание приложения PHP]: #create_app
  [Настройка приложения для использования библиотек Twilio]: #configure_app
  [Практическое руководство. Осуществление исходящего вызова]: #howto_make_call
  [Практическое руководство. Отправка SMS-сообщения]: #howto_send_sms
  [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]: #howto_provide_twiml_responses
  []: http://ahoy.twilio.com/azure
  [1]: http://www.twilio.com/pricing
  [Библиотеки API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Попробуйте Twilio]: https://www.twilio.com/try-twilio
  [странице учетной записи Twilio]: https://www.twilio.com/user/account
  [Управление номерами]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Справка по телефонным номерам Twilio]: https://www.twilio.com/help/faq/phone-numbers
  [библиотекой Twilio для PHP из Github]: https://github.com/twilio/twilio-php
  [2]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [Осуществление телефонных звонков с использованием Twilio в PHP-приложении Azure]: ../partner-twilio-php-make-phone-call
  [3]: https://github.com/twilio/twilio-php/blob/master/README.md
  [4]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [5]: http://twimlets.com/message
  [6]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [7]: https://www.twilio.com/docs/api/twiml
  [Рекомендации по безопасности Twilio]: http://www.twilio.com/docs/security
  [Практические руководства и примеры кода Twilio]: http://www.twilio.com/docs/howto
  [Краткие учебники по Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio на GitHub]: https://github.com/twilio
  [Обращение в службу поддержки Twilio]: http://www.twilio.com/help/contact
