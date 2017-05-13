---
title: "Отправка событий в концентраторы событий Azure с помощью Java | Документация Майкрософт"
description: "Начало работы с отправкой в концентраторы событий с помощью Java."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: jotaub;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 45776b0920f65ae9749b00978656bcefa2bf01a8
ms.contentlocale: ru-ru
ms.lasthandoff: 05/04/2017


---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Отправка событий в концентраторы событий Azure с помощью Java

## <a name="introduction"></a>Введение
Концентраторы событий — это высокомасштабируемая система приема, которая может принимать миллионы событий в секунду, позволяя приложениям обрабатывать и анализировать большие объемы данных, сформированных подключенными устройствами и приложениями. После сбора данных в концентраторах событий их можно преобразовать и сохранить с помощью любого поставщика аналитики в реальном времени или в кластере хранилища.

Дополнительные сведения см. в [обзоре концентраторов событий][Event Hubs overview].

В этом руководстве показано, как отправлять события в концентратор событий с помощью консольного приложения на языке Java. Дополнительные сведения о получении событий с помощью библиотеки узла обработчика событий для Java см. в [этой статье](event-hubs-java-get-started-receive-eph.md), или выберите соответствующий язык в оглавлении статьи слева.

Чтобы выполнить задания из этого учебника, вам нужно следующее:

* Среда разработки Java. Для этого учебника предполагается использование среды [Eclipse](https://www.eclipse.org/).
* Активная учетная запись Azure. <br/>Если ее нет, можно создать бесплатную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Бесплатная пробная версия Azure</a>.

## <a name="send-messages-to-event-hubs"></a>Отправка сообщений в центры событий
Клиентская библиотека Java для концентраторов событий доступна для использования в проектах из [центрального репозитория Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Ссылаться на нее можно, используя следующее объявление зависимости в файле проекта Maven:    

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Для различных типов сред сборки можно явно получить последние выпущенные JAR-файлы из [центрального репозитория Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) или из [точки распространения выпусков на GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Если используется простой издатель событий, импортируйте пакет *com.microsoft.azure.eventhubs* для клиентских классов концентраторов событий и пакет *com.microsoft.azure.servicebus* для служебных классов, таких как общие исключения, которые используются совместно с клиентом обмена сообщениями служебной шины Azure. 

Следующий пример сначала создает новый проект Maven для приложения консоли или оболочки в избранной среде разработки Java. Класс будет называться `Send`.     

```Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Замените имя пространства имен и концентратора событий значениями, использованными при создании концентратора событий.

```Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Затем создайте одиночное событие, преобразовав строку в байтовую кодировку UTF-8. После этого мы создаем новый экземпляр клиента концентраторов событий из строки подключения и отправляем сообщение.   

```Java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Receive events using the EventProcessorHost](event-hubs-java-get-started-receive-eph.md) (Получение событий с помощью EventProcessorHost)
* [Обзор концентраторов событий Azure][Event Hubs overview].
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
