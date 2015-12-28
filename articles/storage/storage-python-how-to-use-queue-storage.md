<properties 
	pageTitle="Использование хранилища очередей из Python | Microsoft Azure" 
	description="Узнайте о том, как использовать службу очередей Azure из Python для создания и удаления очередей, а также для вставки, получения и удаления сообщений." 
	services="storage" 
	documentationCenter="python" 
	authors="emgerner-msft" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/11/2015" 
	ms.author="emgerner"/>

# Использование хранилища очередей из Python

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Azure. Примеры написаны на Python и используют пакет [Хранилище Azure для Python][]. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия]].

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


> [AZURE.NOTE]Если требуется установить Python или [пакет Azure для Python][], см. [Руководство по установке Python](../python-how-to-install.md).

## Практическое руководство. Создание очереди

Объект **QueueService** позволяет работать с очередями. Следующий код создает объект **QueueService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage.queue import QueueService

Следующий код создает объект **QueueService**, используя имя и ключ доступа учетной записи. Замените myaccount и mykey фактическими значениями учетной записи и ключа.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## Практическое руководство. Вставка сообщения в очередь

Чтобы вставить сообщение в очередь, используйте метод **put\_message** для создания нового сообщения и добавления его в очередь.

	queue_service.put_message('taskqueue', 'Hello World')


## Практическое руководство. Просмотр следующего сообщения

Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **peek\_messages**. По умолчанию метод **peek\_messages** просматривает одно сообщение.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## Практическое руководство. Удаление следующего сообщения из очереди

Ваш код удаляет сообщение из очереди в два этапа. При вызове метода **get\_messages** вы по умолчанию получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **get\_messages**, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **delete\_message**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message** сразу после обработки сообщения.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## Практическое руководство. Изменение содержимого сообщения в очереди

Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Приведенный ниже код использует метод **update\_message**, чтобы обновить сообщение.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## Практическое руководство. Использование дополнительных параметров для удаления сообщений из очереди

Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **get\_messages** используется для получения 16 сообщений за один вызов. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## Практическое руководство. Получение длины очереди

Вы можете узнать приблизительное количество сообщений в очереди. Метод **get\_queue\_metadata** отправляет в службу очередей запрос на возврат метаданных об очереди, а **x-ms-approximate-messages-count** следует использовать в качестве индекса в возвращенном словаре для определения количества. В результате число указывается лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## Практическое руководство. Удаление очереди

Чтобы удалить очередь и все сообщения в ней, вызовите метод **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## Дальнейшие действия

Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   Посетите [блог команды разработчиков хранилища Azure][].

Дополнительные сведения см. в [Центре разработчика Python](/develop/python/).

[блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет Azure для Python]: https://pypi.python.org/pypi/azure
[Хранилище Azure для Python]: https://pypi.python.org/pypi/azure-storage
 

<!---HONumber=AcomDC_1217_2015-->