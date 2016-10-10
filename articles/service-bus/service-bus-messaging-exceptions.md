<properties 
    pageTitle="Исключения обмена сообщениями служебной шины | Microsoft Azure"
    description="Список исключений обмена сообщениями служебной шины и предлагаемые действия."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# Исключения обмена сообщениями служебной шины

В этой статье перечислены некоторые исключения, создаваемые API обмена сообщениями служебной шины Microsoft Azure. Этот справочник может измениться, поэтому следите за обновлениями.

## Категории исключений

API обмена сообщениями создают исключения, которые можно разделить на следующие категории с указанием связанного действия, предпринимаемого для их устранения. Обратите внимание, что значение и причины исключения могут различаться в зависимости от типа объекта обмена сообщениями (ретрансляторы, очереди и разделы, концентраторы событий).

1.  Ошибка в коде пользователя ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Общее действие: прежде чем продолжить, попытайтесь исправить код.

2.  Ошибка настройки или конфигурации ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Общее действие: проверьте конфигурацию и измените ее при необходимости.

3.  Временные исключения ([Microsoft.ServiceBus.Messaging.MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx), [Microsoft.ServiceBus.Messaging.ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Общее действие: повторите операцию или уведомите пользователей.

4.  Другие исключения ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx), [Microsoft.ServiceBus.Messaging.SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx)). Общее действие: связано с типом исключения; см. таблицу в следующем разделе.

## Типы исключений

В следующей таблице перечислены типы исключений обмена сообщениями и их причины, а также рекомендуемые действия, которые можно выполнять.

