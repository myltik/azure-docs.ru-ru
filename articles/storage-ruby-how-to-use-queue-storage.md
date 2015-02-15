<properties 
	pageTitle="Как использовать службу очередей (Ruby - Microsoft Azure" 
	description="Вы узнаете, как использовать службы очередей Azure для создания и удаления очередей, вставки, получения и удаления сообщений. Примеры кода написаны на Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# Использование службы хранения очередей в Ruby

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Microsoft Azure. Примеры написаны с помощью Ruby Azure API.
Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительную информацию об очередях см. в разделе [Дальнейшие действия](#next-steps) .

## Оглавление

* [Что такое хранилище очередей](#what-is)
* [Основные понятия](#concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения Ruby](#create-a-ruby-application)
* [Настройка приложения для доступа к хранилищу](#configure-your-application-to-access-storage)
* [Настройка подключения к службе хранилища Azure](#setup-a-windows-azure-storage-connection)
* [Практическое руководство. Создание очереди](#how-to-create-a-queue)
* [Практическое руководство. Вставка сообщения в очередь](#how-to-insert-a-message-into-a-queue)
* [Практическое руководство. Просмотр следующего сообщения](#how-to-peek-at-the-next-message)
* [Практическое руководство. Удаление следующего сообщения из очереди](#how-to-dequeue-the-next-message)
* [Практическое руководство. Изменение содержимого сообщения в очереди](#how-to-change-the-contents-of-a-queued-message)
* [Практическое руководство. Дополнительные параметры для удаления сообщений из очереди](#how-to-additional-options-for-dequeuing-messages)
* [Практическое руководство. Получение длины очереди](#how-to-get-the-queue-length)
* [Практическое руководство. Удаление очереди](#how-to-delete-a-queue)
* [Дальнейшие действия](#next-steps)

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>Создание учетной записи хранения Azure

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Создание приложения Ruby

Создайте приложение Ruby. Указания 
см. в разделе [Создание приложения Ruby в Azure](/ru-ru/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### Использование RubyGems для получения пакета

1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).

2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### Импорт пакета

Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure

Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS_KEY**, 
чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, нужно указать информацию об учетной записи перед использованием **Azure::QueueService** с помощью следующего кода:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Для получения этих значений:

1. Выполните вход на [портал управления Azure](https://manage.windowsazure.com/).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ** в нижней части области навигации.
4. В открывшемся диалоговом окне вы увидите имя учетной записи хранения, первичный ключ доступа и вторичный ключ доступа. В качестве ключа доступа можно использовать либо первичный, либо вторичный ключ.

## <a id="how-to-create-a-queue"></a>Практическое руководство. Создание очереди

Следующий пример кода создает объект **Azure::QueueService**, который позволяет работать с очередями.

	azure_queue_service = Azure::QueueService.new

Используйте метод **create_queue()**, чтобы создать очередь с указанным именем.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>Практическое руководство. Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод **create_message()** для создания нового сообщения и добавления его в очередь.

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **peek\_messages()**. По умолчанию **peek\_messages()** просматривает одно сообщение. Вы также можете указать количество сообщений для просмотра.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>Практическое руководство. Удаление следующего сообщения из очереди

Вы можете удалить сообщение из очереди в два этапа.

1. При вызове метода **list\_messages()** вы по умолчанию получаете следующее сообщение в очереди. Вы также можете указать количество сообщений для получения. Сообщения, возвращаемые методом **list\_messages()**, становятся невидимыми для другого кода, считывающего сообщения из этой очереди. Время ожидания видимости (в секундах) передается в качестве параметра.

2. Чтобы завершить удаление сообщения из очереди, необходимо вызвать метод **delete_message()**.

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message()** сразу после обработки сообщения.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Приведенный ниже код использует метод **update_message()**, чтобы обновить сообщение. Этот метод возвращает последовательность, содержащую подтверждение получения сообщения очереди, дату и время в формате UTC, представляющие время, когда сообщение будет видимо в очереди.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>Практическое руководство. Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами.

1. Можно получить пакет сообщения.

2. Можно задать более длительное или короткое время ожидания невидимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения.

В следующем примере кода метод **list\_messages()** используется, чтобы получить 15 сообщений в одном вызове. Затем код выводит и удаляет все сообщения. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>Практическое руководство. Получение длины очереди

Вы можете получить приблизительное количество сообщений в очереди. Метод **get\_queue\_metadata()** запрашивает у службы очереди приблизительное количество сообщений и метаданные очереди.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>Практическое руководство. Удаление очереди

Для удаления очереди и всех сообщений в ней вызовите метод **delete\_queue()** в объекте очереди.

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранение и доступ к данным в Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx)
- Посетите [блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Посетите репозиторий [Пакет SDK для Azure для Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub.

Сравнение службы очередей Azure, описанной в этой статье, и очередей Service Bus Azure, описанных в статье [Использование очередей Service Bus](/ru-ru/develop/ruby/how-to-guides/service-bus-queues/), см. в статье [Очереди Azure и очереди шины обслуживания - сходство и отличия](http://msdn.microsoft.com/ru-ru/library/windowsazure/hh767287.aspx).

<!--HONumber=42-->
