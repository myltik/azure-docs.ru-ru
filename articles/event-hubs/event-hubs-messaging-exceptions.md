---
title: Исключения обмена сообщениями концентраторов событий Azure | Документация Майкрософт
description: Список исключений обмена сообщениями концентраторов событий и предлагаемые действия.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 964475ba8b42ac41707fa78468bfe551677c595f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783134"
---
# <a name="event-hubs-messaging-exceptions"></a>Исключения обмена сообщениями концентраторов событий

В этой статье перечислены некоторые исключения, создаваемые библиотекой API обмена сообщениями служебной шины Azure, в том числе API концентраторов событий. Этот справочник может измениться, поэтому следите за обновлениями.

## <a name="exception-categories"></a>Категории исключений

API концентраторов событий создают исключения, которые можно разделить на следующие категории с указанием связанного действия, предпринимаемого для их устранения.

1. Ошибка в коде пользователя: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Общее действие: прежде чем продолжить, попытайтесь исправить код.
2. Ошибка настройки или конфигурации: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Общее действие: проверьте конфигурацию и измените ее при необходимости.
3. Временные исключения: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Общее действие: повторите операцию или уведомите пользователей.
4. Другие исключения: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Общее действие: связано с типом исключения; см. таблицу в следующем разделе. 

## <a name="exception-types"></a>Типы исключений
В следующей таблице перечислены типы исключений обмена сообщениями и их причины, а также рекомендуемые действия, которые можно выполнять.

