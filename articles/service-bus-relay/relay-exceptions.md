---
title: Исключения ретранслятора Azure и способы их разрешения | Документация Майкрософт
description: Список исключений ретранслятора Azure и предлагаемые действия по их разрешению.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: 1dbe73dac0d09db96ab902909125869959963e6f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
ms.locfileid: "26855867"
---
# <a name="azure-relay-exceptions"></a>Исключения ретранслятора Azure

В этой статье перечислены некоторые исключения, создаваемые интерфейсами API ретранслятора Azure. Этот справочник может измениться, поэтому следите за обновлениями.

## <a name="exception-categories"></a>Категории исключений

Интерфейсы API ретранслятора создают исключения, которые можно разделить на указанные ниже категории. Кроме того, здесь указаны предлагаемые действия, которые можно предпринять для их разрешения.

*   **Ошибка в коде пользователя.** [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Общее действие.** Прежде чем продолжить, попытайтесь исправить код.
*   **Ошибка настройки или конфигурации.** [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Общее действие.** Проверьте конфигурацию и измените ее при необходимости.
*   **Временные исключения.** [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Общее действие.** Повторите операцию или уведомите пользователей.
*   **Другие исключения.** [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Общее действие.** Связано с типом исключения. Ознакомьтесь с таблицей в следующем разделе. 

## <a name="exception-types"></a>Типы исключений

В следующей таблице перечислены типы исключений обмена сообщениями и их причины, а также рекомендуемые действия, которые можно выполнять для их разрешения.

| **Тип исключения** | **Описание** | **Рекомендуемое действие** | **Примечание к автоматическому или немедленному повтору** |
| --- | --- | --- | --- |
| [Время ожидания](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Сервер не ответил на запрошенную операцию в течение указанного времени, которое задается параметром [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за сетевых или других задержек в инфраструктуре. |Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку. Ознакомьтесь с разделом [TimeoutException](#timeoutexception). |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [Недопустимая операция](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Запрошенная пользователем операция не допускается в пределах сервера или службы. Подробные сведения см. в сообщении об исключении. |Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. |Повторная попытка не поможет. |
| [Операция отменена](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. |Проверьте код и убедитесь, что он не вызывает операции с удаленным объектом. |Повторная попытка не поможет. |
| [Несанкционированный доступ](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Объекту [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) не удалось получить маркер. Маркер является недопустимым или не содержит утверждений, необходимых для выполнения операции. |Убедитесь, что поставщик токенов создан с использованием правильных значений. Проверьте конфигурацию службы контроля доступа. |Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [Исключение аргумента](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Аргумент Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Аргумент вне допустимого диапазона](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Далее приведены причины возникновения исключений:<br />Для этого метода предоставлен один или несколько недопустимых аргументов.<br /> Универсальный код ресурса (URI), переданный в [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create), содержит один или несколько сегментов пути.<br />В [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) или [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) передана недопустимая схема универсального кода ресурса (URI). <br />Значение свойства превышает 32 КБ. |Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. |Повторная попытка не поможет. |
| [Сервер занят](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |В настоящее время служба не может обработать запрос. |Клиент может некоторое время подождать, после чего следует повторить операцию. |Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [Превышена квота](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Сущность обмена сообщениями достигла максимально допустимого размера. |Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. Ознакомьтесь с разделом [Исключение QuotaExceededException](#quotaexceededexception). |Повторная попытка может помочь, если сообщения были удалены. |
| [Превышен размер сообщения](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Полезные данные сообщения превышают предел в 256 КБ. Обратите внимание, что предел в 256 КБ является общим размером сообщения с учетом системных свойств и служебных данных Microsoft .NET. |Сократите размер полезных данных сообщения, а затем повторите операцию. |Повторная попытка не поможет. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) указывает на превышение квоты для конкретного объекта.

Для ретранслятора это исключение служит оболочкой для исключения [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), указывающего, что для этой конечной точки превышено максимальное число прослушивателей. Это указывается в значении поля **MaximumListenersPerEndpoint** сообщения об исключении.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции. 

Проверьте значение свойства [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit). Достижение этого ограничения также может породить исключение [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Для ретранслятора исключения времени ожидания могут отображаться при первом открытии подключения отправителя ретрансляции. Существует две основные причины этого исключения:

*   слишком маленькое значение [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) (вплоть до доли секунды);
* неотвечающие локальные прослушиватели ретрансляции (или проблемы, связанные с тем, что правила брандмауэра запрещают прослушивателям принимать новые клиентские подключения) и значение [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) меньше 20 секунд.

Пример:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Основные причины
Существует две основные причины этой ошибки.

*   **Неправильная конфигурация**
    
    Время ожидания операции может быть слишком коротким для состояния операции. Время ожидания операции в пакете SDK клиента по умолчанию составляет 60 секунд. Проверьте, не задано ли в вашем коде слишком маленькое значение. Обратите внимание, что загрузка ЦП и состояние сети могут повлиять на время, необходимое для выполнения операции. Мы рекомендуем не задавать слишком маленькое значение времени ожидания операции.
*   **Временная ошибка службы**

    Иногда в службе ретранслятора могут происходить задержки при обработке запросов. Например, это может произойти в период интенсивной нагрузки сети. В этом случае можно настроить повторную попытку выполнения операции через некоторое время до ее успешного завершения. Если же операцию по-прежнему не удается выполнить после нескольких попыток, посетите [сайт состояния служб Azure](https://azure.microsoft.com/status/), чтобы получить сведения об известных простоях служб.

## <a name="next-steps"></a>Дополнительная информация
* [Часто задаваемые вопросы о ретрансляторе Azure](relay-faq.md)
* [Создание пространства имен ретранслятора с помощью портала Azure](relay-create-namespace-portal.md)
* [Приступая к работе с гибридными подключениями к ретранслятору](relay-hybrid-connections-dotnet-get-started.md)
* [Приступая к работе с гибридными подключениями к ретранслятору](relay-hybrid-connections-node-get-started.md)

