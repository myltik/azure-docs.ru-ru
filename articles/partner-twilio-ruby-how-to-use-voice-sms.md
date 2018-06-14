---
title: Использование Twilio для поддержки голосовых вызовов и SMS (Ruby) | Документация Майкрософт
description: Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры кода написаны на Ruby.
services: ''
documentationcenter: ruby
author: devinrader
manager: twilio
editor: ''
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: 69e50e7fe0e1f302e96c309878b8dea6869dff4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23039889"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Использование Twilio для поддержки голосовых возможностей и SMS в Ruby
В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия](#NextSteps).

## <a id="WhatIs"></a>Что такое Twilio?
Twilio — это API веб-службы телефонии, позволяющий использовать существующие языки веб-программирования и навыки для создания приложений для работы с голосовыми вызовами и SMS. Twilio — это сторонняя служба (не компонент Azure и не продукт Майкрософт).

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет приложениям реализовать речевую связь с помощью существующих интернет-подключений, в том числе на мобильных устройствах.

## <a id="Pricing"></a>Цены и специальные предложения Twilio
Сведения о ценах на Twilio доступны на веб-сайте [цен на Twilio][twilio_pricing]. Клиентам Azure доступно [специальное предложение][special_offer]: бесплатный кредит на 1000 текстовых сообщений или 1000 минут входящих вызовов. Чтобы воспользоваться этим предложением или получить дополнительные сведения, посетите сайт [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Основные понятия
Twilio API — это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Полный список клиентских библиотек API Twilio, которые доступны на разных языках, см. на [этой странице][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML — это набор инструкций на основе XML, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Во всех документах TwiML элемент `<Response>` является корневым. Вы будете использовать команды Twilio для определения поведения приложения.

### <a id="Verbs"></a>Команды TwiML
Команды Twilio — это XML-теги, сообщающие Twilio, что **делать**. Например, команда **&lt;Say&gt;** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова. 

Ниже приведен список команд Twilio.

* **&lt;Dial&gt;**: подключение вызывающего абонента к другому телефону.
* **&lt;Gather&gt;**: сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;**: окончание вызова.
* **&lt;Play&gt;**: воспроизведение звукового файла.
* **&lt;Pause&gt;**: бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Record&gt;**: запись голоса вызывающего абонента и возврат URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;**: передача управления для вызова или SMS в TwiML по другому URL-адресу.
* **&lt;Reject&gt;**: отклонение входящего вызова на ваш номер Twilio без выставления счета.
* **&lt;Say&gt;**: преобразование текста в речь при вызове.
* **&lt;Sms&gt;**: отправка SMS-сообщения.

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. на странице [TwiML][twiml]. Дополнительные сведения об API Twilio см. на [этой странице][twilio_api].

## <a id="CreateAccount"></a>Создание учетной записи Twilio
После подготовки к получению учетной записи Twilio зарегистрируйтесь на [странице получения пробной версии Twilio ][try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

После регистрации учетной записи Twilio вы получите бесплатный номер телефона для вашего приложения. Вы также получите идентификатор безопасности учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Идентификатор безопасности учетной записи и маркер проверки подлинности отображаются на [странице учетной записи Twilio][twilio_account] в полях **ACCOUNT SID** (Идентификатор безопасности учетной записи) и **AUTH TOKEN** (Маркер проверки подлинности) соответственно.

### <a id="VerifyPhoneNumbers"></a>Проверка телефонных номеров
В дополнение к номеру, предоставленному Twilio, вы также можете проверить номера, которыми вы управляете (например, номер сотового или домашнего телефона), для использования в приложениях. 

Сведения о проверке номера телефона см. на [этой странице][verify_phone].

## <a id="create_app"></a>Создание приложения Ruby
Приложение Ruby, которое использует службу Twilio и выполняется в Azure, не отличается от других приложений Ruby, которые используют службу Twilio. Хотя службы Twilio поддерживают REST и могут вызываться из Ruby несколькими способами, в этой статье основное внимание уделяется использованию служб Twilio со [вспомогательной библиотекой Twilio для Ruby][twilio_ruby].

Сначала [установите новую виртуальную машину Linux Azure][azure_vm_setup] в качестве узла для нового веб-приложения Ruby. Игнорируйте инструкции, связанные с созданием приложения Rails, достаточно настроить виртуальную машину. Обязательно создайте конечную точку с внешним портом 80 и внутренним портом 5000.

В примерах ниже мы будем использовать [Sinatra][sinatra] — очень простую веб-платформу для Ruby. Однако вы можете использовать вспомогательную библиотеку Twilio для Ruby с любой другой веб-платформой, в том числе Ruby on Rails.

Войдите на новую виртуальную машину по протоколу SSH и создайте каталог для нового приложения. В этом каталоге создайте файл с именем Gemfile и скопируйте в него следующий код:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

В командной строке запустите `bundle install`. Эта команда установит описанные выше зависимости. Затем создайте файл с именем `web.rb`. В нем будет размещен код веб-приложения. Вставьте в него следующий код:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

На этом этапе можно будет выполнить команду `ruby web.rb -p 5000`. Она настроит небольшой веб-сервер с портом 5000. Вы сможете просмотреть приложение в браузере, перейдя по URL-адресу, настроенному для виртуальной машины Azure. После открытия веб-приложения в браузере вы готовы к созданию приложения Twilio.

## <a id="configure_app"></a>Настройка приложения для использования Twilio
Можно настроить веб-приложение для использования библиотеки Twilio, добавив к `Gemfile` следующую строку:

    gem 'twilio-ruby'

В командной строке запустите `bundle install`. Теперь откройте `web.rb` и следующую строку в начале:

    require 'twilio-ruby'

Теперь вы готовы к использованию вспомогательной библиотеки Twilio для Ruby в веб-приложении.

## <a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова
Далее показано, как осуществлять исходящий вызов. К основным понятиям относятся использование вспомогательной библиотеки Twilio для Ruby для вызова API REST и отрисовки TwiML. Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

Добавьте эту функцию в `web.md`:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Если открыть `http://yourdomain.cloudapp.net/make_call` в браузере, вы активируете вызов API Twilio для осуществления звонка. Первые два параметра в `client.account.calls.create` говорят сами за себя: номер вызова `from` и номер вызова `to`. 

Третий параметр (`url`) — URL-адрес, который Twilio запрашивает для получения инструкций по действиям после подключения вызова. В этом случае мы задаем URL-адрес (`http://yourdomain.cloudapp.net`), который возвращает простой документ TwiML и использует команду `<Say>`, чтобы преобразовать текст в речь и сказать "Hello Monkey" для принимающего вызов абонента.

## <a id="howto_recieve_sms"></a>Руководство: получение SMS-сообщения
В предыдущем примере мы инициировали **исходящий** звонок. На этот раз мы используем номер телефона, предоставленный Twilio во время регистрации, для обработки **входящего** SMS-сообщения.

Сначала войдите на [панель мониторинга Twilio][twilio_account]. Щелкните "Номера" на верхней панели навигации и выберите предоставленный вам номер Twilio. Вы увидите два URL-адреса, которые можно настроить: URL-адрес запроса голосового вызова и URL-адрес запроса SMS. Эти URL-адреса используются Twilio при осуществлении голосового вызова или отправке SMS-сообщения на ваш номер. Эти адреса также называют "веб-привязками".

Мы хотели бы обработать входящие SMS-сообщения, поэтому изменим URL-адрес на `http://yourdomain.cloudapp.net/sms_url`. Нажмите кнопку "Сохранить изменения" в нижней части страницы. Теперь вернемся к `web.rb` и напишем код, чтобы наше приложение могло обработать это:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

После внесения изменений перезапустите веб-приложение. Теперь возьмите телефон и отправьте SMS на ваш номер Twilio. Вы должны быстро получить ответ: "Hey, thanks for the ping! Twilio and Azure rock!" (Спасибо за сообщение. Twilio и Azure лучше всех!).

## <a id="additional_services"></a>Руководство: использование дополнительных служб Twilio
В дополнение к приведенным примерам, Twilio предлагает веб-интерфейсы API для использования дополнительных функций Twilio в вашем приложении Azure. Дополнительные сведения см. в [документации по интерфейсу API Twilio][twilio_api_documentation].

### <a id="NextSteps"></a>Дальнейшие действия
Вы узнали основные сведения о службе Twilio. Для получения дополнительных сведений используйте следующие ссылки.

* [Рекомендации по безопасности Twilio][twilio_security_guidelines]
* [Практические руководства по Twilio и пример кода][twilio_howtos]
* [Краткие учебники по Twilio][twilio_quickstarts] 
* [Twilio на GitHub][twilio_on_github]
* [Обращение в службу поддержки Twilio][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/
