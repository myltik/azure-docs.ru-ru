---
title: "Количество сообщений служебной шины Azure | Документация Майкрософт"
description: "Получение числа сообщений служебной шины Azure."
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: f5d2aa551bbe77a66459907cf5cd1313bb907981
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="message-counters"></a>Счетчики сообщений

Можно получить количество сообщений в очереди и подписке с помощью интерфейсов API [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) Azure Resource Manager и служебной шины, доступных в пакете SDK для .NET Framework.

С помощью PowerShell это количество можно получить следующим образом.

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Сведения о количестве сообщений

Знание числа активных сообщений помогает определить, создает ли очередь невыполненную работу, которая требует больше ресурсов для обработки, чем развернуто в настоящее время. В классе [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) доступны следующие данные счетчиков.

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): число сообщений в очереди или подписке, которые находятся в активном состоянии и готовы к доставке.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): число сообщений в очереди недоставленных сообщений.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): число запланированных сообщений.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): число сообщений, которые не удалось перенести в другую очередь или раздел и которые были перемещены в очередь недоставленных сообщений для передачи.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): число сообщений, ожидающих передачи в другую очередь или раздел.

Если приложению требуется масштабировать ресурсы в зависимости от длины очереди, оно должно делать это очень размеренно. Получение данных счетчиков сообщений является ресурсоемкой операцией в брокере обмена сообщениями, и частое ее выполнение напрямую негативно влияет на производительность сущности.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об обмене сообщениями через служебную шину см. в следующих статьях:

* [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
* [Очереди, разделы и подписки служебной шины](service-bus-queues-topics-subscriptions.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Как использовать разделы и подписки служебной шины](service-bus-dotnet-how-to-use-topics-subscriptions.md)