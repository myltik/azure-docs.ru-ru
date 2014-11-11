<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Использование Twilio для поддержки голосовых возможностей и SMS в Java

В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое Twilio?][Что такое Twilio?]
-   [Цены на Twilio][Цены на Twilio]
-   [Основные понятия][Основные понятия]
-   [Создание учетной записи Twilio][Создание учетной записи Twilio]
-   [Проверка телефонных номеров][Проверка телефонных номеров]
-   [Создание приложения Java][Создание приложения Java]
-   [Настройка приложения для использования библиотек Twilio][Настройка приложения для использования библиотек Twilio]
-   [Практическое руководство. Осуществление исходящего вызова][Практическое руководство. Осуществление исходящего вызова]
-   [Практическое руководство. Отправка SMS-сообщения][Практическое руководство. Отправка SMS-сообщения]
-   [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]
-   [Практическое руководство. Использование дополнительных служб Twilio][Практическое руководство. Использование дополнительных служб Twilio]
-   [Дальнейшие действия][Дальнейшие действия]

## <span id="WhatIs"></span></a> Что такое Twilio?

Twilio — это API веб-службы телефонии, позволяющий использовать существующие языки веб-программирования и навыки для создания приложений для работы с голосовыми вызовами и SMS. Twilio — это сторонняя служба (не компонент Azure и не продукт Майкрософт).

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет приложениям реализовать речевую связь с помощью существующих интернет-подключений, в том числе на мобильных устройствах.

## <span id="Pricing"></span></a>Цены и специальные предложения Twilio

Сведения о ценах на Twilio доступны на веб-сайте [цен на Twilio][цен на Twilio]. Клиентам Azure доступно [специальное предложение][специальное предложение]: бесплатный кредит на 1000 текстовых сообщений или 1000 минут входящих вызовов. Чтобы воспользоваться этим предложением или получить дополнительные сведения, посетите веб-сайт [][специальное предложение]<http://ahoy.twilio.com/azure></a>.

## <span id="Concepts"></span></a>Основные понятия

Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки API Twilio][Библиотеки API Twilio].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

### <span id="Verbs"></span></a>Команды Twilio

В API используются команды Twilio: например, команда **Say** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова.

Ниже приведен список команд Twilio.

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

Различные номера телефонов для вашей учетной записи должны быть проверены с помощью Twilio. Например, если вы хотите совершить исходящие телефонные звонки, номер телефона должен проверяться в Twilio как идентификатор объекта, выполняющего исходящий вызов. Аналогичным образом если требуется номер телефона для получения SMS-сообщений, номер получающего телефона должен быть проверен в Twilio. Сведения о том, как проверить номер телефона, см. в разделе [Управление номерами][Управление номерами]. В следующем коде указываются номера телефонов, которые нужно будет проверить в Twilio.

Вместо использования для приложений существующего номера можно приобрести телефонный номер Twilio. Сведения о приобретении телефонного номера Twilio см. в разделе [Справка по телефонным номерам Twilio][Справка по телефонным номерам Twilio]

## <span id="create_app"></span></a>Создание приложения Java

