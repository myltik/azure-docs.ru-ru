---
title: Использование Twilio для поддержки голосовых возможностей и SMS (Python) | Microsoft Docs
description: Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры кода написаны на Python.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: f4a02bb7a7c46e7a0e3c75b870c522eae8294339
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23040009"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Использование Twilio для поддержки голосовых возможностей и SMS на Python
В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия](#NextSteps).

## <a id="WhatIs"></a>Что такое Twilio?
Twilio создает новые возможности для бизнес-коммуникаций, позволяя разработчикам встраивать в приложения функции голосовой связи, VoIP и обмена сообщениями. Они виртуализируют всю необходимую инфраструктуру в облачной глобальной среде с предоставлением доступа через коммуникационную API платформу Twilio. Приложения отличаются простотой создания и масштабирования. Оцените гибкость повременной оплаты, а также преимущества, связанные с надежностью облачных решений.

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы.
**Twilio SMS** позволяет приложениям отправлять и принимать текстовые сообщения.
**Twilio Client** позволяет выполнять VoIP звонки с любого телефона, планшета или из браузера, а также поддерживает WebRTC.

## <a id="Pricing"></a>Цены и специальные предложения Twilio
Клиентам Azure доступно [специальное предложение][special_offer]: кредит Twilio в размере 10 долл. США при обновлении учетной записи Twilio. Этот кредит Twilio применяется к любым сценариям использования Twilio (кредит в размере 10 $ позволяет отправить 1000 SMS-сообщений или получать входящие голосовые вызовы продолжительностью до 1000 минут в зависимости от расположения телефонного номера, а также от направления отправки сообщения или совершения звонка). Получите этот [кредит Twilio][special_offer] и приступите к работе.

Twilio представляет собой службу с повременной оплатой. Стартовые платежи отсутствуют, а учетную запись можно закрыть в любое время. Дополнительные сведения см. в статье [Цены на Twilio][twilio_pricing].

## <a id="Concepts"></a>Основные понятия
Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Полный список клиентских библиотек API Twilio, которые доступны на разных языках, см. на [этой странице][twilio_libraries].

Основные аспекты Twilio API: команды Twilio и язык разметки Twilio (TwiML).

### <a id="Verbs"></a>Команды Twilio
В API используются команды Twilio: например, команда **&lt;Say&gt;** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова.

Ниже приведен список команд Twilio. Дополнительные сведения о других командах и возможностях см. в [документации по языку разметки Twilio][twiml].

* **&lt;Dial&gt;**: подключение вызывающего абонента к другому телефону.
* **&lt;Gather&gt;**: сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;**: окончание вызова.
* **&lt;Pause&gt;**: бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Play&gt;**: воспроизведение звукового файла.
* **&lt;Queue&gt;**: добавление абонентов в очередь.
* **&lt;Record&gt;**: запись голоса вызывающего абонента и возврат URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;**: передача управления для вызова или SMS в TwiML по другому URL-адресу.
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

Когда приложение вызывает Twilio API, одним из параметров API является URL-адрес, который возвращает ответ TwiML. Для целей разработки можно использовать URL-адреса из Twilio для предоставления ответов TwiML, используемых приложениями. Также можно разместить свои собственные URL-адреса для получения ответов TwiML; другой вариант — использовать объект `TwiMLResponse`.

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. на странице [TwiML][twiml]. Дополнительные сведения об API Twilio см. на [этой странице][twilio_api].

## <a id="CreateAccount"></a>Создание учетной записи Twilio
После подготовки к получению учетной записи Twilio зарегистрируйтесь на странице [Попробуйте Twilio][try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

При регистрации учетной записи Twilio вы получите идентификатор безопасности учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор безопасности учетной записи и маркер проверки подлинности отображаются на [консоли Twilio][twilio_console] в полях **ACCOUNT SID** (Идентификатор безопасности учетной записи) и **AUTH TOKEN** (Маркер проверки подлинности) соответственно.

## <a id="create_app"></a> Создание приложения Python
Приложение Python, которое использует службу Twilio и выполняется в Azure, не отличается от других приложений Python, которые используют службу Twilio. Хотя службы Twilio поддерживают интерфейс REST и могут вызываться из Python несколькими способами, в этой статье основное внимание уделяется использованию служб Twilio с [библиотекой Twilio для Python из GitHub][twilio_python]. Дополнительные сведения об использовании библиотеки Twilio для Python см. в статье [http://readthedocs.org/docs/twilio-python/en/latest/index.html][twilio_lib_docs].

Во-первых, [настройте новую виртуальную машину Linux Azure] [azure_vm_setup] в качестве узла для нового веб-приложения Python. После запуска виртуальной машины необходимо будет предоставить доступ к приложению через открытый порт, как описано ниже.

### <a name="add-an-incoming-rule"></a>Добавление правила для входящего трафика
  1. Перейдите на страницу [Группа безопасности сети] [azure_nsg].
  2. Выберите группу безопасности сети, соответствующую вашей виртуальной машине.
  3. Добавьте **Правило для исходящего трафика** для **порта 80**. Разрешите входящий трафик с любых адресов.

### <a name="set-the-dns-name-label"></a>Установка метки DNS-имени
  1. Перейдите на страницу [Общедоступные IP-адреса] [azure_ips].
  2. Выберите общедоступный IP-адрес, который соответствует виртуальной машине.
  3. В разделе **Конфигурация** задайте **метку DNS-имени**. В этом примере она будет выглядеть примерно так: *метка_ домена*.centralus.cloudapp.azure.com.

После подключения к виртуальной машине протоколу SSH можно установить веб-платформу по своему выбору (две наиболее известные в Python — [Flask](http://flask.pocoo.org/) и [Django](https://www.djangoproject.com)). Для установки любой из них нужно лишь выполнить команду `pip install`.

Имейте в виду, что мы настроили виртуальную машину для разрешения прохождения трафика только через порт 80. Поэтому настройте приложение для использования этого порта.

## <a id="configure_app"></a>Настройка приложения для использования библиотек Twilio
Настроить приложение для работы с библиотекой Twilio для Python можно двумя способами.

* Установите библиотеку Twilio для Python в виде пакета Pip. Для установки можно использовать следующие команды:
   
        $ pip install twilio

    -ИЛИ-

* Скачайте библиотеку Twilio для Python из GitHub ([https://github.com/twilio/twilio-python][twilio_python]) и установите ее, как показано далее.

        $ python setup.py install

После установки библиотеки Twilio для Python ее можно `import` в файлах Python.

        import twilio

Дополнительные сведения см. в документе [https://github.com/twilio/twilio-python/blob/master/README.md][twilio_github_readme].

## <a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова
Далее показано, как осуществлять исходящий вызов. Этот код также использует сайт из Twilio для выдачи ответа на языке разметки Twilio (TwiML). Замените значения телефонных номеров **from_number** (От) и **to_number** (Кому) и проверьте номер телефона **from_number** (От) для учетной записи Twilio до выполнения кода.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "http://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Как уже упоминалось, этот код также использует сайт из Twilio для выдачи ответа на языке TwiML. Можно использовать собственный веб-сайт для предоставления ответа TwiML; дополнительные сведения содержатся в разделе [Предоставление ответов TwiML с собственного веб-сайта](#howto_provide_twiml_responses).

## <a id="howto_send_sms"></a>Практическое руководство. Отправка SMS-сообщения
Ниже показано, как отправить SMS-сообщение с использованием класса `TwilioRestClient`. С целью отправки SMS-сообщений для пробных учетных записей номер **from_number** (От) предоставляется Twilio. Номер **to_number** (Кому) для учетной записи Twilio необходимо проверить перед выполнением кода.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>Практическое руководство. Предоставление ответов TwiML с собственного веб-сайта
Когда приложение инициирует вызов API Twilio, Twilio отправляет ваш запрос на URL-адрес, который должен возвратить ответ TwiML. В примере выше используется предоставляемый Twilio URL-адрес [http://twimlets.com/message][twimlet_message_url]. (Хотя TwiML предназначается для использования службой Twilio, его можно также просмотреть в браузере. Например, щелкните [http://twimlets.com/message][twimlet_message_url] для просмотра пустого элемента `<Response>`; в качестве другого примера щелкните [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] для просмотра элемента `<Response>`, содержащего элемент `<Say>`.)

Вместо того чтобы использовать URL-адрес, предоставленный Twilio, можно создать собственный сайт для возврата HTTP-ответов. Веб-сайт можно создавать на любом языке, который возвращает XML-ответы; в этом разделе предполагается, что для создания TwiML будет использоваться язык Python.

В следующем примере выводится ответ TwiML **Hello World** на вызов.

С помощью Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

С помощью Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Как видно из приведенного выше примера, ответ TwiML будет представлять собой простой XML-документ. Библиотека Twilio для Python содержит классы, которые создадут для вас TwiML. В следующем примере возвращается аналогичный предыдущему ответ, но используется модуль `twiml` из библиотеки Twilio для Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Дополнительные сведения о TwiML см. по следующему адресу: [https://www.twilio.com/docs/api/twiml][twiml_reference].

После настройки приложения Python для предоставления ответов TwiML используйте URL-адрес приложения как URL-адрес, передаваемый в метод `client.calls.create`. Например, URL-адрес веб-приложения с именем **MyTwiML**, развернутого в размещенной службе Azure, можно использовать в качестве объекта webhook, как показано в следующем примере:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>Руководство: использование дополнительных служб Twilio
В дополнение к приведенным примерам, Twilio предлагает веб-интерфейсы API для использования дополнительных функций Twilio в вашем приложении Azure. Дополнительные сведения см. в [документации по интерфейсу API Twilio][twilio_api].

## <a id="NextSteps"></a>Дальнейшие действия
Вы узнали основные сведения о службе Twilio. Для получения дополнительных сведений используйте следующие ссылки.

* [Рекомендации по безопасности Twilio][twilio_security_guidelines]
* [Практические руководства по Twilio и примеры кода][twilio_howtos]
* [Краткие учебники по Twilio][twilio_quickstarts]
* [Twilio на GitHub][twilio_on_github]
* [Обращение в службу поддержки Twilio][twilio_support]

[special_offer]: http://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-python/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
