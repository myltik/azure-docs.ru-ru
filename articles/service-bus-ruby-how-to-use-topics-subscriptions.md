<properties linkid="dev-ruby-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Использование разделов и подписок Service Bus

В этом руководстве показано, как использовать разделы и подписки Service Bus в приложениях Ruby. В этой статье описаны такие сценарии, как **создание разделов и подписок, создание фильтров подписок, отправка сообщений** в раздел, **получение сообщений от подписки** и **удаление разделов и подписок**. Дополнительные сведения о разделах и подписках см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

-   [Что такое разделы и подписки Service Bus][Что такое разделы и подписки Service Bus]
-   [Создание пространства имен службы][Создание пространства имен службы]
-   [Получение учетных данных управления по умолчанию для пространства имен][Получение учетных данных управления по умолчанию для пространства имен]
-   [Создание приложения Ruby][Создание приложения Ruby]
-   [Настройка приложения для использования шины обслуживания][Настройка приложения для использования шины обслуживания]
-   [Настройка подключения к Service Bus Azure][Настройка подключения к Service Bus Azure]
-   [Создание раздела][Создание раздела]
-   [Создание подписок][Создание подписок]
-   [Отправка сообщений в раздел][Отправка сообщений в раздел]
-   [Получение сообщений от подписки][Получение сообщений от подписки]
-   [Обработка сбоев приложения и нечитаемых сообщений][Обработка сбоев приложения и нечитаемых сообщений]
-   [Удаление разделов и подписок][Удаление разделов и подписок]
-   [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span id="create-a-ruby-application"></span></a>Создание приложения Ruby

Создайте приложение Ruby. Инструкции см. в разделе [Создание приложения Ruby в Azure][Создание приложения Ruby в Azure].

## <span id="configure-your-application-to-use-service-bus"></span></a>Настройка приложения для использования Service Bus

Для использования Azure Service Bus необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### Использование RubyGems для получения пакета

1.  Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).

2.  Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### Импорт пакета

Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Настройка подключения к Service Bus Azure

Модуль Azure считывает переменные среды **AZURE\_SERVICEBUS\_NAMESPACE** и **AZURE\_SERVICEBUS\_ACCESS\_KEY**
для получения сведений, необходимых для подключения к пространству имен шины обслуживания Azure. Если эти переменные среды не заданы, необходимо указать сведения о пространстве имен перед использованием **Azure::ServiceBusService** с помощью следующего кода:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-topic"></span></a>Создание раздела

Объект **Azure::ServiceBusService** позволяет работать с разделами. Следующий код создает объект **Azure::ServiceBusService**. Чтобы создать раздел, используйте метод **create\_topic()**. В следующем примере создается раздел или выводится ошибка, если она возникла.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

Можно также передать объект **Azure::ServiceBus::Topic** с дополнительными параметрами, которые позволяют переопределить параметры раздела по умолчанию, например срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни 1 минута.

    topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <span id="how-to-create-subscriptions"></span></a>Создание подписок

Подписки разделов также создаются с помощью объекта **Azure::ServiceBusService**. У подписок есть имена, и они могут использовать дополнительный фильтр, ограничивающий набор сообщений, доставляемых в виртуальную очередь подписки.

**Примечание**
.Подписки сохраняются и продолжают существовать до их удаления или удаления раздела, с которым они связаны. Если приложение содержит логику для создания подписки, оно сначала должно проверить, существует ли подписка, используя метод getSubscription.

### Создание подписки с фильтром по умолчанию (MatchAll)

Фильтр **MatchAll** является фильтром по умолчанию, используемым, если при создании новой подписки не указан фильтр. Если используется фильтр **MatchAll**, все сообщения, опубликованные в разделе, помещаются в виртуальную очередь подписки. В следующем примере создается подписка с именем "all-messages" и используется фильтр по умолчанию **MatchAll**.

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "all-messages")

### <span id="how-to-create-subscriptions"></span></a>Создание подписок с фильтрами

Вы также можете настроить фильтры, позволяющие определять, какие сообщения, отправленные в раздел, будут отображаться в определенной подписке раздела.

