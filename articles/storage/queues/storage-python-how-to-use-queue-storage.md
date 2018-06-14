---
title: Как использовать хранилище очередей из Python | Документация Майкрософт
description: Узнайте о том, как использовать службу очередей Azure из Python для создания и удаления очередей, а также для вставки, получения и удаления сообщений.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
ms.locfileid: "25986755"
---
# <a name="how-to-use-queue-storage-from-python"></a>Использование хранилища очередей из Python
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Azure. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python]. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Скачивание и установка пакета SDK службы хранилища Azure для Python

Для установки пакета SDK службы хранилища Azure для Python потребуется Python 2.7, 3.3, 3.4, 3.5 или 3.6. Как правило, этот пакет бывает 4 видов: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` и `azure-storage-queue`. В этом руководстве мы будем использовать пакет `azure-storage-queue`.
 
### <a name="install-via-pypi"></a>Установка с помощью PyPI

Для установки с помощью индекса пакетов Python (PyPI) введите:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Если вы обновляете пакет SDK службы хранилища Azure для Python с версии 0.36 или более ранней, сначала удалите эту версию с помощью команды `pip uninstall azure-storage`, так как этот пакет больше не выпускается отдельно.
> 
> 

Другие методы установки см. в статье [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) (Пакет SDK службы хранилища Microsoft Azure для Python) на сайте Github.

## <a name="how-to-create-a-queue"></a>Практическое руководство. Создание очереди
Объект **QueueService** позволяет работать с очередями. Следующий код создает объект **QueueService** . Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

```python
from azure.storage.queue import QueueService
```

Следующий код создает объект **QueueService** , используя имя и ключ доступа учетной записи. Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Практическое руководство. Вставка сообщения в очередь
Чтобы вставить сообщение в очередь, используйте метод **put\_message** для создания нового сообщения и добавления его в очередь.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Практическое руководство. Просмотр следующего сообщения
Вы можете просмотреть сообщение в начале очереди, не удаляя его из нее, вызвав метод **peek\_messages**. По умолчанию метод **peek\_messages** просматривает одно сообщение.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Практическое руководство. Удаление следующего сообщения из очереди
Ваш код удаляет сообщение из очереди в два этапа. При вызове метода **get\_messages** вы по умолчанию получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **get\_messages**, становится невидимым для другого кода, считывающего сообщения из этой очереди. По умолчанию это сообщение остается невидимым в течение 30 секунд. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **delete\_message**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message** сразу после обработки сообщения.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Способ извлечения сообщения из очереди можно настроить двумя способами.
Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода метод **get\_messages** используется для получения 16 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Практическое руководство. Изменение содержимого сообщения в очереди
Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Приведенный ниже код использует метод **update\_message** для обновления сообщения. Время ожидания видимости равно 0. Это означает, что сообщение появится немедленно, и содержимое обновится.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Практическое руководство. Получение длины очереди
Вы можете узнать приблизительное количество сообщений в очереди. Метод **get\_queue\_metadata** запрашивает у службы очереди **приблизительное количество сообщений** и метаданные очереди. В результате число указывается лишь приблизительно, так как сообщения могут добавляться или удаляться после ответа службы очередей на ваш запрос.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Практическое руководство. Удаление очереди
Чтобы удалить очередь и все сообщения в ней, вызовите метод **delete\_queue**.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Дальнейшие действия
Вы ознакомились с основными понятиями хранилища очередей. Дополнительные сведения см. по следующим ссылкам.

* [Центр по разработке для Python](/develop/python/)
* [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Блог рабочей группы службы хранилища Azure]
* [пакет SDK для службы хранилища Microsoft Azure для Python]

[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python