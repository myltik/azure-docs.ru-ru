<properties 
   pageTitle="Служебная шина и Python с протоколом AMQP 1.0 | Microsoft Azure"
   description="Использование служебной шины в Python с протоколом AMQP"
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
   ms.date="02/08/2016"
   ms.author="sethm" />

# Использование служебной шины в Python с протоколом AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python — это привязка языка Python к Proton-C; т. е. Proton-Python реализован как оболочка для ядра, реализованного на языке C.

## Скачивание клиентской библиотеки Proton

Proton-C и связанные с ним привязки (включая Python) можно загрузить на сайте [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Файл загружается в форме исходного кода. Чтобы создать код, следуйте инструкциям, содержащимся в загруженном пакете.

Обратите внимание, что на момент написания этой статьи поддержка SSL-протокола в Proton-C доступна только для операционных систем Linux. Поскольку для служебной шины Azure требуется использование SSL-протокола, в настоящее время Proton-C (и языковые привязки) можно применять только для доступа к служебной шине в среде Linux. Работа по реализации Proton-C с SSL-протоколом в среде Windows уже ведется, поэтому регулярно проверяйте наличие обновлений.

## Работа с очередями, разделами и подписками служебной шины из Python

В следующем примере кода показано, как отправлять и получать сообщения из сущности обмена сообщениями служебной шины.

### Отправка сообщений с помощью Proton-Python

В следующем примере кода показано, как отправить сообщение из сущности обмена сообщениями служебной шины.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### Получение сообщений с помощью Proton-Python

В следующем примере кода показано, как получить сообщение из сущности обмена сообщениями служебной шины.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## Обмен сообщениями между .NET и Proton-Python

### Свойства приложения

#### Взаимодействие между Proton-Python и API .NET служебной шины

Сообщения Proton-Python поддерживают свойства приложений следующих типов: **int**, **long**, **float**, **uuid**, **bool**, **string**. В следующем примере кода Python показано, как настроить свойства в сообщении с помощью каждого из этих типов свойств.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

В API .NET служебной шины свойства приложения для сообщения передаются в коллекцию **Properties** объекта [BrokeredMessage][]. В следующем примере кода показано, как прочитать свойства приложения для сообщения, полученного от клиента Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

В таблице ниже приведены типы свойств Python и соответствующие им типы свойств .NET.

| Тип свойств Python | Тип свойств .NET |
|----------------------|--------------------|
| int | int |
| float; | double |
| длинное целое число | int64 |
| uuid | guid |
| bool | bool |
| строка | строка |

#### Взаимодействие между API .NET служебной шины и Proton-Python

Тип [BrokeredMessage][] поддерживает свойства приложений следующих типов: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** и **TimeSpan**. В следующем примере кода .NET показано, как настроить свойства в объекте [BrokeredMessage][] с помощью каждого из этих типов свойств.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

В следующем примере кода Python показано, как прочитать свойства приложения для сообщения, полученного от клиента .NET служебной шины.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

В таблице ниже приведены типы свойств .NET и соответствующие им типы свойств Python.

| Тип свойств .NET | Тип свойств Python | Примечания |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | int | - | | sbyte | int | - | | char | char | Класс Proton-Python | | short | int | - | | ushort | int | - | | int | int | - | | uint | int | - | | long | int | - | | ulong | long | Класс Proton-Python | | float | float | - | | double | float | - | | decimal | String | В настоящее время Proton не поддерживает тип decimal. | | bool | bool | - | | Guid | uuid | Класс Proton-Python | | string | string | - | | DateTime | timestamp | Класс Proton-Python | | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks сопоставляется с типом AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | | TimeSpan | DescribedType | Timespan.Ticks сопоставляется с типом AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | | Uri | DescribedType | Uri.AbsoluteUri сопоставляется с типом AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Стандартные свойства

В следующих таблицах приведены свойства стандартных сообщений Proton-Python и соответствующие им свойства стандартных сообщений [BrokeredMessage][].

#### Взаимодействие между Proton-Python и API .NET служебной шины

| Proton-Python | .NET служебной шины | Примечания |
|----------------------|--------------------------|-----------------------------------------------------------|
| durable | Недоступно | Служебная шина поддерживает только устойчивые сообщения. |
| priority | Недоступно | Служебная шина поддерживает только приоритет одиночных сообщений. |
| Ttl | Message.TimeToLive | Преобразование; срок жизни сообщений Proton-Python определяется в миллисекундах. |
| first\_acquirer | Недоступно | - | | delivery\_count | Недоступно | - | | Id | Message.MessageID | - | | user\_id | Недоступно | - | | address | Message.To | - | | subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationID | - | | content\_type | Message.ContentType | - | | content\_encoding | Недоступно | - | | expiry\_time | Недоступно | - | | creation\_time | Недоступно | - | | group\_id | Message.SessionId | - | | group\_sequence | Недоступно | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | format | Недоступно | - |

| .NET служебной шины | Proton | Примечания |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType | Message.content\_type | - | | CorrelationId | Message.correlation\_id | - | | EnqueuedTimeUtc | Недоступно | - | | Label | Message.subject | - | | MessageId | Message.id | - | | ReplyTo | Message.reply\_to | - | | ReplyToSessionId | Message.reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | Недоступно | - | | SessionId | Message.group\_id | - | | TimeToLive | Message.ttl | Преобразование; срок жизни сообщений Proton-Python определяется в миллисекундах. | | To | Message.address | - |

## Дальнейшие действия

Хотите узнать больше? Перейдите по следующим ссылкам:

- [Протокол AMQP служебной шины — обзор]
- [Протокол AMQP служебной шины для Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[Протокол AMQP служебной шины для Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md

<!---HONumber=AcomDC_0211_2016-->