| **Тип исключения** | **Описание, причина, примеры** | **Рекомендуемое действие** | **Примечание к автоматическому или немедленному повтору** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Сервер не ответил на запрошенную операцию в течение указанного времени, которое задается параметром [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за сетевых или других задержек в инфраструктуре. |Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку.<br /> Ознакомьтесь с разделом [TimeoutException](#timeoutexception). |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Запрошенная пользователем операция не допускается в пределах сервера или службы. Подробные сведения см. в сообщении об исключении. Например, операция [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) создаст это исключение, если сообщение было получено в режиме [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode). |Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. |Повторная попытка не поможет. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. |Проверьте код и убедитесь, что он не вызывает операции с удаленным объектом. |Повторная попытка не поможет. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Объекту [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) не удалось получить маркер. Маркер является недопустимым или не содержит утверждений, необходимых для выполнения операции. |Убедитесь, что поставщик маркеров создан с использованием правильных значений. Проверьте конфигурацию службы контроля доступа. |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Для этого метода предоставлен один или несколько недопустимых аргументов. Универсальный код ресурса (URI), переданный в [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__), содержит сегменты пути. В [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) передана недопустимая схема универсального кода ресурса (URI). Значение свойства превышает 32 КБ. |Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. |Повторная попытка не поможет. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |Сущность, связанная с операцией, не существует или была удалена. |Убедитесь, что сущность существует. |Повторная попытка не поможет. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Клиент не может установить соединение с концентратором событий. |Убедитесь, что имя узла указано правильно и узел доступен. |Повторная попытка может помочь при наличии периодических сбоев подключения. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |В настоящее время служба не может обработать запрос. |Клиент может некоторое время подождать, после чего следует повторить операцию. <br /> Ознакомьтесь с разделом [ServerBusyException](#serverbusyexception). |Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Универсальное исключение обмена сообщениями, которое может быть вызвано в следующих случаях: предпринята попытка создать [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) с использованием имени или пути, которые относятся к другому типу сущности (например, разделу). Предпринята попытка отправить сообщение размером свыше 256 КБ. Во время обработки запроса возникла ошибка сервера или службы. Подробные сведения см. в сообщении об исключении. Как правило, это временное исключение. |Проверьте код и убедитесь, что для текста сообщения применяются только сериализуемые объекты (или используйте настраиваемый сериализатор). Обратитесь к документации для получения сведений о поддерживаемых типах значений свойств и используйте только поддерживаемые типы. Проверьте свойство [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) . Если оно имеет значение **true**, можно повторить операцию. |Алгоритм поведения не определен и может не помочь. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Предпринята попытка создать сущность с именем, которое уже используется другой сущностью в этом пространстве имен службы. |Удалите существующую сущность или выберите другое имя для сущности, которую нужно создать. |Повторная попытка не поможет. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Сущность обмена сообщениями достигла максимально допустимого размера. Это может произойти, если уже было открыто максимальное число получателей (равное 5) на группу потребителей. |Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. <br /> Ознакомьтесь с разделом [QuotaExceededException](#quotaexceededexception). |Повторная попытка может помочь, если сообщения были удалены. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Выполнен запрос на операцию среды выполнения с отключенной сущностью. |Активируйте сущность. |Повторная попытка может помочь, если сущность была активирована. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Полезные данные сообщения превышают предел в 256 КБ. Обратите внимание, что предел в 256 КБ является общим размером сообщения, в который могут входить системные свойства и нагрузка .NET. |Сократите размер полезных данных сообщения, а затем повторите операцию. |Повторная попытка не поможет. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) указывает на превышение квоты для конкретного объекта.

Это может произойти, если уже было открыто максимальное число получателей (5) на группу потребителей.

### <a name="event-hubs"></a>Концентраторы событий
Концентраторы событий имеют ограничение в 20 групп потребителей на один концентратор событий. При попытке создать больше групп появляется исключение [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции. 

Для концентраторов событий время ожидания указывается как часть строки подключения или с помощью [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Само сообщение об ошибке может отличаться, но оно всегда содержит значение времени ожидания, указанное для текущей операции. 

### <a name="common-causes"></a>Основные причины
Существует две основные причины этой ошибки: неправильная конфигурация или временная ошибка службы.

1. **Неправильная конфигурация.** Время ожидания операции может быть слишком коротким для состояния операции. Время ожидания операции в пакете SDK клиента по умолчанию составляет 60 секунд. Проверьте, не задано ли в вашем коде слишком маленькое значение. Учтите, что состояние сети и загрузка ЦП могут повлиять на время, необходимое для выполнения конкретной операции, поэтому не следует задавать слишком маленькое значение времени ожидания операции.
2. **Временная ошибка службы.** Иногда обработка запросов в службе концентраторов событий выполняется с задержками, например в периоды интенсивной загрузки сети. В таких случаях можно настроить повторную попытку выполнения операции через некоторое время до ее успешного завершения. Если же операцию по-прежнему не удается выполнить после нескольких попыток, посетите [сайт состояния служб Azure](https://azure.microsoft.com/status/), чтобы получить сведения об известных простоях служб.

## <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) или [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) указывают на то, что сервер перегружен. Для этого исключения есть два соответствующих кода ошибки.

### <a name="error-code-50002"></a>Код ошибки 50002

Эта ошибка может возникать по одной из двух причин:

1. Нагрузка неравномерно распределяется между всеми секциями в концентраторе событий, и в одной секции превышается локальное ограничение пропускной способности.
    
    Способ устранения: пересмотреть стратегию распределения нагрузки по секциям или попробовать воспользоваться [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_).

2. Пространство имен концентраторов событий не имеет достаточного числа единиц пропускной способности (это можно проверить на [портале Azure](https://portal.azure.com) на экране **Метрики** в окне пространства имен концентраторов событий). Обратите внимание, что на портале отображаются обобщенные (за 1 минуту) сведения, но мы измеряем пропускную способность в режиме реального времени, то есть это только оценка.

    Способ устранения: увеличить число единиц пропускной способности для пространства имен. Это можно сделать на портале в окне **Масштаб** экрана пространства имен концентраторов событий.

### <a name="error-code-50001"></a>Код ошибки 50001

Эта ошибка не должна возникать часто. Она происходит, когда контейнеру, выполняющему код для пространства имен, не хватает ресурсов ЦП — всего за пару секунд до запуска подсистемы балансировки нагрузки концентраторов событий.


## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [Event Hubs overview](event-hubs-what-is-event-hubs.md)
* [Создание концентратора событий](event-hubs-create.md)
* [Часто задаваемые вопросы о концентраторах событий](event-hubs-faq.md)
