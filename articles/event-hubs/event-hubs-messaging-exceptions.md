<properties 
    pageTitle="Исключения обмена сообщениями концентраторов событий | Microsoft Azure"
    description="Список исключений обмена сообщениями концентраторов событий и предлагаемые действия."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/18/2016"
    ms.author="sethm" />

# Исключения обмена сообщениями концентраторов событий

В этой статье перечислены некоторые исключения, создаваемые API обмена сообщениями служебной шины Azure. Эти API работают и с концентраторами событий. Этот справочник может измениться, поэтому следите за обновлениями.

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
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) | Сервер не ответил на запрошенную операцию в течение указанного времени, которое задается параметром [OperationTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx). Возможно, сервер выполнил запрошенную операцию. Это может произойти из-за сетевых или других задержек в инфраструктуре. | Проверьте состояние системы для обеспечения согласованности и при необходимости повторите попытку. | Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) | Запрошенная пользователем операция не допускается в пределах сервера или службы. Подробные сведения см. в сообщении об исключении. Например, операция [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) создаст это исключение, если сообщение было получено в режиме **ReceiveAndDelete**. | Проверьте код и обратитесь к документации. Убедитесь, что запрошенная операция допустима. | Повторная попытка не поможет. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Предпринята попытка вызвать операцию с объектом, который уже закрыт, прерван или удален. Иногда это означает, что внешняя транзакция уже удалена. | Проверьте код и убедитесь, что он не вызывает операции с удаленным объектом. | Повторная попытка не поможет. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Объекту [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) не удалось получить маркер. Маркер является недопустимым или не содержит утверждений, необходимых для выполнения операции. | Убедитесь, что поставщик маркеров создан с использованием правильных значений. Проверьте конфигурацию службы контроля доступа. | Повторная попытка может помочь в некоторых случаях. Добавьте в код логику повторных попыток. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Для этого метода предоставлен один или несколько недопустимых аргументов. Универсальный код ресурса (URI), переданный в [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) или [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx), содержит сегменты пути. В [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) или [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.create.aspx) передана недопустимая схема URI. Размер значения превышает 32 КБ. | Проверьте вызывающий код и убедитесь, что аргументы заданы правильно. | Повторная попытка не поможет. |
| [MessagingEntityNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitynotfoundexception.aspx) | Сущность, связанная с операцией, не существует или была удалена. | Убедитесь, что сущность существует. | Повторная попытка не поможет. |
| [MessageNotFoundException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagenotfoundexception.aspx) | Предпринята попытка получить сообщение с определенным порядковым номером. Это сообщение не найдено. | Убедитесь, что сообщение уже не было получено. Проверьте очередь недоставленных сообщений, чтобы увидеть, не находится ли в ней сообщение. | Повторная попытка не поможет. |
| [MessagingCommunicationException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingcommunicationexception.aspx) | Клиент не может установить соединение с концентратором событий. | Убедитесь, что имя узла указано правильно и узел доступен. | Повторная попытка может помочь при наличии периодических сбоев подключения. |
| [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) | В настоящее время служба не может обработать запрос. | Клиент может некоторое время подождать, после чего следует повторить операцию. | Клиент может повторить операцию через определенный промежуток времени. Если в результате повтора возникает другое исключение, проверьте поведение повтора этого исключения. |
| [MessageLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagelocklostexception.aspx) | Истек срок действия маркера блокировки, связанного с данным сообщением, или маркер блокировки не найден. | Удалите сообщение. | Повторная попытка не поможет. |
| [SessionLockLostException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessionlocklostexception.aspx) | Потеряна блокировка, связанная с данным сеансом. | Прервите объект [MessageSession](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.aspx). | Повторная попытка не поможет. |
| [MessagingException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx) | Универсальное исключение обмена сообщениями, которое может быть вызвано в следующих случаях: предпринята попытка создать [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) с использованием имени или пути, которые относятся к другому типу сущности (например, разделу). Предпринята попытка отправить сообщение размером свыше 256 КБ. Во время обработки запроса возникла ошибка сервера или службы. Подробные сведения см. в сообщении об исключении. Как правило, это временное исключение. | Проверьте код и убедитесь, что для текста сообщения применяются только сериализуемые объекты (или используйте настраиваемый сериализатор). Обратитесь к документации для получения сведений о поддерживаемых типах значений свойств и используйте только поддерживаемые типы. Проверьте свойство [IsTransient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx). Если оно имеет значение **true**, можно повторить операцию. | Алгоритм поведения не определен и может не помочь. |
| [MessagingEntityAlreadyExistsException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentityalreadyexistsexception.aspx) | Предпринята попытка создать сущность с именем, которое уже используется другой сущностью в этом пространстве имен службы. | Удалите существующую сущность или выберите другое имя для сущности, которую нужно создать. | Повторная попытка не поможет. |
| [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) | Сущность обмена сообщениями достигла максимально допустимого размера. | Создайте в сущности пространство, получая сообщения из сущности или ее подочередей. | Повторная попытка может помочь, если сообщения были удалены.                                                           
|
| [SessionCannotBeLockedException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sessioncannotbelockedexception.aspx) | Предпринята попытка принять сеанс с конкретным идентификатором сеанса, но сеанс в данный момент заблокирован другим клиентом. | Убедитесь, что сеанс разблокирован другими клиентами. | Повторная попытка может помочь, если сеанс был освобожден до этого времени. |
| [TransactionSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transactionsizeexceededexception_methods.aspx) | В состав транзакции входит слишком много операций. | Сократите количество операций, входящих в состав этой транзакции. | Повторная попытка не поможет. |
| [MessagingEntityDisabledException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingentitydisabledexception.aspx) | Выполнен запрос на операцию среды выполнения с отключенной сущностью. | Активируйте сущность. | Повторная попытка может помочь, если сущность была активирована. |
| [MessageSizeExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesizeexceededexception.aspx) | Полезные данные сообщения превышают предел в 256 КБ. Обратите внимание, что предел в 256 КБ является общим размером сообщения, в который могут входить системные свойства и нагрузка .NET. | Сократите размер полезных данных сообщения, а затем повторите операцию. | Повторная попытка не поможет. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) | Внешняя транзакция (*Transaction.Current*) является недопустимой. Возможно, она завершена или прервана. Дополнительные сведения можно найти во внутреннем исключении. | | Повторная попытка не поможет. | - 
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) | Предпринята попытка выполнить операцию с сомнительной транзакцией либо попытка зафиксировать транзакцию, и транзакция становится сомнительной. | Ваше приложение должно обрабатывать это исключение (как особый случай), так как транзакция уже может быть зафиксирована. | - |

