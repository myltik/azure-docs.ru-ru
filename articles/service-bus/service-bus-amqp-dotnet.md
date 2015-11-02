<properties 
   pageTitle="Служебная шина и .NET с протоколом AMQP 1.0 | Microsoft Azure"
   description="Использование служебной шины на платформе .NET с протоколом AMQP"
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
   ms.date="10/15/2015"
   ms.author="sethm" />

# Использование служебной шины на платформе .NET с протоколом AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Загрузка пакета SDK служебной шины.

Поддержка AMQP 1.0 доступна в пакете SDK служебной шины версии 2.1 или более поздней. Последнюю версию пакета SDK можно скачать в [NuGet][].

## Настройка приложений .NET для использования протокола AMQP 1.0

По умолчанию клиентская библиотеке служебной шины .NET взаимодействует со службой служебной шины, используя специализированный протокол на базе SOAP. Чтобы использовать протокол AMQP 1.0 вместо протокола по умолчанию, необходимо явно настроить строку подключения служебной шины, как описано в следующем разделе. Помимо этих изменений, код приложения остается в прежнем состоянии при использовании AMQP 1.0.

Текущий выпуск содержит некоторые функции интерфейса API, не поддерживаемые при использовании AMQP. Эти неподдерживаемые функции перечислены ниже в разделе «Неподдерживаемые функции и ограничения». Кроме того, при использовании AMQP отличается значение некоторых дополнительных параметров конфигурации.

### Настройка с помощью файла App.config

Рекомендуется использовать файл конфигурации App.config для хранения настроек при работе с приложениями. Если вы используете приложения служебной шины, в файле App.config можно хранить значение параметра строки подключения **ConnectionString** служебной шины. Ниже приводится пример файла App.config:

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

Значение параметра `Microsoft.ServiceBus.ConnectionString` — это строка подключения служебной шины, используемая для настройки подключения к служебной шине. Используется следующий формат:

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Где `[namespace]` и `SharedAccessKey` получены с портала Azure. Дополнительные сведения см. в разделе [Использование очередей Service Bus][].

Если вы используете AMQP, добавьте строку подключения с `;TransportType=Amqp`. Эта запись сообщает клиентской библиотеке о создании подключения к служебной шине с помощью протокола AMQP 1.0.

## Сериализация сообщений

Если вы используете протокол по умолчанию, поведением, определяющим сериализацию клиентской библиотеки .NET по умолчанию, является использование типа [DataContractSerializer][] для сериализации экземпляра [BrokeredMessage][]. Это делает возможным транспорт между клиентской библиотекой и службой служебной шины. При использовании транспортного режима AMQP клиентская библиотека применяет систему типов AMQP для сериализации [сообщения в брокере][BrokeredMessage] в сообщение AMQP. Сериализация позволяет получать и распознавать сообщение принимающим приложением, выполняемым на разных платформах. Например, это может быть приложение Java, которое использует JMS API для доступа к служебной шине.

Создавая экземпляр [BrokeredMessage][], вы можете предоставить объект .NET конструктору в качестве параметра (как текст сообщения). Для объектов, которые могут быть сопоставлены с простыми типами AMQP, текст сообщения сериализуется в типы данных AMQP. Если объект не удается непосредственно сопоставить с простым типом AMQP (когда пользовательский тип определяется приложением), объект сериализуется с использованием [DataContractSerializer][], а сериализованные байты отправляются в сообщении о данных AMQP.

Для упрощения взаимодействия с клиентами, отличными от .NET, используйте только те типы .NET, которые могут быть сериализованы непосредственно в типы AMQP (как текст сообщения). В таблице ниже приведены эти типы, а также соответствующие им типы AMQP.

| Тип объекта текста .NET | Сопоставленный тип AMQP | Тип раздела текста AMQP |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| bool | Логическое | Значение AMQP |
| byte | ubyte | Значение AMQP |
| ushort | ushort | Значение AMQP |
| uint | uint | Значение AMQP |
| ulong | ulong | Значение AMQP |
| sbyte | byte | Значение AMQP |
| short | short | Значение AMQP |
| int | int | Значение AMQP |
| длинное целое число | длинное целое число | Значение AMQP |
| float; | float; | Значение AMQP |
| double | double | Значение AMQP |
| decimal; | decimal128 | Значение AMQP |
| char; | char; | Значение AMQP |
| DateTime | Timestamp | Значение AMQP |
| Guid | uuid | Значение AMQP |
| byte | binary; | Значение AMQP |
| строка | строка | Значение AMQP |
| System.Collections.IList | list | Значение AMQP. В коллекции могут содержаться только элементы, которые определены в этой таблице. |
| System.Array | array | Значение AMQP. В коллекции могут содержаться только элементы, которые определены в этой таблице. |
| System.Collections.IDictionary | map | Значение AMQP. В коллекции могут содержаться только элементы, которые определены в этой таблице. Примечание: поддерживаются только строковые ключи. |
| URI | Описанный тип string (см. таблицу ниже) | Значение AMQP |
| DateTimeOffset | Описанный тип long (см. таблицу ниже) | Значение AMQP |
| TimeSpan | Описанный тип long (см. таблицу ниже) | Значение AMQP |
| Поток | binary; | Данные AMQP (может быть несколько). Разделы данных содержат необработанные байты из объекта Stream. |
| Другой объект | binary; | Данные AMQP (может быть несколько). Содержит сериализованные двоичные данные объекта, который использует DataContractSerializer или предоставленный приложением сериализатор. |

