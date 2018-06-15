---
title: Как использовать очереди служебной шины в Node.js | Документация Майкрософт
description: Узнайте, как использовать очереди служебной шины в Azure в приложении Node.js.
services: service-bus-messaging
documentationcenter: nodejs
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 7bc291c3a453b4dec5e6c47ab4a7f2e2ac2b9967
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641447"
---
# <a name="how-to-use-service-bus-queues-with-nodejs"></a>Как использовать очереди служебной шины с Node.js

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

В этой статье показано, как использовать очереди служебной шины с Node.js. Примеры написаны на JavaScript и используют модуль Node.js для Azure. Здесь описаны такие сценарии, как **создание очередей**, **отправка и получение сообщений**, а также **удаление очередей**. Дополнительные сведения об очередях см. в разделе [Дальнейшие действия](#next-steps).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-nodejs-application"></a>Создание приложения Node.js
Создайте пустое приложение Node.js. Указания по созданию приложения Node.js можно найти в статьях [Создание веб-приложения Node.js в службе приложений Azure][Create and deploy a Node.js application to an Azure Website] или [Построение и развертывание приложения Node.js в облачной службе Azure][Node.js Cloud Service] с помощью Windows PowerShell.

## <a name="configure-your-application-to-use-service-bus"></a>Настройка приложения для использования служебной шины
Чтобы использовать служебную шину Azure, необходимо скачать и запустить пакет Azure для Node.js. Пакет содержит набор библиотек, взаимодействующих со службами REST Service Bus.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Использование диспетчера пакета Node (NPM) для получения пакета
1. Используя командное окно **Windows PowerShell для Node.js**, перейдите в папку **C:\\node\\sbqueues\\WebRole1**, в которой создан пример приложения.
2. В командном окне введите **npm install azure**, что должно привести к результату, аналогичному следующему:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Выполнив команду **ls** вручную, можно убедиться, что папка **node_modules** создана. В этой папке находится пакет **azure**, содержащий библиотеки, необходимые для доступа к очередям служебной шины.

### <a name="import-the-module"></a>Импорт модуля
С помощью Блокнота или другого текстового редактора добавьте в начало файла **server.js** приложения следующее:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Настройка подключения к служебной шине Azure
Модуль Azure считывает переменную среды `AZURE_SERVICEBUS_CONNECTION_STRING`, чтобы получить сведения, необходимые для подключения к служебной шине. Если эта переменная среды не задана, при вызове `createServiceBusService` необходимо указать сведения об учетной записи.

Пример настройки переменных среды на [портале Azure][Azure portal] для веб-сайта Azure см. в статье [Использование табличного хранилища Azure из Node.js][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Создание очереди
Объект **ServiceBusService** позволяет работать с очередями служебной шины. Следующий код создает объект **ServiceBusService**. Добавьте его в начало файла **server.js** после оператора импорта модуля Аzure.

```javascript
var serviceBusService = azure.createServiceBusService();
```

Вызов `createQueueIfNotExists` для объекта **ServiceBusService** возвращает указанную очередь (при ее наличии) или создает новую очередь с указанным именем. В следующем коде используется метод `createQueueIfNotExists`, чтобы создать очередь `myqueue` или подключиться к ней.

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Метод `createServiceBusService` также поддерживает дополнительные параметры, позволяющие переопределить настройки очереди по умолчанию, такие как срок жизни сообщения или максимальный размер очереди. В следующем примере показано, как установить максимальный размер очереди 5 ГБ и срок жизни 1 минуту.

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Фильтры
Используя **ServiceBusService**, к выполняемым операциям можно применить дополнительные операции фильтрации. К операциям фильтрации могут относиться ведение журнала, автоматический повтор и т. д. Фильтры являются объектами, реализующими метод со следующей сигнатурой:

```javascript
function handle (requestOptions, next)
```

Выполнив предварительную обработку параметров запроса, метод должен вызвать `next`, передавая обратный вызов со следующей сигнатурой:

```javascript
function (returnObject, finalCallback, next)
```

В этой функции обратного вызова и после обработки `returnObject` (ответ на запрос к серверу) функция обратного вызова должна либо вызвать `next` (при наличии), чтобы продолжить обработку других фильтров, либо в противном случае просто вызвать `finalCallback` для завершения обращения к службе.

Два фильтра (`ExponentialRetryPolicyFilter` и `LinearRetryPolicyFilter`), которые позволяют реализовать логику повторных попыток, входят в состав пакета Azure SDK для Node.js. Следующий код создает объект `ServiceBusService`, который использует метод `ExponentialRetryPolicyFilter`.

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Отправка сообщений в очередь
Чтобы отправить сообщение в очередь служебной шины, приложение вызывает метод `sendQueueMessage` для объекта **ServiceBusService**. Сообщения, отправляемые в очереди служебной шины и получаемые из них, — это объекты **BrokeredMessage**, которые имеют набор стандартных свойств (таких как **Label** и **TimeToLive**), словарь, используемый для хранения настраиваемых свойств приложения, и текст из произвольных данных приложения. Приложение может задать текст сообщения, передав строку как сообщение. Все необходимые стандартные свойства будут заполнены значениями по умолчанию.

В следующем примере показано, как отправить тестовое сообщение в очередь с именем `myqueue` с помощью метода `sendQueueMessage`:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Очереди служебной шины поддерживают максимальный размер сообщения 256 КБ для [уровня "Стандартный"](service-bus-premium-messaging.md) и 1 МБ для [уровня Premium](service-bus-premium-messaging.md). Максимальный размер заголовка, который содержит стандартные и настраиваемые свойства приложения, — 64 КБ. Ограничения на количество сообщений в очереди нет, но есть максимальный общий размер сообщений, содержащихся в очереди. Этот размер очереди, определяемый в момент ее создания, не должен превышать 5 ГБ. Дополнительные сведения о квотах см. в статье [Квоты на служебную шину][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Получение сообщений из очереди
Сообщения извлекаются из очереди с помощью метода `receiveQueueMessage` для объекта **ServiceBusService**. По умолчанию прочитанные сообщения удаляются из очереди. Но можно прочитать (просмотреть) сообщение и заблокировать его без удаления из очереди, задав для необязательного параметра `isPeekLock` значение **true**.

Поведение по умолчанию — чтение и удаление сообщения как часть операции получения — это простейшая модель, оптимальная для сценариев, в которых приложение может не обрабатывать сообщение в случае сбоя. Чтобы это понять, рассмотрим сценарий, в котором объект-получатель выдает запрос на получение и выходит из строя до его обработки. Поскольку служебная шина помечает сообщение как использованное, то когда после своего перезапуска приложение снова начнет обрабатывать сообщения, оно пропустит сообщение, использованное до сбоя.

Если параметр `isPeekLock` имеет значение **true**, получение становится операцией из двух этапов, что позволяет поддерживать приложения, неустойчивые к пропуску сообщений. Получив запрос, служебная шина находит следующее сообщение, блокирует его, чтобы предотвратить его получение другими получателями, и возвращает его приложению. Обработав сообщение (или сохранив его в надежном месте для последующей обработки), приложение вызывает метод `deleteMessage` и указывает сообщение, которое будет удалено как параметр, таким образом завершая второй этап процесса получения. Метод `deleteMessage` помечает сообщение как использованное и удаляет его из очереди.

В следующем примере показано, как получать и обрабатывать сообщения с помощью метода `receiveQueueMessage`. Сначала приложение получает и удаляет сообщение в примере, затем получает его с помощью параметра `isPeekLock`, для которого задано значение **true**, и удаляет с помощью метода `deleteMessage`.

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Как обрабатывать сбои приложения и нечитаемые сообщения
служебная шина предоставляет функции, помогающие корректно выполнить восстановление после ошибок в приложении или трудностей, возникших при обработке сообщения. Если по какой-либо причине приложению-получателю не удается обработать сообщение, оно вызывает метод `unlockMessage` для объекта **ServiceBusService**. После этого служебная шина разблокирует сообщение в очереди и сделает его доступным для приема тем же приложением или другим приложением.

Кроме того, с сообщением, заблокированным в очереди, связано время ожидания. Если приложение не сможет обработать сообщение в течение времени ожидания (например, при сбое приложения), Service Bus разблокирует сообщение автоматически и сделает его доступным для приема.

Если в приложении происходит сбой после обработки сообщения, но перед вызовом метода `deleteMessage`, сообщение будет повторно доставлено в приложение после его перезапуска. Часто этот подход называют *обработать хотя бы один раз*, т. е. каждое сообщение будет обрабатываться по крайней мере один раз, но в некоторых случаях это же сообщение может быть доставлено повторно. Если повторная обработка недопустима, разработчики приложения должны добавить дополнительную логику для обработки повторной доставки сообщений. Часто это достигается с помощью свойства **MessageId** сообщения, которое остается постоянным для различных попыток доставки.

## <a name="next-steps"></a>Дополнительная информация
Дополнительную информацию об очередях см. в следующих ресурсах.

* [Очереди, разделы и подписки служебной шины][Queues, topics, and subscriptions]
* Репозиторий [пакетов Azure SDK для Node][Azure SDK for Node] на сайте GitHub
* [Центр разработчика Node.js](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
