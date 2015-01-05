<properties urlDisplayName="Queue Service" pageTitle="Как использовать службу очередей (Python) - Microsoft Azure" metaKeywords="Azure Queue Service messaging Python" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Python." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Queue Storage Service from Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="robmcm" />



# Использование службы хранения очередей в Python В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранения очередей Microsoft Azure. Эти примеры написаны с помощью Python API. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительную информацию об очередях см. в разделе [Дальнейшие действия][].

## Оглавление

[Что такое хранилище очередей][]   
 [Основные понятия][]   
 [Создание учетной записи хранения Azure][]   
 [Практическое руководство. Создание очереди][]   
 [Практическое руководство. Вставка сообщения в очередь][]   
 [Практическое руководство. Просмотр следующего сообщения][]   
 [Практическое руководство. Удаление следующего сообщения из очереди][]   
 [Практическое руководство. Изменение содержимого сообщения в очереди][]   
 [Практическое руководство. Дополнительные параметры для удаления сообщений из очереди][]   
 [Практическое руководство. Получение длины очереди][]   
 [Практическое руководство. Удаление очереди][]   
 [Дальнейшие действия][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"> </a>Создание учетной записи хранения Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]


**Примечание.** Информацию об установке клиентских библиотек или Python см. в [Руководстве по установке Python](../python-how-to-install/).

## <a name="create-queue"> </a>Практическое руководство. Создание очереди

Объект **QueueService** позволяет работать с очередями. Следующий код создает объект **QueueService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import QueueService

Следующий код создает объект **QueueService**, используя имя и ключ учетной записи хранения. Замените "myaccount" и "mykey" на фактические значения учетной записи и ключа.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"> </a>Практическое руководство. Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод **put\_message** для создания нового сообщения и добавления его в очередь.

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"> </a>Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод peek\_messages. По умолчанию **peek\_messages** просматривает одно сообщение.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"> </a>Практическое руководство. Удаление следующего сообщения из очереди

Ваш код удаляет сообщение из очереди в два этапа. При вызове метода **get\_messages** вы по умолчанию получаете следующее сообщение в очереди. Сообщение, возвращаемое методом get\_messages, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо вызвать метод **delete\_message**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message** сразу после обработки сообщения.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"> </a>Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Приведенный ниже код использует метод **update\_message**, чтобы обновить сообщение.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"> </a>Практическое руководство. Дополнительные параметры для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **get\_messages** используется для получения 16 сообщений за один вызов. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"> </a>Практическое руководство. Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод **get\_queue\_metadata** отправляет в службу очередей запрос на возврат метаданных об очереди, а **x-ms-approximate-messages-count** следует использовать в качестве индекса в возвращенном словаре для определения количества. В результате число указывается лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"> </a>Практическое руководство. Удаление очереди

Чтобы удалить очередь и все сообщения в ней, вызовите метод **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы ознакомились с основами использования хранилища очередей, следуйте приведенным ссылкам, чтобы узнать о том, как выполнять более сложные задачи хранения.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите блог [группы разработчиков службы хранилища Azure][]

  [Дальнейшие действия]: #next-steps
  [Что такое хранилище очередей]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Практическое руководство. Создание очереди]: #create-queue
  [Практическое руководство. Вставка сообщения в очередь]: #insert-message
  [Практическое руководство. Просмотр следующего сообщения]: #peek-message
  [Практическое руководство. Удаление следующего сообщения из очереди]: #get-message
  [Практическое руководство. Изменение содержимого сообщения в очереди]: #change-contents
  [Практическое руководство. Дополнительные параметры для удаления сообщений из очереди]: #advanced-get
  [Практическое руководство. Получение длины очереди]: #get-queue-length
  [Практическое руководство. Удаление очереди]: #delete-queue
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Блог группы разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35.1-->