## QuotaExceededException

[QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx) указывает на превышение квоты для конкретного объекта.

### Концентраторы событий

Концентраторы событий имеют ограничение в 20 групп потребителей на один концентратор событий. При попытке создать больше групп появляется исключение [QuotaExceededException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.quotaexceededexception.aspx).

## TimeoutException 

[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) указывает, что запущенная пользователем операция занимает больше времени, чем время ожидания операции.

### Концентраторы событий

Для концентраторов событий время ожидания указывается как часть строки подключения или с помощью [ServiceBusConnectionStringBuilder](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.aspx). Само сообщение об ошибке может отличаться, но оно всегда содержит значение времени ожидания, указанное для текущей операции.

### Основные причины

Существует две основные причины этой ошибки: неправильная конфигурация или временная ошибка службы.

1. **Неправильная конфигурация**. Время ожидания операции может быть слишком коротким для состояния операции. Время ожидания операции в пакете SDK клиента по умолчанию составляет 60 секунд. Проверьте, не задано ли в вашем коде слишком маленькое значение. Учтите, что состояние сети и загрузка ЦП могут повлиять на время, необходимое для выполнения конкретной операции, поэтому не следует задавать слишком маленькое значение времени ожидания операции.

2. **Временная ошибка службы**. Иногда служба концентраторов событий может испытывать задержки при обработке запросов, например в периоды интенсивной загрузки сети. В таких случаях можно настроить повторную попытку выполнения операции через некоторое время до ее успешного завершения. Если же операцию по-прежнему не удается выполнить после нескольких попыток, посетите [сайт состояния служб Azure](https://azure.microsoft.com/status/), чтобы получить сведения об известных простоях служб.

## Дальнейшие действия

Полный справочник по API .NET служебной шины и концентраторов событий см. в [справочнике Azure на сайте MSDN](https://msdn.microsoft.com/library/azure/mt419900.aspx).

<!---HONumber=AcomDC_0824_2016-->