---
title: "Как использовать хранилище очередей из Node.js | Документация Майкрософт"
description: "Вы узнаете, как использовать службы очередей Azure для создания и удаления очередей, вставки, получения и удаления сообщений. Примеры кода написаны на Node.js."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a8a92db0-4333-43dd-a116-28b3147ea401
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: ce61687e0f64b5971daca85265ef0e7d2b6f43b8
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/20/2017
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Использование хранилища очередей из Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы очередей Microsoft Azure. Примеры написаны с использованием интерфейса API Node.js. Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Создание приложения Node.js
Создайте пустое приложение Node.js. Инструкции по созданию приложения Node.js см. в статьях [Создание веб-приложения Node.js в службе приложений Azure](../../app-service/app-service-web-get-started-nodejs.md), [Построение и развертывание приложения Node.js в облачной службе Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) -- (с использованием Windows PowerShell, или [Создание и развертывание веб-приложения Node.js в Azure с использованием WebMatrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу
Для использования службы хранилища Azure необходим пакет SDK для службы хранилища Azure для Node.js, который содержит набор удобных библиотек, взаимодействующих со службами REST хранилища.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета
1. Используя интерфейс командной строки, такой как **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix), перейдите в папку, в которой создан пример приложения.
2. Введите в командной строке **npm install azure-storage** . Результат выполнения этой команды будет аналогичен следующему примеру:
 
    ```
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Выполнив команду **ls** вручную, можно убедиться, что папка **node\_modules** создана. В этой папке находится пакет **azure-storage** , содержащий библиотеки, необходимые для доступа к хранилищу.

### <a name="import-the-package"></a>Импорт пакета
Используйте Блокнот или другой текстовый редактор, чтобы добавить следующий код в начало файла **server.js** приложения, где планируется использовать хранилище.

```
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Модуль Azure считывает переменные среды AZURE\_STORAGE\_ACCOUNT и AZURE\_STORAGE\_ACCESS\_KEY или AZURE\_STORAGE\_CONNECTION\_STRING, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, при вызове **createQueueService**необходимо указать сведения об учетной записи.

Пример настройки переменных среды для веб-сайта Azure на [портале Azure](https://portal.azure.com) см. в статье [Веб-приложение Node.js, использующее службу таблиц Azure].

## <a name="how-to-create-a-queue"></a>Практическое руководство. Создание очереди
Следующий пример кода создает объект **QueueService** , который позволяет работать с очередями.

```
var queueSvc = azure.createQueueService();
```

Используйте метод **createQueueIfNotExists** , который возвращает указанную очередь, если она уже существует, или создает новую с указанным именем, если она отсутствует.

```
queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
  if(!error){
    // Queue created or exists
  }
});
```

Если очередь создана, значение `result.created` будет истинно. Если очередь уже существует, значение `result.created` будет ложно.

### <a name="filters"></a>Фильтры
Дополнительные операции фильтрации можно применить к выполняемым операциям, используя **QueueService**. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать "next", передавая обратный вызов со следующей сигнатурой:

```
function (returnObject, finalCallback, next)
```

В этой функции обратного вызова и после обработки returnObject (ответ на запрос к серверу) функция обратного вызова должна либо вызвать "next", если он существует, чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать "finalCallback" для завершения обращения к службе.

В пакет SDK Azure для Node.js включены два фильтра, реализующие логику повторных попыток: **ExponentialRetryPolicyFilter** и **LinearRetryPolicyFilter**. Следующий код создает объект **QueueService**, использующий **ExponentialRetryPolicyFilter**:

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Практическое руководство. Вставка сообщения в очередь
Чтобы создать сообщение и вставить его в очередь, используйте метод **createMessage** .

```
queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Практическое руководство. Просмотр следующего сообщения
Вы можете просмотреть сообщение в начале очереди, не удаляя его из нее, вызвав метод **peekMessages** . По умолчанию **peekMessages** просматривает одно сообщение.

```
queueSvc.peekMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
  }
});
```

Его содержит объект `result` .

> [!NOTE]
> Если вызвать метод **peekMessages** , когда в очереди нет сообщений, оповещение об ошибке не появляется, но сообщения не возвращаются.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Практическое руководство. Удаление следующего сообщения из очереди
Обработка сообщения выполняется двухэтапным процессом:

1. удаление сообщения из очереди.
2. удаление сообщения.

Чтобы удалить сообщение из очереди, используйте метод **getMessage**. В результате сообщения становятся невидимыми в очереди, и другие клиенты не могут их обрабатывать. После того как приложение обработало сообщение, вызовите метод **deleteMessage** , чтобы удалить его из очереди. Следующий пример получает сообщение, а затем удаляет его:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Message text is in messages[0].messageText
    var message = result[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> По умолчанию сообщение остается скрытым в течение 30 секунд, а потом снова становится видимым для других клиентов. Чтобы задать другое значение, укажите с методом **getMessages** параметр `options.visibilityTimeout`.
> 
> [!NOTE]
> Если использовать метод **getMessages** , когда в очереди нет сообщений, оповещение об ошибке не появляется, но сообщения не возвращаются.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Практическое руководство. Изменение содержимого сообщения в очереди
Вы можете изменить содержимое сообщения непосредственно в очереди с помощью **updateMessage**. Следующий пример обновляет текст сообщения:

```
queueSvc.getMessages('myqueue', function(error, result, response){
  if(!error){
    // Got the message
    var message = result[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, result, response){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Практическое руководство. Использование дополнительных параметров для удаления сообщений из очереди
Существует два способа настройки извлечения сообщения из очереди:

* `options.numOfMessages` — извлекает пакет сообщений (до 32).
* `options.visibilityTimeout` — задает более длительное или короткое время ожидания невидимости.

В следующем примере кода метод **getMessages** используется для получения 15 сообщений в одном вызове. Затем он обрабатывает каждое сообщение с помощью цикла for. Он также задает время ожидания невидимости пять минут для всех сообщений, возвращенных методом.

```
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
  if(!error){
    // Messages retreived
    for(var index in result){
      // text is available in result[index].messageText
      var message = result[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, response){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Практическое руководство. Получение длины очереди
Метод **getQueueMetadata** возвращает метаданные очереди, в том числе приблизительное количество сообщений, ожидающих в очереди.

```
queueSvc.getQueueMetadata('myqueue', function(error, result, response){
  if(!error){
    // Queue length is available in result.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Практическое руководство. Получение списка очередей
Чтобы извлечь список очередей, используйте **listQueuesSegmented**. Чтобы извлечь список, отфильтрованный по определенному префиксу, используйте **listQueuesSegmentedWithPrefix**.

```
queueSvc.listQueuesSegmented(null, function(error, result, response){
  if(!error){
    // result.entries contains the list of queues
  }
});
```

Если не удается вернуть все очереди, атрибут `result.continuationToken` можно использовать в качестве первого параметра метода **listQueuesSegmented** или второго параметра метода **listQueuesSegmentedWithPrefix** для получения дополнительных результатов.

## <a name="how-to-delete-a-queue"></a>Практическое руководство. Удаление очереди
Чтобы удалить очередь и все сообщения в ней, вызовите метод **deleteQueue** для объекта очереди.

```
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Чтобы удалить все сообщения очереди без удаления самой очереди, используйте метод **clearMessages**.

## <a name="how-to-work-with-shared-access-signatures"></a>Практическое руководство. Работа с подписанными URL-адресами
Подписи общего доступа (SAS) — безопасный способ предоставить детальный доступ к очередям без указания имени или ключей своей учетной записи хранения. SAS часто используется для предоставления ограниченного доступа к очередям, например, позволяет мобильному приложению отправлять сообщения.

Надежное приложение, например, облачная служба, создает подпись SAS с помощью **generateSharedAccessSignature** службы **QueueService** и предоставляет ее ненадежному или полунадежному приложению. Например, мобильному приложению. Подпись SAS создается с использованием политики, которая описывает даты начала и окончания срока действия SAS, а также уровень доступа, который предоставляется держателю подписи SAS.

В следующем примере создается новая общая политика, которая позволяет держателю подписи SAS добавлять сообщения в очередь в течение 100 минут с момента своего создания.

```
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Обратите внимание, что также должна быть предоставлена информация узла, поскольку она требуется держателю SAS для совершения попыток доступа к очереди.

Клиентское приложение далее использует подпись SAS с помощью **QueueServiceWithSAS** для выполнения операций с очередью. Следующий пример выполняет подключение к очереди и создает сообщение.

```
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Поскольку подпись SAS была создана только для доступа добавления, если выполняется попытка чтения, обновления или удаления сообщений, будет возвращена ошибка.

### <a name="access-control-lists"></a>Доступ к спискам управления
Можно также использовать список управления доступом (ACL) для задания политики доступа подписи SAS. Это может оказаться полезным, когда необходимо предоставить доступ к очереди нескольким клиентам, но с различной политикой доступа для каждого из них.

ACL реализуется с помощью массива политик доступа, каждая из которых связана со своим идентификатором. В следующем примере определяются две политики, по одной для пользователей user1 и user2:

```
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

В этом примере код получает текущее значение ACL для **myqueue**, а затем добавляет новые политики с помощью **setQueueAcl**. Такой подход допускает выполнение:

```
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

После задания ACL можно создать подпись SAS на основе идентификатора политики. В следующем примере создается новая подпись SAS для пользователя 'user2':

```
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Дальнейшие действия
Вы изучили основные сведения о хранилище очередей. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

* Посетите [блог команды разработчиков для службы хранилища Azure][Azure Storage Team Blog].
* Посетите репозиторий [пакета SDK службы хранилища Azure для Node][Azure Storage SDK for Node] на веб-сайте GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Создание веб-приложения Node.js в службе приложений Azure](../../app-service/app-service-web-get-started-nodejs.md)

[Построение и развертывание приложения Node.js в облачной службе Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
