---
title: "Исключения ретранслятора Microsoft Azure и способы их устранения | Документация Майкрософт"
description: "Список исключений ретранслятора Azure и предлагаемые действия."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: bc71995deef3f9795ebd33d9642c7ffa1995021b
ms.openlocfilehash: 346851226dce8ca8e48d3ae291b97e6e43d1ae03
ms.lasthandoff: 02/02/2017


---
# <a name="relay-exceptions"></a>Исключения ретранслятора

В этой статье перечислены некоторые исключения, создаваемые интерфейсами API ретранслятора Microsoft Azure. Этот справочник может измениться, поэтому следите за обновлениями.

## <a name="exception-categories"></a>Категории исключений

Интерфейсы API ретранслятора создают исключения, которые можно разделить на следующие категории с указанием связанного действия, предпринимаемого для их устранения.

1. Ошибка в коде пользователя ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Общее действие: прежде чем продолжить, попытайтесь исправить код.
2. Ошибка настройки или конфигурации ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx)). Общее действие: проверьте конфигурацию и измените ее при необходимости.
3. Временные исключения ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Общее действие: повторите операцию или уведомите пользователей.
4. Другие исключения ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)). Общее действие: связано с типом исключения; см. таблицу в следующем разделе. 

## <a name="exception-types"></a>Типы исключений

В следующей таблице перечислены типы исключений обмена сообщениями и их причины, а также рекомендуемые действия, которые можно выполнять.

| **Тип исключения** | **Описание** | **Рекомендуемое действие** | **Примечание к автоматическому или немедленному повтору** |
| --- | --- | --- | --- |
| [Время ожидания](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Сервер не ответил на запрошенную операцию в течение указанного времени, контролируемого параметром [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за сетевых или других задержек в инфраструктуре. |Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку. Ознакомьтесь с разделом [Исключения времени ожидания](#timeoutexception). |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [Недопустимая операция](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Запрошенная пользователем операция не допускается в пределах сервера или службы. Подробные сведения см. в сообщении об исключении. Например, операция [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) создаст это исключение, если сообщение получено в режиме **ReceiveAndDelete**. |Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. |Повторная попытка не поможет. |
| [Операция отменена](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. |Проверьте код и убедитесь, что он не вызывает операции с удаленным объектом. |Повторная попытка не поможет. |
| [Несанкционированный доступ](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Объекту [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) не удалось получить маркер. Маркер является недопустимым или не содержит утверждений, необходимых для выполнения операции. |Убедитесь, что поставщик маркеров создан с использованием правильных значений. Проверьте конфигурацию службы контроля доступа. |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [Исключение аргумента](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Аргумент null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Аргумент вне допустимого диапазона](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Для этого метода предоставлен один или несколько недопустимых аргументов.<br /> Универсальный код ресурса (URI), переданный в [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create), содержит сегменты пути.<br /> В [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) передана недопустимая схема универсального кода ресурса (URI). <br />Значение свойства превышает 32 КБ. |Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. |Повторная попытка не поможет. |
| [Сервер занят](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |В настоящее время служба не может обработать запрос. |Клиент может некоторое время подождать, после чего следует повторить операцию. |Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [Превышена квота](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Сущность обмена сообщениями достигла максимально допустимого размера. |Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. Ознакомьтесь с разделом [Исключение QuotaExceededException](#quotaexceededexception). |Повторная попытка может помочь, если сообщения были удалены. |
| [Превышен размер сообщения](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Полезные данные сообщения превышают предел в 256 КБ. Обратите внимание, что предел в 256 КБ является общим размером сообщения, в который могут входить системные свойства и нагрузка .NET. |Сократите размер полезных данных сообщения, а затем повторите операцию. |Повторная попытка не поможет. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) указывает на превышение квоты для конкретного объекта.

Для ретранслятора это исключение служит оболочкой для исключения [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), указывающего, что для этой конечной точки превышено максимальное число прослушивателей. Это указывается в значении поля **MaximumListenersPerEndpoint** сообщения об исключении.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции. 

Следует проверить значение свойства [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), так как достижение этого ограничения также может вызвать исключение [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Для ретранслятора исключения времени ожидания могут отображаться при первом открытии подключения отправителя ретрансляции. Существует две основные причины этого исключения:

1. слишком маленькое значение [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) (вплоть до доли секунды);
2. неотвечающие локальные прослушиватели ретрансляции (или проблемы, связанные с тем, что правила брандмауэра запрещают прослушивателям принимать новые клиентские подключения) и значение [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) меньше 20 секунд.

Например:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Основные причины
Существует две основные причины этой ошибки: неправильная конфигурация или временная ошибка службы.

1. **Неправильная конфигурация**
   . Время ожидания операции может быть слишком коротким для состояния операции. Время ожидания операции в пакете SDK клиента по умолчанию составляет 60 секунд. Проверьте, не задано ли в вашем коде слишком маленькое значение. Учтите, что состояние сети и загрузка ЦП могут повлиять на время, необходимое для выполнения конкретной операции, поэтому не следует задавать слишком маленькое значение времени ожидания операции.
2. **Временная ошибка службы**
   . Иногда в ретрансляторе могут происходить задержки при обработке запросов, например в периоды интенсивной загрузки сети. В таких случаях можно настроить повторную попытку выполнения операции через некоторое время до ее успешного завершения. Если же операцию по-прежнему не удается выполнить после нескольких попыток, посетите [сайт состояния служб Azure](https://azure.microsoft.com/status/), чтобы получить сведения об известных простоях служб.

## <a name="next-steps"></a>Дальнейшие действия:
* [Вопросы и ответы по ретранслятору](relay-faq.md)
* [Создание пространства имен](relay-create-namespace-portal.md)
* [Приступая к работе с .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Приступая к работе с Node](relay-hybrid-connections-node-get-started.md)


