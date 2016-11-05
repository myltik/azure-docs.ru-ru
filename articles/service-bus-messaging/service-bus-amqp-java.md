---
title: Служебная шина и Java с протоколом AMQP 1.0 | Microsoft Docs
description: Использование служебной шины на платформе Java с протоколом AMQP
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
# <a name="use-service-bus-from-java-with-amqp-1.0"></a>Использование служебной шины для Java с протоколом AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Служба сообщений Java (JMS) — это стандартный API для работы с ПО промежуточного слоя, ориентированного на обработку сообщений, в рамках платформы Java. Служебная шина Microsoft Azure протестирована с помощью протокола AMQP 1.0 на основе клиентской библиотеки JMS, разработанной проектом Apache Qpid. Эта библиотека поддерживает полную версию API JMS 1.1 и может использоваться с любой службой обмена сообщениями, совместимой с AMQP 1.0. Этот сценарий также поддерживается [служебной шиной для Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (локальной служебной шиной). Дополнительные сведения см. в статье [Протокол AMQP служебной шины для Windows Server][Протокол AMQP служебной шины для Windows Server].

## <a name="download-the-apache-qpid-amqp-1.0-jms-client-library"></a>Загрузка клиентской библиотеки Apache Qpid JMS AMQP 1.0
Сведения о скачивании последней версии клиентской библиотеки Apache Qpid JMS AMQP 1.0 см. на странице [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

При построении и запуске приложений JMS с использованием служебной шины необходимо добавить следующие 4 JAR-файла из архива распространения Apache Qpid JMS AMQP 1.0 в Java CLASSPATH:

* geronimo-jms\_1.1\_spec-[version].jar
* qpid-amqp-1-0-client-[version].jar
* qpid-amqp-1-0-client-jms-[version].jar
* qpid-amqp-1-0-common-[version].jar

## <a name="work-with-service-bus-queues,-topics,-and-subscriptions-from-jms"></a>Работа с очередями, разделами и подписками служебной шины из JMS
### <a name="java-naming-and-directory-interface-(jndi)"></a>Интерфейс JNDI
JMS использует интерфейс JNDI для разделения логических и физических имен. С помощью JNDI разрешаются два типа объектов JMS: **ConnectionFactory** и **Destination**. JNDI использует модель поставщика, к которой можно подключить различные службы каталогов для обработки заданий разрешения имен. Библиотека Apache Qpid JMS AMQP 1.0 поставляется с простым поставщиком JNDI файла свойств, который настраивается с помощью текстового файла.

Поставщик JNDI файла свойств Qpid настраивается с помощью файла свойств в следующем формате:

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### <a name="configure-the-connection-factory"></a>Настройка фабрики подключения
Эта запись используется для определения **ConnectionFactory** в поставщике JNDI файла свойств Qpid в следующем формате:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Где `[jndi\_name]` и `[ConnectionURL]` обозначают следующее:

| Имя | Значение |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| `[jndi\_name]` |Логическое имя фабрики подключения. Это имя разрешается в приложении Java с использованием метода JNDI `IntialContext.lookup()`. | | | | |
| `[ConnectionURL]` |URL-адрес, предоставляющий библиотеке JMS сведения, необходимые брокеру AMQP. | | | | |

Формат URL-адреса подключения выглядит следующим образом:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Где `[namespace]`, `[username]` и `[password]` обозначают следующее:

| Имя | Значение |  |  |  |  |
| --- | --- | --- | --- | --- | --- |
| `[namespace]` |Пространство имен служебной шины, полученное с [портала Azure][]. | | | | |
| `[username]` |Ключ SAS служебной шины, полученный с [портала Azure][]. | | | | |
| `[password]` |Ключ SAS служебной шины, полученный с [портала Azure][], в формате URL-адреса. | | | | |

> [!NOTE]
> Необходимо применить URL-кодирование к паролю вручную. Удобная служебная программа для кодирования URL-адресов доступна на странице [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

Например, с портала могут быть получены следующие данные:

| Пространство имен: | test.servicebus.windows.net |
| --- | --- |
| Имя издателя: |RootManageSharedAccessKey |
| Ключ издателя: |abcdefg |

Поэтому строка конфигурации для определения объекта **ConnectionFactory** с именем `SBCONNECTIONFACTORY` будет выглядеть следующим образом:

```
connectionfactory.SBCONNECTIONFACTORY = amqps://RootManageSharedAccessKey:abcdefg@test.servicebus.windows.net
```

#### <a name="configure-destinations"></a>Настройка назначений
Запись, которая определяет назначение в поставщике JNDI файла свойств Qpid, имеет следующий формат:

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Где `[jndi\_name]` и `[physical\_name]` обозначают следующее:

| Имя | Значение |
| --- | --- |
| `[jndi\_name]` |Логическое имя назначения. Это имя разрешается в приложении Java с использованием метода JNDI `IntialContext.lookup()`. |
| `[physical\name]` |Имя сущности служебной шины, которой приложение отправляет сообщения или от которого оно получает сообщения. |

Обратите внимание на следующее.

* Значение `[physical\name]` может быть очередью или разделом служебной шины.
* При получении данных из подписки раздела служебной шины физическое имя, указанное в JNDI, должно быть именем раздела. Имя подписки предоставляется при создании устойчивой подписки в коде приложения JMS.
* Можно также обрабатывать подписку раздела служебной шины как очередь JMS. У этого подхода есть несколько преимуществ. Например, для очередей и подписок раздела можно использовать один и тот же код получателя. Кроме того, все сведения об адресах (имена разделов и подписок) указываются во внешнем файле свойств.
* Для обработки подписки раздела служебной шины как очереди JMS запись в файле свойств должна быть указана в следующем формате: `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

Для определения логического назначения JMS с именем TOPIC, соответствующего разделу служебной шины с именем topic1, запись в файле свойств должна быть указана в следующем формате.

```
topic.TOPIC = topic1
```

### <a name="send-messages-using-jms"></a>Отправка сообщений с помощью JMS
В следующем коде показано, как отправить сообщение в раздел служебной шины. Предполагается, что `SBCONNECTIONFACTORY` и `TOPIC` определены в файле конфигурации **servicebus.properties**, как описано в предыдущем разделе.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 

InitialContext context = new InitialContext(env); 

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### <a name="receive-messages-using-jms"></a>Получение сообщений с помощью JMS
В следующем коде показано `how`, чтобы получить сообщение из подписки раздела служебной шины. Предполагается, что `SBCONNECTIONFACTORY` и TOPIC определены в файле конфигурации **servicebus.properties**, как описано в предыдущем разделе. Также предполагается, что имя подписки — `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 

InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### <a name="guidelines-for-building-robust-applications"></a>Рекомендации по созданию надежных приложений
В спецификации JMS определяется контракт на исключения для методов API. Кроме того, для обработки таких исключений должен быть написан код приложения. Ниже приведены некоторые аспекты, которые следует учитывать при обработке исключений.

* Зарегистрируйте **ExceptionListener** с помощью подключения JMS, используя **connection.setExceptionListener**. Это позволяет клиенту асинхронно получать уведомления о проблеме. Это уведомление особенно важно для подключений, которые только используют сообщения, поскольку это единственный способ сообщить им об ошибке подключения. **ExceptionListener** вызывается, если существует проблема с базовым подключением AMQP, сеансом или ссылкой. В этом случае приложению следует повторно создать объекты **JMS Connection**, **Session**, **MessageProducer** и **MessageConsumer**.
* Для проверки отправки сообщения из объекта **MessageProducer** в сущность служебной шины в настройках приложения необходимо указать набор системных свойств **qpid.sync\_publish**. Это можно сделать, запустив приложение с параметром виртуальной машины Java **-Dqpid.sync\_publish=true** в командной строке. Если указать этот параметр, библиотека не будет возвращать результат вызова отправки, пока не будет получено подтверждение о принятии сообщения служебной шиной. Если в ходе отправки возникает ошибка, выдается исключение **JMSException**. Существуют две возможные причины. 
  
  1. Если проблема возникает из-за того, что служебная шина отклоняет определенное отправленное сообщение, будет вызвано исключение **MessageRejectedException**. Эта ошибка либо временная, либо связанная с определенной проблемой в сообщении. Рекомендуется несколько раз повторить операцию с использованием логики переключения в пассивный режим. Если проблема сохранится, сообщение следует отклонить с регистрацией ошибки в локальном журнале. В таком случае нет необходимости повторно создавать объекты **JMS Connection**, **Session** или **MessageProducer**. 
  2. Если проблема возникает из-за того, что служебная шина закрывает ссылку AMQP, будет вызвано исключение **InvalidDestinationException**. Это может быть связано с временным сбоем или удалением сущности обмена сообщениями. Объекты **JMS Connection**, **Session** и **MessageProducer** в любом случае следует создать заново. Если ошибка была временной, эта операция в конечном счете будет выполнена успешно. Если сущность удалена, ошибка будет возникать постоянно.

## <a name="messaging-between-.net-and-jms"></a>Обмен сообщениями между .NET и JMS
### <a name="message-bodies"></a>Текст сообщений
В JMS определены пять типов сообщений: **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** и **TextMessage**. В служебной шине API .NET есть только один тип сообщений — [BrokeredMessage][BrokeredMessage].

#### <a name="jms-to-service-bus-.net-api"></a>Взаимодействие между JMS и API .NET служебной шины
В следующих разделах показано, как получать сообщения каждого типа JMS из .NET. Пример **ObjectMessage** не включен, так как текст **ObjectMessage** содержит сериализуемый объект на языке программирования Java, который не интерпретируется приложением .NET.

##### <a name="bytesmessage"></a>BytesMessage
В следующем коде показано, как получить текст объекта **BytesMessage** с помощью API .NET служебной шины.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### <a name="mapmessage"></a>MapMessage
В следующем коде показано, как получить текст объекта **MapMessage** с помощью API .NET служебной шины. Этот код выполняет итерацию по элементам карты, отображая имя и значение каждого элемента.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### <a name="streammessage"></a>StreamMessage
В следующем коде показано, как получить текст объекта **StreamMessage** с помощью API .NET служебной шины. В этом коде перечисляются все элементы из потока, а также их типы.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### <a name="textmessage"></a>TextMessage
В следующем коде показано, как получить текст объекта **TextMessage** с помощью API .NET служебной шины. Этот код отображает текстовую строку, содержащуюся в тексте сообщения.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### <a name="service-bus-.net-apis-to-jms"></a>Взаимодействие между API .NET служебной шины и JMS
В следующих разделах показано, как приложение .NET может создать сообщение всех типов JMS, получаемое в JMS. Пример **ObjectMessage** не включен, так как текст **ObjectMessage** содержит сериализуемый объект на языке программирования Java, который не интерпретируется приложением .NET.

##### <a name="bytesmessage"></a>BytesMessage
В следующем коде показано, как создать объект [BrokeredMessage][BrokeredMessage] в .NET. Этот объект клиент JMS получает как объект **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### <a name="streammessage"></a>StreamMessage
В следующем коде показано, как создать объект [BrokeredMessage][BrokeredMessage] в .NET. Этот объект клиент JMS получает как объект **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### <a name="textmessage"></a>TextMessage
В следующем коде показано, как получить текст объекта **TextMessage** с помощью API .NET служебной шины. Этот код отображает текстовую строку, содержащуюся в тексте сообщения.

```
message = new BrokeredMessage("this is a text string");
```

### <a name="application-properties"></a>Свойства приложения
#### <a name="jms-to-service-bus-.net-apis"></a>Взаимодействие между JMS и API .NET служебной шины
Сообщения JMS поддерживают свойства приложений следующих типов: **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** и **String**. В следующем примере кода .NET показано, как настроить свойства в сообщении с помощью каждого из этих типов свойств.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

В API .NET служебной шины свойства приложения для сообщения передаются в коллекцию **Properties** объекта [BrokeredMessage][BrokeredMessage]. В следующем примере кода показано, как прочитать свойства приложения для сообщения, полученного от клиента JMS.

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

В таблице ниже представлены типы свойств JMS и соответствующие им типы свойств .NET.

| Тип свойств JMS | Тип свойств .NET |
| --- | --- |
| Byte |sbyte |
| Число |int |
| Float |float; |
| Double |double |
| Логический |bool |
| Строка |строка |

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

В следующем примере кода Java показано, как прочитать свойства приложения для сообщения, полученного от клиента .NET служебной шины.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

В таблице ниже представлены типы свойств .NET и соответствующие им типы свойств JMS.

| Тип свойств .NET | Тип свойств JMS | Примечания |
| --- | --- | --- |
| byte. |UnsignedByte |- |
| sbyte |Byte |- |
| char; |Character |- |
| short |Сокращение |- |
| ushort |UnsignedShort |- |
| int |Число |- |
| uint |UnsignedInteger |- |
| длинное целое число |длинное целое |- |
| ulong |UnsignedLong |- |
| float; |Float |- |
| double |Double |- |
| Decimal |BigDecimal |- |
| bool |Логический |- |
| Guid |UUID |- |
| строка |Строка |- |
| DateTime |Дата |- |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks сопоставляется с типом AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan |DescribedType |Timespan.Ticks сопоставляется с типом AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| URI |DescribedType |Uri.AbsoluteUri сопоставляется с типом AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### <a name="standard-headers"></a>Стандартные заголовки
В следующей таблице показано сопоставление стандартных заголовков JMS и стандартных свойств [BrokeredMessage][BrokeredMessage] в AMQP 1.0.

#### <a name="jms-to-service-bus-.net-apis"></a>Взаимодействие между JMS и API .NET служебной шины
| JMS | .NET служебной шины | Примечания |
| --- | --- | --- |
| JMSCorrelationID |Message.CorrelationID |- |
| JMSDeliveryMode |В настоящее время недоступно. |Служебная шина поддерживает только устойчивые сообщения, например DeliveryMode.PERSISTENT, независимо от того, что указано. |
| JMSDestination |Message.To |- |
| JMSExpiration |Message. TimeToLive (срок жизни) |Преобразование |
| JMSMessageID |Message.MessageID |По умолчанию JMSMessageID кодируется в сообщении AMQP в двоичной форме. Получая двоичный идентификатор сообщения, клиентская библиотека .NET преобразует строковое представление на основе значений байтов в Юникод. Чтобы библиотека JMS начала использовать строковые идентификаторы сообщений, добавьте строку binary-messageid=false в параметры запроса ConnectionURL JNDI. Например: “amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false”. |
| JMSPriority |В настоящее время недоступно. |Служебная шина не поддерживает приоритет сообщений. |
| JMSRedelivered |В настоящее время недоступно. |- |
| JMSReplyTo |Message. ReplyTo |- |
| JMSTimestamp |Message.EnqueuedTimeUtc |Преобразование |
| JMSType |Message.Properties[“jms-type”] |- |

#### <a name="service-bus-.net-apis-to-jms"></a>Взаимодействие между API .NET служебной шины и JMS
| .NET служебной шины | JMS | Примечания |
| --- | --- | --- |
| ContentType |- |В настоящее время недоступно. |
| CorrelationId |JMSCorrelationID |- |
| EnqueuedTimeUtc |JMSTimestamp |Преобразование |
| Метка |Недоступно |В настоящее время недоступно. |
| MessageId |JMSMessageID |- |
| ReplyTo |JMSReplyTo |- |
| ReplyToSessionId |Недоступно |В настоящее время недоступно. |
| ScheduledEnqueueTimeUtc |Недоступно |В настоящее время недоступно. |
| SessionId |Недоступно |В настоящее время недоступно. |
| TimeToLive (срок жизни) |JMSExpiration |Преобразование |
| Кому |JMSDestination |- |

## <a name="unsupported-features-and-restrictions"></a>Неподдерживаемые возможности и ограничения
При использовании JMS через протокол AMQP 1.0 со служебной шиной существуют следующие ограничения.

* Для одного сеанса допускается только один объект **MessageProducer** или **MessageConsumer**. Если в приложении нужно создать несколько объектов **MessageProducer** или **MessageConsumer**, создайте выделенные сеансы для каждого из них.
* Временные подписки раздела в настоящее время не поддерживаются.
* Объекты **MessageSelector** не поддерживаются.
* Временное место назначения, например **TemporaryQueue** или **TemporaryTopic**, не поддерживается, как и интерфейсы API **QueueRequestor** и **TopicRequestor**, которые его используют.
* Транзакционные сеансы не поддерживаются.
* Распределенные транзакции не поддерживаются.

## <a name="next-steps"></a>Дальнейшие действия
Хотите узнать больше? Перейдите по следующим ссылкам:

* [Протокол AMQP служебной шины — обзор]
* [Протокол AMQP служебной шины для Windows Server]

[Протокол AMQP служебной шины для Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Протокол AMQP служебной шины — обзор]: service-bus-amqp-overview.md
[портал Azure]: https://portal.azure.com



<!--HONumber=Oct16_HO2-->