Самый гибкий тип фильтра, который поддерживается подписками, — это **Azure::ServiceBus::SqlFilter**, реализующий подмножество SQL92. Фильтры SQL работают со свойствами сообщений, которые опубликованы в разделе. Дополнительные сведения о выражениях, которые можно использовать с SQL-фильтром, см. в описании синтаксиса [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Фильтры можно добавить в подписку с помощью метода **create\_rule()** объекта **Azure::ServiceBusService**. Этот метод позволяет добавлять новые фильтры в существующую подписку.

**Примечание.**
Поскольку фильтр по умолчанию применяется автоматически ко всем новым подпискам, сначала необходимо удалить фильтр по умолчанию, или **MatchAll** переопределит все остальные фильтры, которые вы укажете. Вы можете удалить правило по умолчанию с помощью метода **delete\_rule()** объекта **Azure::ServiceBusService**.

В примерах ниже создается подписка с именем high-messages с фильтром **Azure::ServiceBus::SqlFilter**, который выбирает только сообщения, значение настраиваемого свойства **message\_number** которых больше 3.

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "high-messages")
    azure_service_bus_service.delete_rule("test-topic", "high-messages", 
      "$Default")

    rule = Azure::ServiceBus::Rule.new("high-messages-rule")
    rule.topic = "test-topic"
    rule.subscription = "high-messages"
    rule.filter = Azure::ServiceBus::SqlFilter.new({ 
      :sql_expression => "message_number > 3" })
    rule = azure_service_bus_service.create_rule(rule)

Следующий пример создает подписку с именем low-messages с фильтром **Azure::ServiceBus::SqlFilter**, который выбирает только сообщения, значение свойства **message\_number** которых меньше или равно 3.

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "low-messages")
    azure_service_bus_service.delete_rule("test-topic", "low-messages", 
      "$Default")

    rule = Azure::ServiceBus::Rule.new("low-messages-rule")
    rule.topic = "test-topic"
    rule.subscription = "low-messages"
    rule.filter = Azure::ServiceBus::SqlFilter.new({ 
      :sql_expression => "message_number <= 3" })
    rule = azure_service_bus_service.create_rule(rule)

Если сообщение будет отправлено в раздел "test-topic", оно всегда будет доставляться в приемники, подписанные на подписку раздела "all-messages", и в отдельные приемники, подписанные на подписки разделов "high-messages" и "low-messages" (в зависимости от содержимого сообщений).

## <span id="how-to-send-messages-to-a-topic"></span></a>Отправка сообщений в раздел

Чтобы отправить сообщение в раздел Service Bus, приложение должно использовать метод **send\_topic\_message()** объекта **Azure::ServiceBusService**. Сообщения, отправленные в разделы Service Bus — это объекты **Azure::ServiceBus::BrokeredMessage**. У объектов **Azure::ServiceBus::BrokeredMessage** есть набор стандартных свойств (например, **label** и **time\_to\_live**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из строковых данных. Приложение может задать текст сообщения, передав строковое значение в метод **send\_topic\_message()**, а все необходимые стандартные свойства будут заполнены значениями по умолчанию.

В следующем примере показано, как отправить 5 тестовых сообщений в раздел "test-topic". Обратите внимание, что значение настраиваемого свойства **message\_number** каждого сообщения зависит от итерации цикла (это определяет, получит ли подписка сообщение).

    5.times do |i|
      message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
        { :message_number => i })
      azure_service_bus_service.send_topic_message("test-topic", message)
    end

Разделы Service Bus поддерживают максимальный размер сообщения 256 МБ (максимальный размер заголовка, содержащего стандартные и настраиваемые свойства приложения — 64 МБ). Ограничения на количество сообщений в разделе нет, но есть максимальный общий размер сообщений, содержащихся в разделе. Этот размер раздела определяется при создании с верхним пределом 5 ГБ.

## <span id="how-to-receive-messages-from-a-subscription"></span></a>Получение сообщений из подписки

Сообщения извлекаются из подписки с помощью метода **receive\_subscription\_message()** объекта **Azure::ServiceBusService**. По умолчанию сообщения считываются и блокируются без удаления из подписки. Вы можете читать сообщения и удалять их из подписки, установив для параметра **peek\_lock** значение **false**.