| **Тип исключения** | **Описание, причина, примеры** | **Рекомендуемое действие** | **Примечание к автоматическому или немедленному повтору** |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) | Сервер не ответил на запрошенную операцию в течение указанного времени, контролируемого параметром [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за сетевых или других задержек в инфраструктуре. | Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку. См. раздел [TimeoutException](#timeoutexception). | Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) | Запрошенная пользователем операция не допускается в пределах сервера или службы. Подробные сведения см. в сообщении об исключении. Например, операция [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) создаст это исключение, если сообщение было получено в режиме **ReceiveAndDelete**. | Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. | Повторная попытка не поможет. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. | Проверьте код и убедитесь, что он не вызывает операции с удаленным объектом. | Повторная попытка не поможет. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Объекту [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) не удалось получить маркер. Маркер является недопустимым или не содержит утверждений, необходимых для выполнения операции. | Убедитесь, что поставщик маркеров создан с использованием правильных значений. Проверьте конфигурацию службы контроля доступа. | Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Для этого метода предоставлен один или несколько недопустимых аргументов.<br /> Универсальный код ресурса (URI), переданный в [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) или [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx), содержит сегменты пути.<br /> В [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) или [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) передана недопустимая схема URI. <br />Значение свойства превышает 32 КБ. | Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. | Повторная попытка не поможет. |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) | Сущность, связанная с операцией, не существует или была удалена. | Убедитесь, что сущность существует. | Повторная попытка не поможет. |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) | Предпринята попытка получить сообщение с определенным порядковым номером. Это сообщение не найдено. | Убедитесь, что сообщение уже не было получено. Проверьте очередь недоставленных сообщений, чтобы увидеть, не находится ли в ней сообщение. | Повторная попытка не поможет. |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) | Клиент не сможет установить соединение со служебной шиной. | Убедитесь, что имя узла указано правильно и узел доступен. | Повторная попытка может помочь при наличии периодических сбоев подключения. |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) | В настоящее время служба не может обработать запрос. | Клиент может некоторое время подождать, после чего следует повторить операцию. | Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) | Истек срок действия маркера блокировки, связанного с данным сообщением, или маркер блокировки не найден. | Удалите сообщение. | Повторная попытка не поможет. |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) | Потеряна блокировка, связанная с данным сеансом. | Прервите объект [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx). | Повторная попытка не поможет. |
| [MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) | Универсальное исключение обмена сообщениями, которое может быть вызвано в следующих случаях:<br /> предпринята попытка создать [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) с использованием имени или пути, которые относятся к другому типу сущности (например, разделу).<br /> Предпринята попытка отправить сообщение размером свыше 256 КБ. Во время обработки запроса возникла ошибка сервера или службы. Подробные сведения см. в сообщении об исключении. Как правило, это временное исключение. | Проверьте код и убедитесь, что для текста сообщения применяются только сериализуемые объекты (или используйте настраиваемый сериализатор). Обратитесь к документации для получения сведений о поддерживаемых типах значений свойств и используйте только поддерживаемые типы. Проверьте свойство [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx). Если оно имеет значение **true**, можно повторить операцию. | Алгоритм поведения не определен и может не помочь. |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) | Предпринята попытка создать сущность с именем, которое уже используется другой сущностью в этом пространстве имен службы. | Удалите существующую сущность или выберите другое имя для сущности, которую нужно создать. | Повторная попытка не поможет. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) | Сущность обмена сообщениями достигла максимально допустимого размера. | Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. См. раздел [QuotaExceededException](#quotaexceededexception). | Повторная попытка может помочь, если сообщения были удалены. |
| [RuleActionException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruleactionexception.aspx) | Служебная шина возвращает это исключение, если вы пытаетесь создать недопустимое действие правила. Служебная шина вкладывает это исключение в недоставленное сообщение, если во время обработки действия правила для данного сообщения произошла ошибка. | Проверьте правильность действия правила. | Повторная попытка не поможет. |
| [FilterException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.filterexception.aspx) | Служебная шина возвращает это исключение, если вы пытаетесь создать недопустимый фильтр. Служебная шина вкладывает это исключение в недоставленное сообщение, если во время обработки фильтра для данного сообщения произошла ошибка. | Проверьте правильность фильтра. | Повторная попытка не поможет. |
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) | Предпринята попытка принять сеанс с конкретным идентификатором сеанса, но сеанс в данный момент заблокирован другим клиентом. | Убедитесь, что сеанс разблокирован другими клиентами. | Повторная попытка может помочь, если сеанс был освобожден до этого времени. |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) | В состав транзакции входит слишком много операций. | Сократите количество операций, входящих в состав этой транзакции. | Повторная попытка не поможет. |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) | Выполнен запрос на операцию среды выполнения с отключенной сущностью. | Активируйте сущность. | Повторная попытка может помочь, если сущность была активирована. |
| [NoMatchingSubscriptionException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.nomatchingsubscriptionexception.aspx) | Служебная шина возвращает это исключение, если сообщение отправлено в раздел, в котором включена предварительная фильтрация, и ни один из фильтров не соответствует. | Убедитесь, что соответствует по меньшей мере один фильтр. | Повторная попытка не поможет. |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) | Полезные данные сообщения превышают предел в 256 КБ. Обратите внимание, что предел в 256 КБ является общим размером сообщения, в который могут входить системные свойства и нагрузка .NET. | Сократите размер полезных данных сообщения, а затем повторите операцию. | Повторная попытка не поможет. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) | Внешняя транзакция (*Transaction.Current*) является недопустимой. Возможно, она завершена или прервана. Дополнительные сведения можно найти во внутреннем исключении. | | Повторная попытка не поможет. | - | [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) | Предпринята попытка выполнить операцию с сомнительной транзакцией либо попытка зафиксировать транзакцию, и транзакция становится сомнительной. | Ваше приложение должно обрабатывать это исключение (как особый случай), так как транзакция уже может быть зафиксирована. | - |

## QuotaExceededException

[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) указывает на превышение квоты для конкретного объекта.

### Очереди и разделы

Для очередей и разделов это, как правило, размер очереди. Свойство сообщения об ошибке будет содержать дополнительные сведения, как показано в следующем примере:

