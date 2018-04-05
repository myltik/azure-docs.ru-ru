---
title: Отправка событий в концентраторы событий Azure с помощью Java | Документация Майкрософт
description: Начало работы с отправкой в концентраторы событий с помощью Java.
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Отправка событий в концентраторы событий Azure с помощью Java

Концентраторы событий — это высокомасштабируемая система, способная принимать миллионы событий в секунду, благодаря которой приложения могут обрабатывать и анализировать большие объемы данных от подключенных устройств и приложений. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в [обзоре концентраторов событий][Event Hubs overview].

В этом руководстве показано, как отправлять события в концентратор событий с помощью консольного приложения на языке Java. Дополнительные сведения о получении событий с помощью библиотеки узла обработчика событий для Java см. в [этой статье](event-hubs-java-get-started-receive-eph.md), или выберите соответствующий язык в оглавлении статьи слева.

Чтобы выполнить задания из этого учебника, вам нужно следующее:

* Среда разработки Java. Для этого руководства предполагается использование среды [Eclipse](https://www.eclipse.org/).
* Активная учетная запись Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись Azure][], прежде чем начинать работу.

Код в этом руководстве основан на [примере Send в GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send). Изучите его, чтобы получить полное представление о рабочем приложении.

## <a name="send-events-to-event-hubs"></a>Отправка событий в службу "Концентраторы событий"

Клиентскую библиотеку Java для концентраторов событий можно использовать в проектах Maven из [центрального репозитория Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Чтобы сослаться на эту библиотеку, используйте приведенное ниже объявление зависимостей в файле проекта Maven. Текущая версия — 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Для различных типов сред сборки можно явно получить последние выпущенные JAR-файлы из [центрального репозитория Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Если используется простой издатель событий, импортируйте пакет *com.microsoft.azure.eventhubs* для клиентских классов концентраторов событий и пакет *com.microsoft.azure.servicebus* для служебных классов, таких как общие исключения, которые используются совместно с клиентом обмена сообщениями служебной шины Azure. 

### <a name="declare-the-send-class"></a>Объявление класса Send

Следующий пример сначала создает новый проект Maven для приложения консоли или оболочки в избранной среде разработки Java. Назовите класс `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Создание строки подключения

Чтобы создать значение строки подключения, используйте класс ConnectionStringBuilder. Затем передайте это значение в экземпляр клиента службы "Концентраторы событий". Замените заполнители значениями, которые получены при создании пространства имен и концентратора событий:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Отправка событий

Затем создайте одиночное событие, преобразовав строку в байтовую кодировку UTF-8. Затем создайте новый экземпляр клиента концентраторов событий из строки подключения и отправьте сообщение.   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Receive events using the EventProcessorHost](event-hubs-java-get-started-receive-eph.md) (Получение событий с помощью EventProcessorHost)
* [Обзор концентраторов событий Azure][Event Hubs overview].
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[бесплатную учетную запись Azure]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

