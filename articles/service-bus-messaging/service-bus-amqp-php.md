---
title: Служебная шина и PHP с протоколом AMQP 1.0 | Microsoft Docs
description: Использование служебной шины в PHP с протоколом AMQP
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm

---
# <a name="using-service-bus-from-php-with-amqp-1.0"></a>Использование служебной шины на платформе PHP с протоколом AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP — это привязка языка PHP к Proton-C; т. е. Proton-PHP реализован как оболочка для ядра, реализованного на языке C.

## <a name="downloading-the-proton-client-library"></a>Загрузка клиентской библиотеки Proton
Proton-C и связанные с ним привязки (включая PHP) можно скачать с сайта [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Файл загружается в форме исходного кода. Чтобы создать код, следуйте инструкциям, содержащимся в загруженном пакете.

> [!IMPORTANT]
> На момент написания этой статьи поддержка SSL-протокола в Proton-C доступна только для операционных систем Linux. Поскольку для служебной шины Azure требуется использование SSL-протокола, в настоящее время Proton-C (и языковые привязки) можно применять только для доступа к служебной шине в среде Linux. Работа по реализации Proton-C с SSL-протоколом в среде Windows уже ведется, поэтому регулярно проверяйте наличие обновлений.
> 
> 

## <a name="working-with-service-bus-queues,-topics,-and-subscriptions-from-php"></a>Работа с очередями, разделами и подписками служебной шины из PHP
В следующем примере кода показано, как отправлять и получать сообщения из сущности обмена сообщениями служебной шины.

### <a name="sending-messages-using-proton-php"></a>Отправка сообщений с помощью Proton-PHP
В следующем примере кода показано, как отправить сообщение из сущности обмена сообщениями служебной шины.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Получение сообщений с помощью Proton-PHP
В следующем примере кода показано, как получить сообщение из сущности обмена сообщениями служебной шины.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-.net-and-proton-php"></a>Обмен сообщениями между .NET и Proton-PHP
### <a name="application-properties"></a>Свойства приложения
#### <a name="protonphp-to-service-bus-.net-apis"></a>Взаимодействие между Proton-PHP и API .NET служебной шины
Сообщения Proton-PHP поддерживают свойства приложений следующих типов: **integer**, **double**, **Boolean**, **string** и **object**. В следующем примере кода PHP показано, как настроить свойства в сообщении с помощью каждого из этих типов свойств.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

В API .NET служебной шины свойства приложения для сообщения передаются в коллекцию **Properties** объекта [BrokeredMessage][BrokeredMessage]. В следующем примере кода показано, как прочитать свойства приложения для сообщения, полученного от клиента PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

В таблице ниже приведены типы свойств PHP и соответствующие им типы свойств .NET.

| Тип свойств PHP | Тип свойств .NET |
| --- | --- |
| целое число |int |
| double |double |
| Логическое |bool |
| string |string |
| object |Объект |

#### <a name="service-bus-.net-apis-to-php"></a>Взаимодействие между API .NET служебной шины и PHP
Тип [BrokeredMessage][BrokeredMessage] поддерживает свойства приложений следующих типов: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** и **TimeSpan**. В следующем примере кода .NET показано, как настроить свойства в объекте [BrokeredMessage][BrokeredMessage] с помощью каждого из этих типов свойств.

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
```

В следующем примере кода PHP показано, как прочитать свойства приложения для сообщения, полученного от клиента .NET служебной шины.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

В таблице ниже приведены типы свойств .NET и соответствующие им типы свойств PHP.

| Тип свойств .NET | Тип свойств PHP | Примечания |
| --- | --- | --- |
| byte. |целое число |- |
| sbyte |целое число |- |
| char; |Char |Класс Proton-PHP |
| short |целое число |- |
| ushort |целое число |- |
| int |целое число |- |
| uint |Число |- |
| длинное целое число |целое число |- |
| ulong |целое число |- |
| float; |double |- |
| double |double |- |
| Decimal |строка |Decimal в настоящее время не поддерживается в Proton. |
| bool |Логическое |- |
| Guid |UUID |Класс Proton-PHP |
| строка |строка |- |
| DateTime |целое число |- |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks сопоставляется с типом AMQP:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan |DescribedType |Timespan.Ticks сопоставляется с типом AMQP:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> |
| URI |DescribedType |Uri.AbsoluteUri сопоставляется с типом AMQP:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### <a name="standard-properties"></a>Стандартные свойства
В следующих таблицах приведены свойства стандартных сообщений Proton-PHP и соответствующие им свойства стандартных сообщений [BrokeredMessage][BrokeredMessage].

| Proton-PHP | .NET служебной шины | Примечания |
| --- | --- | --- |
| durable |Недоступно |Служебная шина поддерживает только устойчивые сообщения. |
| Приоритет |Недоступно |Служебная шина поддерживает только приоритет одиночных сообщений. |
| Ttl |Message.TimeToLive |Преобразование; срок жизни сообщений Proton-PHP определяется миллисекундами. |
| first\_acquirer |- |- |
| delivery\_count |- |- |
| Идентификатор |Message.Id |- |
| user\_id |- |- |
| Адрес |Message.To |- |
| Субъект |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationId |- |
| content\_type |Message.ContentType |- |
| content\_encoding |Недоступно |- |
| expiry\_time |Message.ExpiresAtUTC |- |
| creation\_time |Недоступно |- |
| group\_id |Message.SessionId |- |
| group\_sequence |- |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| Формат |Недоступно |- |

#### <a name="service-bus-.net-apis-to-proton-php"></a>Взаимодействие между API .NET служебной шины и Proton-PHP
| .NET служебной шины | Proton-PHP | Примечания |
| --- | --- | --- |
| ContentType |Message-\>content\_type |- |
| CorrelationId |Message-\>correlation\_id |- |
| EnqueuedTimeUtc |Message-\>annotations[x-opt-enqueued-time] |- |
| Метка |Message-\>subject |- |
| MessageId |Message-\>id |- |
| ReplyTo |Message-\>reply\_to |- |
| ReplyToSessionId |Message-\>reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |Message-\>annotations ["x-opt-scheduled-enqueue-time"] |- |
| SessionId |Message-\>group\_id |- |
| TimeToLive (срок жизни) |Message-\>ttl |Преобразование; срок жизни сообщений Proton-PHP определяется миллисекундами. |
| Кому |Message-\>address |- |

## <a name="next-steps"></a>Дальнейшие действия
Хотите узнать больше? Перейдите по следующим ссылкам:

* [Протокол AMQP служебной шины — обзор]
* [Протокол AMQP служебной шины для Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[Протокол AMQP служебной шины для Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md



<!--HONumber=Oct16_HO2-->


