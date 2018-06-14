---
title: Как использовать хранилище очередей из Ruby | Документация Майкрософт
description: Вы узнаете, как использовать службы очередей Azure для создания и удаления очередей, вставки, получения и удаления сообщений. Примеры кода написаны на Ruby.
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 59c2d81b-db9c-46ee-ade2-2f0caae6b1e6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 0d7624d47a6924a5c8dec66b47ac0887ff493879
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2018
ms.locfileid: "27993642"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Использование хранилища очередей из Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища очередей Microsoft Azure. Примеры написаны с помощью Ruby Azure API.
Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Создание приложения Ruby
Создайте приложение Ruby. Инструкции см. в статье [Создание приложения Ruby в службе приложений на платформе Linux](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу
Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### <a name="use-rubygems-to-obtain-the-package"></a>Использование RubyGems для получения пакета
1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).
2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### <a name="import-the-package"></a>Импорт пакета
Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS_KEY**, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать сведения об учетной записи перед использованием объекта **Azure::QueueService** с помощью следующего кода:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Вот как можно получить эти значения из классический учетной записи хранения или учетной записи хранения Resource Manager на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. В колонке "Параметры" справа щелкните **Ключи доступа**.
4. В колонке "Ключи доступа" вы увидите ключи доступа 1 и 2. Можно использовать любой из них. 
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена. 

## <a name="how-to-create-a-queue"></a>Практическое руководство. Создание очереди
Следующий пример кода создает объект **Azure::QueueService** , который позволяет работать с очередями.

```ruby
azure_queue_service = Azure::QueueService.new
```

Используйте метод **create_queue()**, чтобы создать очередь с указанным именем.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Практическое руководство. Вставка сообщения в очередь
Чтобы вставить сообщение в очередь, используйте метод **create_message()** для создания нового сообщения и добавления его в очередь.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Практическое руководство. Просмотр следующего сообщения
Вы можете просмотреть сообщение в начале очереди, не удаляя его из нее, вызвав метод **peek\_messages()**. По умолчанию метод **peek\_messages()** просматривает одно сообщение. Вы также можете указать количество сообщений для просмотра.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Практическое руководство. Удаление следующего сообщения из очереди
Вы можете удалить сообщение из очереди в два этапа.

1. При вызове метода **list\_messages()** по умолчанию вы получаете следующее сообщение в очереди. Вы также можете указать количество сообщений для получения. Сообщения, возвращаемые методом **list\_messages ()**, становятся невидимыми для другого кода, читающего сообщения из этой очереди. Время ожидания видимости (в секундах) передается в качестве параметра.
2. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **delete_message()**.

Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message()** сразу после обработки сообщения.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Практическое руководство. Изменение содержимого сообщения в очереди
Вы можете изменить содержимое сообщения непосредственно в очереди. Приведенный ниже код использует метод **update_message()** для обновления сообщения. Этот метод возвращает последовательность, содержащую подтверждение получения сообщения очереди, дату и время в формате UTC, представляющие время, когда сообщение будет видимо в очереди.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Практическое руководство. Использование дополнительных параметров для удаления сообщений из очереди
Способ извлечения сообщения из очереди можно настроить двумя способами.

1. Можно получить пакет сообщения.
2. Можно задать более длительное или короткое время ожидания невидимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения.

В следующем примере кода метод **list\_messages()** используется для получения 15 сообщений в одном вызове. Затем код выводит и удаляет все сообщения. Он также задает время ожидания невидимости 5 минут для каждого сообщения.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Практическое руководство. Получение длины очереди
Вы можете получить приблизительное количество сообщений в очереди. Метод **get\_queue\_metadata()** запрашивает у службы очереди приблизительное количество сообщений и метаданные очереди.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Практическое руководство. Удаление очереди
Чтобы удалить очередь и все сообщения в ней, вызовите метод **delete\_queue()** для объекта очереди.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Дальнейшие действия
Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* Посетите [блог команды разработчиков хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Посетите репозиторий [Azure SDK для Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub

Сравнение службы очередей Azure, описанной в этой статье, и очередей служебной шины Azure, описанных в статье [Как использовать очереди служебной шины](/develop/ruby/how-to-guides/service-bus-queues/), см. в статье [Очереди Azure и очереди служебной шины: сходства и различия](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).