1.  Получите JAR-файл Twilio и добавьте его к пути построения Java и к WAR-сборке. По адресу [][]<https://github.com/twilio/twilio-java></a> можно загрузить источники GitHub и создать свои собственные JAR-файлы или загрузить готовый JAR-файл (с зависимостями или без зависимостей).
2.  Убедитесь, что в хранилище ключей **cacerts** JDK содержится сертификат Equifax Secure Certificate Authority с отпечатком MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (серийный номер 35:DE:F4:CF, отпечаток SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Это сертификат центра сертификации (ЦС) для службы [][1]<https://api.twilio.com></a>, который был вызван при использовании интерфейсов API Twilio. Сведения о том, как убедиться, что хранилище ключей **cacerts** JDK содержит правильный сертификат ЦС, см. в разделе [Добавление сертификата в хранилище сертификатов ЦС Java][Добавление сертификата в хранилище сертификатов ЦС Java].

Подробные инструкции по использованию клиентской библиотеки Twilio для Java доступны в разделе [Осуществление телефонных звонков с использованием Twilio в Java-приложении на платформе Azure][Осуществление телефонных звонков с использованием Twilio в Java-приложении на платформе Azure].

## <span id="configure_app"></span></a>Настройка приложения для использования библиотек Twilio

В коде можно добавить операторы **импорта** в верхней части исходных файлов для пакетов или классов Twilio, которые требуется использовать в приложении.

Для исходных файлов Java:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Для исходных файлов Java Server Page (JSP):

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"

Операторы **импорта** могут отличаться в зависимости от того, какие пакеты или классы Twilio необходимо использовать.

## <span id="howto_make_call"></span></a>Практическое руководство. Осуществление исходящего вызова

Далее показано, как осуществлять исходящий вызов с использованием класса **CallFactory**. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Дополнительные сведения о параметрах, передаваемых в метод **CallFactory.create**, см. по адресу [][2]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Можно использовать собственный веб-сайт для предоставления ответа TwiML; дополнительные сведения содержатся в разделе [Предоставление ответов TwiML в Java-приложении на платформе Azure][Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта].

## <span id="howto_send_sms"></span></a>Практическое руководство. Отправка SMS-сообщения

Ниже показано, как отправить SMS-сообщение с использованием класса **SmsFactory**. С целью отправки SMS-сообщений для пробных учетных записей номер **From** (От), **4155992671**, предоставляется Twilio. Номер **To** (Кому) для учетной записи Twilio необходимо проверить перед выполнением кода.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

Дополнительные сведения о параметрах, передаваемых в метод **SmsFactory.create**, см. по адресу [][3]<http://www.twilio.com/docs/api/rest/sending-sms></a>.

## <span id="howto_provide_twiml_responses"></span></a>Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта

Когда приложение инициирует вызов API Twilio — например, с использованием метода **CallFactory.create** — Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. В приведенном выше примере используется URL-адрес, предоставляемый Twilio: [][4]<http://twimlets.com/message></a>. (Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, выберите [][4]<http://twimlets.com/message></a> для просмотра пустого элемента **\<Response\>**; в качестве другого примера нажмите [][4][http://twimlets.com/message?Message%5B0%5D=Hello%20World][4]</a> для просмотра элемента, в котором содержится элемент **\<Say\>**.)

Вместо того, чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный URL-адрес для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает HTTP-ответы; в этом разделе предполагается, что URL-адрес будет размещаться на странице JSP.

Следующая страница JSP создает ответ TwiML **Hello World** на вызов.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

Следующая страница JSP приведет к появлению ответа TwiML, в котором произносится определенный текст, присутствуют несколько пауз, а также сообщаются сведения о версии Twilio API и имя роли Azure.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Параметр **ApiVersion** доступен в голосовых запросах Twilio (но не в SMS-запросах). Доступные параметры запроса для голосовых и SMS-запросов Twilio см. в разделах <https://www.twilio.com/docs/api/twiml/twilio_request> и <https://www.twilio.com/docs/api/twiml/sms/twilio_request> соответственно. Переменная среды **RoleName** доступна как часть развертывания Azure. (Если вы хотите добавлять пользовательские переменные среды, их можно взять из **System.getenv**, см. раздел, посвященный переменным среды в главе [Прочие параметры конфигурации ролей][Прочие параметры конфигурации ролей]).

После настройки JSP-страницы на предоставление ответов TwiML используйте URL страницы JSP как URL, передаваемый в метод **CallFactory.create**. Например, если у вас есть веб-приложение с именем MyTwiML, развернутое в размещенной службе Azure, а JSP-страница использует имя mytwiml.jsp, URL-адрес может быть передан в **CallFactory.create**, как показано далее:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Другой вариант ответа TwiML — через класс **TwiMLResponse**, который доступен в пакете **com.twilio.sdk.verbs**.

Дополнительные сведения об использовании Twilio в Azure с Java см. в разделе [Осуществление телефонных звонков с использованием Twilio в Java-приложении Azure][Осуществление телефонных звонков с использованием Twilio в Java-приложении на платформе Azure].

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
  [Создание приложения Java]: #create_app
  [Настройка приложения для использования библиотек Twilio]: #configure_app
  [Практическое руководство. Осуществление исходящего вызова]: #howto_make_call
  [Практическое руководство. Отправка SMS-сообщения]: #howto_send_sms
  [Практическое руководство. Предоставление ответа TwiML с собственного веб-сайта]: #howto_provide_twiml_responses
  [Практическое руководство. Использование дополнительных служб Twilio]: #AdditionalServices
  [цен на Twilio]: http://www.twilio.com/pricing
  [специальное предложение]: http://ahoy.twilio.com/azure
  [Библиотеки API Twilio]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [API Twilio]: http://www.twilio.com/api
  [Попробуйте Twilio]: https://www.twilio.com/try-twilio
  [странице учетной записи Twilio]: https://www.twilio.com/user/account
  [Управление номерами]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Справка по телефонным номерам Twilio]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [1]: https://api.twilio.com
  [Добавление сертификата в хранилище сертификатов ЦС Java]: ../java-add-certificate-ca-store
  [Осуществление телефонных звонков с использованием Twilio в Java-приложении на платформе Azure]: ../partner-twilio-java-phone-call-example
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://www.twilio.com/docs/api/rest/sending-sms
  [4]: http://twimlets.com/message
  [Прочие параметры конфигурации ролей]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh690945.aspx
  [Рекомендации по безопасности Twilio]: http://www.twilio.com/docs/security
  [Практические руководства и примеры кода Twilio]: http://www.twilio.com/docs/howto
  [Краткие учебники по Twilio]: http://www.twilio.com/docs/quickstart
  [Twilio на GitHub]: https://github.com/twilio
  [Обращение в службу поддержки Twilio]: http://www.twilio.com/help/contact
