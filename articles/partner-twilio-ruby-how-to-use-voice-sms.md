<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Использование Twilio для поддержки голосовых вызовов и сообщений SMS (Ruby) - Azure" metaKeywords="Azure Twilio, телефонные вызовы Azure, телефонные звонки Azure, Azure twilio, Azure SMS, Azure SMS, голосовые вызовы, голосовые вызовы Azure, текстовые сообщения Azure, текстовые сообщения Azure" description="Узнайте, как осуществлять телефонные вызовы и отправку SMS-сообщений с помощью службы Twilio API в Azure. Примеры кода написаны на Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com" />





# Использование Twilio для поддержки голосовых возможностей и SMS в Ruby
В этом руководстве показано, как выполнять типовые задачи программирования с помощью службы Twilio API в Azure. Здесь описываются такие сценарии, как телефонный звонок и отправка SMS-сообщения. Дополнительные сведения о Twilio и использовании голосовых функций и SMS в приложениях см. в разделе [Дальнейшие действия](#NextSteps).

## Оглавление
* [Что такое Twilio?](#WhatIs)
* [Цены на Twilio](#Pricing)
* [Основные понятия](#Concepts)
* [Создание учетной записи Twilio](#CreateAccount)
* [Создание приложения Sinatra](#create_app)
* [Настройка приложения для использования библиотек Twilio](#configure_app)
* [Практическое руководство. Осуществление исходящего вызова](#howto_make_call)
* [Практическое руководство. Получение SMS-сообщения](#howto_recieve_sms)
* [Практическое руководство. Дополнительные службы Twilio](#additional_services)
* [Дальнейшие действия](#NextSteps)

## <a id="WhatIs"></a>Что такое Twilio?
Twilio - это API веб-службы телефонии, который позволяет использовать существующие языки веб-программирования и навыки для создания приложений для работы с голосовыми вызовами и SMS. Twilio - сторонняя служба (не компонент Azure и не продукт корпорации Майкрософт).

**Twilio Voice** позволяет приложениям осуществлять и принимать телефонные вызовы. **Twilio SMS** позволяет приложениям отправлять и принимать SMS-сообщения. **Twilio Client** позволяет приложениям реализовать речевую связь с помощью существующих интернет-подключений, в том числе на мобильных устройствах.

## <a id="Pricing"></a>Цены и специальные предложения Twilio
Сведения о ценах на Twilio доступны на веб-сайте [цен на Twilio] [twilio_pricing]. Клиентам Azure доступно [специальное предложение][special_offer]: бесплатный кредит на 1000 текстовых сообщений или 1000 минут входящих вызовов. Чтобы воспользоваться этим предложением или получить дополнительные сведения, посетите веб-сайт [http://ahoy.twilio.com/azure][special_offer].  

## <a id="Concepts"></a>Основные понятия
Twilio API - это интерфейс API RESTful, который предоставляет приложениям функции для работы с голосовыми вызовами и SMS. Клиентские библиотеки доступны на разных языках. Полный список см. в разделе [Библиотеки Twilio API] [twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML - это набор инструкций на основе XML, которые сообщают службе Twilio, как необходимо обработать вызов или SMS.

Например, следующие инструкции TwiML преобразуют текст **Hello World** в речь.

    ?xml version="1.0" encoding="UTF-8" ?
    <Response>
       <Say>Hello World</Say>
    </Response>

Во всех документах TwiML элемент `<Response>` является корневым. Вы будете использовать команды Twilio для определения поведения приложения.

### <a id="Verbs"></a>Команды TwiML
Команды Twilio - это XML-теги, сообщающие Twilio, что **делать**. Например, команда **&lt;Say&gt;** указывает Twilio, что необходимо воспроизвести сообщение в случае вызова. 

Ниже приведен список команд Twilio.

* **&lt;Dial&gt;**: подключение вызывающего абонента к другому телефону.
* **&lt;Gather&gt;**: сбор цифр, введенных на клавиатуре телефона.
* **&lt;Hangup&gt;**: окончание вызова.
* **&lt;Play&gt;**: воспроизведение звукового файла.
* **&lt;Pause&gt;**: бесшумное ожидание в течение указанного времени (в секундах).
* **&lt;Record&gt;**: Запись голоса вызывающего абонента и возврат URL-адреса файла, содержащего запись.
* **&lt;Redirect&gt;**: передача управления для вызова или SMS в TwiML по другому URL-адресу.
* **&lt;Reject&gt;**: отклонение входящего вызова на ваш номер Twilio без выставления счета.
* **&lt;Say&gt;**: преобразование текста в речь в вызове.
* **&lt;Sms&gt;**: отправка SMS-сообщения.

Дополнительные сведения о командах Twilio, их атрибутах и TwiML см. в статье [TwiML] [twiml]. Дополнительные сведения о Twilio API см. в разделе [Twilio API] [twilio_api].

## <a id="CreateAccount"></a>Создание учетной записи Twilio
После подготовки к получению учетной записи Twilio зарегистрируйтесь на странице [Попробуйте Twilio] [try_twilio]. Вы можете начать с бесплатной учетной записи и обновить ее позднее.

После регистрации учетной записи Twilio вы получите бесплатный номер телефона для вашего приложения. Вы также получите идентификатор безопасности учетной записи и маркер проверки подлинности. Эти элементы необходимы для вызовов Twilio API. Чтобы предотвратить несанкционированный доступ к учетной записи, храните маркер проверки подлинности в безопасности. Код безопасности учетной записи и маркер проверки подлинности отображаются на [странице учетной записи Twilio][twilio_account] в полях **КОД БЕЗОПАСНОСТИ УЧЕТНОЙ ЗАПИСИ** и **МАРКЕР ПРОВЕРКИ ПОДЛИННОСТИ** соответственно.

### <a id="VerifyPhoneNumbers"></a>Проверка телефонных номеров
Помимо номера, который вы сообщаете Twilio, вы также можете подтвердить числа, которые вы контролируете (например, ваш номер мобильного или домашнего телефона), для использования в других приложениях. 

Сведения о том, как проверить номер телефона, см. в разделе [Управление номерами] [verify_phone].

## <a id="create_app"></a>Создание приложения Ruby
Приложение Ruby, использующее службу Twilio и работающее на Azure, ничем не отличается от других приложений Ruby, использующих службу Twilio. Хотя службы Twilio поддерживают RESTful и могут вызываться из Ruby несколькими способами, в этой статье основное внимание уделяется использованию служб Twilio с помощью [вспомогательной библиотеки Twilio для Ruby][twilio_ruby].

Прежде всего [, настройте новую виртуальную машину Linux Azure][azure_vm_setup] на размещение вашего нового приложения Ruby. Игнорируйте инструкции, связанные с созданием приложения Rails, достаточно настроить виртуальную машину. Обязательно создайте конечную точку с внешним портом 80 и внутренним портом 5000.

В приведенных ниже примерах мы будем использовать [Sinatra][sinatra], очень простую веб-платформу для Ruby. Однако вы можете использовать вспомогательную библиотеку Twilio для Ruby с любой другой веб-платформой, в том числе Ruby on Rails.

Войдите на новую виртуальную машину по протоколу SSH и создайте каталог для нового приложения. В этом каталоге создайте файл с именем Gemfile и скопируйте в него следующий код:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

В окне командной строки введите `bundle install`. Эта команда установит описанные выше зависимости. Затем создайте файл с именем `web.rb`. В нем будет размещен код веб-приложения. Вставьте в него следующий код:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

На этом этапе вы должны выполнить команду `ruby web.rb -p 5000`. Команда настроит небольшой веб-сервер с портом 5000. Вы сможете просмотреть приложение в браузере, перейдя по URL-адресу, настроенному для виртуальной машины Azure. После открытия веб-приложения в браузере вы готовы к созданию приложения Twilio.

## <a id="configure_app"></a>Настройка приложения для использования Twilio
Вы можете настроить веб-приложение для использования библиотеки Twilio, добавив в файл `Gemfile` следующую строку:

    gem 'twilio-ruby'

В окне командной строки введите `bundle install`. Теперь откройте `web.rb` и добавьте следующую строку в начало файла:

    require 'twilio-ruby'

Теперь вы готовы к использованию вспомогательной библиотеки Twilio для Ruby в веб-приложении.

## <a id="howto_make_call"></a>Практическое руководство. Осуществление исходящего вызова
Далее показано, как осуществлять исходящий вызов. К основным понятиям относятся использование вспомогательной библиотеки Twilio для Ruby для вызова API REST и отрисовки TwiML. Замените значения телефонных номеров **From** (От) и **To** (Кому) и проверьте номер телефона **From** (От) для учетной записи Twilio до выполнения кода.

Добавьте эту функцию в файл `web.md`:

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
    
Если открыть страницу `http://yourdomain.cloudapp.net/make_call` в браузере, вы активируете вызов Twilio API для осуществления звонка. Назначение первых двух параметров в `client.account.calls.create` очевидно: Номер, с которого осуществляется `вызов`, и номер, на который адресуется `вызов`. 

Третий параметр (`url`) - это URL-адрес, который используется Twilio для получения инструкций по действиям после вызова. В данном случае мы задаем URL-адрес (`http://yourdomain.cloudapp.net`), которые возвращает обычный документ TwiML и использует команду `<Say>` для преобразования текста в речь и произнесения "Привет, обезьяна", когда абонент ответит на звонок.

## <a id="howto_recieve_sms"></a>Практическое руководство. Получение SMS-сообщения
В предыдущем примере мы инициировали **исходящий** звонок. На этот раз мы используем номер телефона, предоставленный Twilio во время регистрации, для обработки **входящего** SMS-сообщения.

Во-первых, войдите в [панель мониторинга Twilio][twilio_account]. Щелкните "Номера" на верхней панели навигации и выберите предоставленный вам номер Twilio. Вы увидите два URL-адреса, которые можно настроить: URL-адрес запроса голосового вызова и URL-адрес запроса SMS. Эти URL-адреса используются Twilio при осуществлении голосового вызова или отправке SMS-сообщения на ваш номер. Эти адреса также называют "веб-привязками".

Мы хотели бы обработать входящие SMS-сообщения, поэтому изменим URL-адрес на `http://yourdomain.cloudapp.net/sms_url`. Нажмите кнопку "Сохранить изменения" в нижней части страницы. Теперь вернемся к файлу `web.rb` и напишем код, чтобы наше приложение могло обработать SMS-сообщение:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

После внесения изменений перезапустите веб-приложение. Теперь возьмите телефон и отправьте SMS на ваш номер Twilio. Вы должны быстро получить ответ: "Hey, thanks for the ping! Twilio and Azure rock!" (Спасибо за сообщение. Twilio и Azure лучше всех!).

## <a id="additional_services"></a>Практическое руководство. Использование дополнительных служб Twilio
Помимо примеров, перечисленных здесь, Twilio предлагает веб-интерфейсы API, с помощью которых вы можете использовать дополнительные функции Twilio из вашего приложения Azure. Дополнительные сведения см. в [документации по Twilio API] [twilio_api_documentation].

### <a id="NextSteps"></a>Дальнейшие действия
Вы узнали основные сведения о службе Twilio. Для получения дополнительных сведений используйте следующие ссылки.

* [Рекомендации по безопасности Twilio] [twilio_security_guidelines]
* [Практические руководства и примеры кода Twilio] [twilio_howtos]
* [Краткие учебники по Twilio][twilio_quickstarts] 
* [Twilio на GitHub] [twilio_on_github]
* [Обращение в службу поддержки Twilio] [twilio_support]

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
[azure_vm_setup]: http://www.windowsazure.com/ru-ru/develop/ruby/tutorials/web-app-with-linux-vm/