| Тип .NET | Сопоставленный описанный тип AMQP | Примечания |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| URI | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | Uri.AbsoluteUri |
| DateTimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## Неподдерживаемые функции, ограничения и различия в поведении

Следующие функции API служебной шины .NET в настоящее время не поддерживаются при использовании AMQP:

-   транзакции;

-   отправка посредством назначения передачи;

-   получение по порядковому номеру сообщения;

-   обзор сообщений и сеансов;

-   состояние сеанса;

-   API-интерфейсы на основе пакетов;

-   масштабируемое получение;

-   изменение правил подписки во время выполнения;

-   возобновление блокировки сеанса.

В частности, в настоящее время при использовании AMQP не поддерживаются следующие интерфейсы API:

- [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession][];
- [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)][];

- [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][];

- [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)][];
- [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch][];
- [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][];
- [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek][];
- [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch][];

- [Microsoft.ServiceBus.Messaging.QueueClient.Peek][];
- [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch][];

- [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})][];

- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)][];
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch][];
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})][];
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek][];
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch][];
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule][];
- [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)][];

- [Microsoft.ServiceBus.Messaging.MessageSession.GetState][];
- [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)][];
- [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock][];

- [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock][].

Существуют также некоторые различия в поведении API .NET служебной шины при использовании AMQP в сравнении с протоколом по умолчанию:

-   Свойство [OperationTimeout][] игнорируется.

-   `MessageReceiver.Receive(TimeSpan.Zero)` реализуется в виде `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## Параметры управления протоколом AMQP

API-интерфейсы .NET предоставляют несколько параметров для управления поведением протокола AMQP.

-   **MessageReceiver.PrefetchCount**: определяет начальные разрешения, которые применяются к ссылке. Значение по умолчанию — 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize**: определяет максимальный размер кадра AMQP, доступный во время согласования при открытии подключения. Значение по умолчанию: 65 536 байт.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval**: если передачу данных можно разбить на пакеты, это значение определяет максимальную задержку отправки стратегий обработки. По умолчанию наследуется отправителями и получателями. Отдельные отправители или получатели могут переопределить значение по умолчанию, составляющее 20 миллисекунд.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity**: определяет, устанавливаются ли AMQP-подключения с использованием протокола SSL. Значение по умолчанию — **true**.

## Дальнейшие действия

Хотите узнать больше? Перейдите по следующим ссылкам:

- [Протокол AMQP служебной шины — обзор]
- [Протокол AMQP служебной шины для Windows Server]

  [Использование очередей Service Bus]: service-bus-dotnet-how-to-use-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [Microsoft.ServiceBus.Messaging.MessageSender.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh322665.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.Peek]: https://msdn.microsoft.com/library/azure/jj908731.aspx
  [Microsoft.ServiceBus.Messaging.MessageReceiver.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.QueueClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.TopicClient.SendBatch(System.Collections.Generic.IEnumerable{Microsoft.ServiceBus.Messaging.BrokeredMessage})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.sendbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Receive(System.Int64)]: https://msdn.microsoft.com/library/azure/hh293110.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.ReceiveBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.receivebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.CompleteBatch(System.Collections.Generic.IEnumerable{System.Guid})]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.completebatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.Peek]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peek.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.PeekBatch]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.AddRule]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.addrule.aspx
  [Microsoft.ServiceBus.Messaging.SubscriptionClient.RemoveRule(System.String)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.removerule.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.GetState]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.SetState(System.IO.Stream)]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx
  [Microsoft.ServiceBus.Messaging.MessageSession.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx
  [Microsoft.ServiceBus.Messaging.BrokeredMessage.RenewLock]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/

[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md
[Протокол AMQP служебной шины для Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=Oct15_HO4-->