<properties
	pageTitle="Использование хранилища очередей из Python | Microsoft Azure"
	description="Узнайте о том, как использовать службу очередей Azure из Python для создания и удаления очередей, а также для вставки, получения и удаления сообщений."
	services="storage"
	documentationCenter="python"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="cbrooks;robinsh"/>

# Использование хранилища очередей из Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Azure. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python]. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Практическое руководство. Создание очереди

Объект **QueueService** позволяет работать с очередями. Следующий код создает объект **QueueService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage.queue import QueueService

Следующий код создает объект **QueueService**, используя имя и ключ доступа учетной записи. Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Практическое руководство. Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод **put\_message** для создания нового сообщения и добавления его в очередь.

	queue_service.put_message('taskqueue', u'Hello World')


## Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **peek\_messages**. По умолчанию метод **peek\_messages** просматривает одно сообщение.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.content)


## Практическое руководство. Удаление следующего сообщения из очереди

Ваш код удаляет сообщение из очереди в два этапа. При вызове метода **get\_messages** вы по умолчанию получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **get\_messages**, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **delete\_message**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message** сразу после обработки сообщения.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.id, message.pop_receipt)

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **get\_messages** используется для получения 16 сообщений за один вызов. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

	messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
	for message in messages:
		print(message.content)
		queue_service.delete_message('taskqueue', message.id, message.pop_receipt)		


## Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Приведенный ниже код использует метод **update\_message**, чтобы обновить сообщение. Время ожидания видимости равно 0. Это означает, что сообщение появится немедленно, и содержимое обновится.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')

## Практическое руководство. Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод **get\_queue\_metadata** запрашивает у службы очередей метаданные очереди и **approximate\_message\_count**. В результате число указывается лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

	metadata = queue_service.get_queue_metadata('taskqueue')
	count = metadata.approximate_message_count

## Практическое руководство. Удаление очереди

Чтобы удалить очередь и все сообщения в ней, вызовите метод **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Дальнейшие действия

Вы ознакомились с основными понятиями хранилища очередей. Дополнительные сведения см. по следующим ссылкам.

- [Центр по разработке для Python](/develop/python/)
- [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Блог рабочей группы службы хранилища Azure]
- [пакет SDK для службы хранилища Microsoft Azure для Python]

[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->