По умолчанию чтение и удаление выполняются в 2 этапа, что позволяет поддерживать приложения, не способные обрабатывать отсутствующие сообщения. Получив запрос, Service Bus находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Когда приложение завершает обработку сообщения (или сохраняет его для будущей обработки), она завершает второй этап процесса получения, вызывая метод **delete\_subscription\_message()** и указывая сообщение для удаления в качестве параметра. Метод **delete\_subscription\_message()** помечает сообщение как используемое и удаляет его из подписки.

Если параметр **:peek\_lock** имеет значение **false**, чтение и удаление сообщения становится очень простым процессом и оптимально подходит для сценариев, в которых приложение может не обработать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку Service Bus помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

В примере ниже показано, как можно получать и обрабатывать сообщения с помощью метода **receive\_subscription\_message()**. Этот пример сначала получает и удаляет сообщение из подписки low-messages, установив для параметра **:peek\_lock** значение **false**, а затем получает другое сообщение из high-messages и удаляет сообщение с помощью метода **delete\_subscription\_message()**.

    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a>Обработка сбоев приложения и нечитаемых сообщений

Service Bus предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если приложение-получатель по каким-либо причинам не может обработать сообщение, то оно может вызвать метод **unlock\_subscription\_message()** объекта **Azure::ServiceBusService**. После этого Service Bus разблокирует сообщение в подписке и снова сделает его доступным для получения тем же или другим приложением.

Кроме того, с сообщением, заблокированным в подписке, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода **delete\_subscription\_message()**, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют **обработать хотя бы один раз**, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **message\_id** сообщения, которое остается постоянным для различных попыток доставки.

## <span id="how-to-delete-topics-and-subscriptions"></span></a>Удаление разделов и подписок

Разделы и подписки являются постоянными, и их необходимо удалять явным образом на [портале управления Azure][портале управления Azure] или программными средствами. В приведенном ниже примере показано, как удалить раздел с именем "test-topic".

    azure_service_bus_service.delete_topic("test-topic")

При удалении раздела также удаляются все подписки, зарегистрированные в этом разделе. Подписки также можно удалять по отдельности. В следующем примере кода показано, как удалить подписку с именем "high-messages" из раздела "test-topic":

    azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <span id="NextSteps"></span></a> Дальнейшие действия

Вы узнали основные сведения о разделах Service Bus. Для получения дополнительных сведений используйте следующие ссылки.

-   См. справочник MSDN: [очереди, разделы и подписки][очереди, разделы и подписки]
-   Справочник API для [SqlFilter][SqlFilter]
-   Посетите репозиторий [Azure SDK для Ruby][Azure SDK для Ruby] на веб-сайте GitHub

  [Дальнейшие действия]: #NextSteps
  [Что такое разделы и подписки Service Bus]: #what-are-service-bus-topics
  [Создание пространства имен службы]: #create-a-service-namespace
  [Получение учетных данных управления по умолчанию для пространства имен]: #obtain-default-credentials
  [Создание приложения Ruby]: #create-a-ruby-application
  [Настройка приложения для использования шины обслуживания]: #configure-your-application-to-use-service-bus
  [Настройка подключения к Service Bus Azure]: #setup-a-windows-azure-service-bus-connection
  [Создание раздела]: #how-to-create-a-topic
  [Создание подписок]: #how-to-create-subscriptions
  [Отправка сообщений в раздел]: #how-to-send-messages-to-a-topic
  [Получение сообщений от подписки]: #how-to-receive-messages-from-a-subscription
  [Обработка сбоев приложения и нечитаемых сообщений]: #how-to-handle-application-crashes-and-unreadable-messages
  [Удаление разделов и подписок]: #how-to-delete-topics-and-subscriptions
  [howto-service-bus-topics]: ../includes/howto-service-bus-topics.md
  [Создание приложения Ruby в Azure]: /ru-ru/develop/ruby/tutorials/web-app-with-linux-vm/
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [портале управления Azure]: https://manage.windowsazure.com
  [очереди, разделы и подписки]: http://msdn.microsoft.com/ru-ru/library/windowsazure/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ru-ru/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Azure SDK для Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