```
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
	Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Сообщение уведомляет о том, что для раздела превышено ограничение размера, в данном случае 1 ГБ (ограничение по умолчанию).

#### Основные причины

Существует две основные причины этой ошибки: очередь недоставленных сообщений и неработающие получатели сообщений.

1. **Очередь недоставленных сообщений**. Средству чтения не удается завершить доставку сообщений, и сообщения возвращаются в очередь или раздел по истечении срока действия блокировки. Это может произойти, если в средстве чтения возникает исключение, блокирующее вызов [BrokeredMessage.Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx). После 10 попыток считывания сообщения оно по умолчанию перемещается в очередь недоставленных сообщений. Это поведение управляется свойством [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) со значением по умолчанию "10". По мере накапливания сообщений в очереди недоставленных сообщений они начинают занимать много места.

	Чтобы устранить эту проблему, считайте и завершите сообщения в очереди недоставленных сообщений, как и в любой другой очереди. Класс [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) содержит удобный метод [FormatDeadLetterPath](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.formatdeadletterpath.aspx), который помогает форматировать путь очереди недоставленных сообщений.

2. **Получатель остановлен**. Получатель прекратил прием сообщений из очереди или подписки. Определить эту проблему можно с помощью свойства [QueueDescription.MessageCountDetails](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecountdetails.aspx), которое выводит полную статистику по сообщениям. Если значение свойства [ActiveMessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagecountdetails.activemessagecount.aspx) большое или растет, значит сообщения не считываются с той же скоростью, с которой отправляются.

### Концентраторы событий

Концентраторы событий имеют ограничение в 20 групп потребителей на один концентратор событий. При попытке создать больше групп появляется исключение [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx).

### Передача

Для ретранслятора служебной шины это исключение служит оболочкой для исключения [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), указывающего, что для этой конечной точки было превышено максимальное число прослушивателей. Это указывается в значении поля **MaximumListenersPerEndpoint** сообщения об исключении.

## TimeoutException 

[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции.

Следует проверить значение свойства [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit), так как достижение этого ограничения также может вызвать исключение [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### Очереди и разделы

Для очередей и разделов время ожидания указывается в свойстве [MessagingFactorySettings.OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) (как часть строки подключения) или с помощью [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx). Само сообщение об ошибке может отличаться, но оно всегда содержит значение времени ожидания, указанное для текущей операции.

### Концентраторы событий

Для концентраторов событий время ожидания указывается как часть строки подключения или с помощью [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx). Само сообщение об ошибке может отличаться, но оно всегда содержит значение времени ожидания, указанное для текущей операции.

### Передача

Для ретранслятора служебной шины исключения времени ожидания могут отображаться при первом открытии подключения отправителя ретрансляции. Существует две основные причины этого исключения:

1. слишком маленькое значение [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) (вплоть до доли секунды);
2. неотвечающие локальные прослушиватели ретрансляции (или проблемы, связанные с тем, что правила брандмауэра запрещают прослушивателям принимать новые клиентские подключения) и значение [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) меньше 20 секунд.

Например:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### Основные причины

Существует две основные причины этой ошибки: неправильная конфигурация или временная ошибка службы.

1. **Неправильная конфигурация**. Время ожидания операции может быть слишком коротким для состояния операции. Время ожидания операции в пакете SDK клиента по умолчанию составляет 60 секунд. Проверьте, не задано ли в вашем коде слишком маленькое значение. Учтите, что состояние сети и загрузка ЦП могут повлиять на время, необходимое для выполнения конкретной операции, поэтому не следует задавать слишком маленькое значение времени ожидания операции.

2. **Временная ошибка службы**. Иногда служба служебной шины может испытывать задержки при обработке запросов, например, в периоды интенсивной загрузки сети. В таких случаях можно настроить повторную попытку выполнения операции через некоторое время до ее успешного завершения. Если же операцию по-прежнему не удается выполнить после нескольких попыток, посетите [сайт состояния служб Azure](https://azure.microsoft.com/status/), чтобы получить сведения об известных простоях служб.

## Дальнейшие действия

Полный справочник по API .NET служебной шины и концентраторов событий см. в [справочнике Azure на сайте MSDN](https://msdn.microsoft.com/library/azure/mt419900.aspx).

Дополнительные сведения о [служебной шине](https://azure.microsoft.com/services/service-bus/) см. в следующих разделах.

- [Основные сведения об обмене сообщениями через служебную шину](../service-bus-messaging/service-bus-messaging-overview.md)
- [Базовая информация о служебной шине](service-bus-fundamentals-hybrid-solutions.md)
- [Архитектура служебной шины](service-bus-architecture.md)

<!---HONumber=AcomDC_0928_2016-->