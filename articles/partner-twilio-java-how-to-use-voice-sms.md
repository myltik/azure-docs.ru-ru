<properties 
	pageTitle="Использование Twilio для поддержки голосовых вызовов и SMS (Java) | Microsoft Azure" 
	description="Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры кода написаны на Java." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Использование Twilio для поддержки голосовых возможностей и SMS в Java

В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия](#NextSteps).

## <a id="WhatIs"></a>Что такое Twilio?
Twilio — это API веб-службы телефонии, позволяющий использовать существующие языки веб-программирования и навыки для создания приложений для работы с голосовыми вызовами и SMS. Twilio — это сторонняя служба (не компонент Azure и не продукт Майкрософт).

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет приложениям реализовать речевую связь с помощью существующих интернет-подключений, в том числе на мобильных устройствах.

## <a id="Pricing"></a>Цены и специальные предложения Twilio
Сведения о ценах на Twilio доступны на веб-сайте [цен на Twilio][twilio_pricing]. Клиентам Azure доступно [специальное предложение][special_offer]\: бесплатный кредит на 1000 текстовых сообщений или 1000 минут входящих вызовов. Чтобы воспользоваться этим предложением или получить дополнительные сведения, посетите сайт [http://ahoy.twilio.com/azure][special_offer].

## <a id="Concepts"></a>Основные понятия
Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки API Twilio][twilio_libraries].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

### <a id="Verbs"></a>Команды Twilio
Интерфейс API использует команды Twilio: например, команда **&lt;Say&gt;** предписывает Twilio воспроизвести сообщение при вызове.

Ниже приведен список команд Twilio.

* **&lt;Dial&gt;**: подключение звонящего к другому телефону.
* **&lt;Gather&gt;**: сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;**: завершение вызова.
* **&lt;Play&gt;**: воспроизведение звукового файла.
* **&lt;Pause&gt;**: бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Record&gt;**: запись голоса звонящего и возврат URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;**: передача управления вызовом или SMS в TwiML по другому URL-адресу.
* **&lt;Reject&gt;**: отклонение входящего вызова на ваш номер Twilio без выставления счета.
* **&lt;Say&gt;**: преобразование текста в речь при вызове.
* **&lt;Sms&gt;**: отправка SMS-сообщения.

### <a id="TwiML"></a>TwiML
TwiML — это набор инструкций на основе XML и с использованием команд Twilio, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Когда приложение вызывает Twilio API, одним из параметров API является URL-адрес, который возвращает ответ TwiML. Для целей разработки можно использовать URL-адреса из Twilio для предоставления ответов TwiML, используемых приложениями. Также может разместить свои собственные URL-адреса для получения ответов TwiML; другой вариант — использовать объект **TwiMLResponse**.

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. в разделе [TwiML][twiml]. Дополнительные сведения об API Twilio см. в разделе [API Twilio][twilio_api].

## <a id="CreateAccount"></a>Создание учетной записи Twilio
После подготовки к получению учетной записи Twilio зарегистрируйтесь на странице [Попробуйте Twilio][try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio, вы получите идентификатор учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор учетной записи и маркер проверки подлинности отображаются на [странице учетной записи Twilio][twilio_account] в полях **КОД БЕЗОПАСНОСТИ УЧЕТНОЙ ЗАПИСИ** и **МАРКЕР ПРОВЕРКИ ПОДЛИННОСТИ** соответственно.

## <a id="create_app"></a>Создание приложения Java
1. Получите JAR-файл Twilio и добавьте его к пути построения Java и к WAR-сборке. На сайте [https://github.com/twilio/twilio-java][twilio_java] можно загрузить исходные файлы GitHub и создать свои собственные JAR-файлы или загрузить готовый JAR-файл (с зависимостями или без них).
2. Убедитесь, что в хранилище ключей **cacerts** JDK содержится сертификат Equifax Secure Certificate Authority с отпечатком MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (серийный номер 35:DE:F4:CF, отпечаток SHA1 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Это сертификат центра сертификации (ЦС) для службы [https://api.twilio.com][twilio_api_service], которая вызывается при использовании интерфейсов API Twilio. Сведения о том, как убедиться, что хранилище ключей **cacerts** JDK содержит правильный сертификат ЦС, см. в разделе [Добавление сертификата в хранилище сертификатов ЦС Java][add_ca_cert].

Подробные инструкции по использованию клиентской библиотеки Twilio для Java доступны в разделе [Осуществление телефонных звонков с использованием Twilio в Java-приложении на платформе Azure][howto_phonecall_java].

## <a id="configure_app"></a>Настройка приложения для использования библиотек Twilio
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

## <a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова
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

Дополнительные сведения о параметрах, передаваемых в метод **CallFactory.create**, см. в разделе [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Можно использовать собственный веб-сайт для предоставления ответа TwiML; дополнительные сведения содержатся в разделе [Предоставление ответов TwiML в Java-приложении на платформе Azure](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Практическое руководство. Отправка SMS-сообщения
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
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Дополнительные сведения о параметрах, передаваемых в метод **SmsFactory.create**, см. в разделе [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a id="howto_provide_twiml_responses"></a>Практическое руководство. Предоставление ответов TwiML с собственного веб-сайта
Когда приложение инициирует вызов API Twilio — например, с использованием метода **CallFactory.create** — Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. В примере выше используется предоставляемый Twilio URL-адрес [http://twimlets.com/message][twimlet_message_url]. (Хотя TwiML предназначается для использования веб-службами, TwiML можно также просмотреть в браузере. Например, щелкните [http://twimlets.com/message][twimlet_message_url] для просмотра пустого элемента **&lt;Response&gt;**; в качестве другого примера щелкните [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] для просмотра элемента **&lt;Response&gt;**, содержащего элемент **&lt;Say&gt;**.

Вместо того чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный URL-адрес для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает HTTP-ответы; в этом разделе предполагается, что URL-адрес будет размещаться на странице JSP.

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

Параметр **ApiVersion** доступен в голосовых запросах Twilio (но не в SMS-запросах). Доступные параметры запроса для голосовых и SMS-запросов Twilio см. в разделах <https://www.twilio.com/docs/api/twiml/twilio_request> и <https://www.twilio.com/docs/api/twiml/sms/twilio_request> соответственно. Переменная среды **RoleName** доступна как часть развертывания Azure. (Если вы хотите добавлять пользовательские переменные среды, их можно взять из **System.getenv**, см. раздел, посвященный переменным среды в главе [Прочие параметры конфигурации ролей][misc_role_config_settings].)

После настройки JSP-страницы на предоставление ответов TwiML используйте URL страницы JSP как URL, передаваемый в метод **CallFactory.create**. Например, если у вас есть веб-приложение с именем MyTwiML, развернутое в размещенной службе Azure, а JSP-страница использует имя mytwiml.jsp, URL-адрес может быть передан в **CallFactory.create**, как показано далее:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Другой вариант ответа TwiML — через класс **TwiMLResponse**, который доступен в пакете **com.twilio.sdk.verbs**.

Дополнительные сведения об использовании Twilio в Azure с Java см. в разделе [Осуществление телефонных звонков с использованием Twilio в Java-приложении Azure][howto_phonecall_java].

## <a id="AdditionalServices"></a>Руководство: использование дополнительных служб Twilio
В дополнение к приведенным примерам, Twilio предлагает веб-интерфейсы API для использования дополнительных функций Twilio в вашем приложении Azure. Дополнительные сведения см. в [документации по интерфейсу API Twilio][twilio_api_documentation].

## <a id="NextSteps"></a>Дальнейшие действия
Вы узнали основные сведения о службе Twilio. Для получения дополнительных сведений используйте следующие ссылки.

* [Рекомендации по безопасности Twilio][twilio_security_guidelines]
* [Практические руководства по Twilio и примеры кода][twilio_howtos]
* [Краткие учебники по Twilio][twilio_quickstarts] 
* [Twilio на GitHub][twilio_on_github]
* [Обращение в службу поддержки Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!---HONumber=Oct15_HO3-->