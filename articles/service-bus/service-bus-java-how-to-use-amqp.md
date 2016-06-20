<properties 
	pageTitle="Использование AMQP 1.0 с API служебной шины для Java | Microsoft Azure" 
	description="Узнайте, как использовать службу сообщений Java (JMS) со служебной шиной Azure и протоколом AMQP."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"  
	manager="timlt" 
	editor="" />

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/09/2016" 
	ms.author="sethm"/>

# Как использовать API службы сообщений Java (JMS) со служебной шиной и AMQP 1.0

AMQP 1.0 — это эффективный и надежный протокол обмена сообщениями на уровне соединения, который можно использовать для создания надежных кроссплатформенных приложений для обмена сообщениями. Поддержка AMQP 1.0 была добавлена в Azure Service Bus в октябре 2012 г. и перешла к общей доступности (GA) в мае 2013 г.

Добавление AMQP 1.0 означает, что теперь возможно использовать функции очереди и сообщений с публикацией/подпиской у брокера, относящиеся к служебной шине, на целом ряде платформ с применением эффективного двоичного протокола. Кроме того, можно создавать приложения, содержащие компоненты, созданные с использованием разнообразных языков, платформ и операционных систем.

В этом практическом руководстве описывается использование функций обмена сообщениями, выполняемых посредством шины обслуживания (очередей и разделов публикации/подписки), из приложений Java, использующих популярный стандарт API JMS.

## Приступая к работе со служебной шиной

В этом руководстве предполагается, что вы уже создали пространство имен служебной шины, содержащее очередь с именем **queue1**. Если это не так, можно создать пространство имен и очередь, используя [классический портал Azure](http://manage.windowsazure.com). Дополнительную информацию о создании пространства имен и очередей служебной шины см. в статье [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md).

### Загрузка клиентской библиотеки AMQP 1.0 JMS

Информацию о том, где скачать последнюю версию клиентской библиотеки Apache Qpid JMS AMQP 1.0, см. на странице [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

При построении и запуске приложений JMS с использованием служебной шины необходимо добавить следующие 4 JAR-файла из архива распространения Apache Qpid JMS AMQP 1.0 в Java CLASSPATH:

*    geronimo-jms\_1.1\_spec-1.0.jar
*    qpid-amqp-1-0-client-[version].jar
*    qpid-amqp-1-0-client-jms-[version].jar
*    qpid-amqp-1-0-common-[version].jar

## Создание приложений Java

### Интерфейс JNDI

JMS использует интерфейс JNDI для разделения логических и физических имен. С помощью JNDI разрешаются два типа объектов JMS: ConnectionFactory и Destination. JNDI использует модель поставщика, к которой можно подключить различные службы каталогов для обработки заданий разрешения имен. Библиотека Apache Qpid JMS AMQP 1.0 поставляется с простым файловым поставщиком JNDI, настроенным с помощью файла свойств в следующем формате:

```
# servicebus.properties – sample JNDI configuration
	
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
	
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### Настройка ConnectionFactory

Эта запись используется для определения **ConnectionFactory** в поставщике JNDI файла свойств Qpid в следующем формате:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

где **[jndi\_name]** и **[ConnectionURL]** имеют следующий смысл:

- **[jndi\_name]**: логическое имя ConnectionFactory. Это имя, которое будет разрешено в Java-приложении с помощью метода JNDI IntialContext.lookup().
- **[ConnectionURL]**: URL-адрес, предоставляющий библиотеке JMS сведения, необходимые брокеру AMQP.

Формат **URL\_подключения** выглядит следующим образом:

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

где **[namespace]**, **[username]** и **[password]** имеют следующий смысл:

- **[namespace]**: пространство имен служебной шины.
- **[username]**: имя издателя служебной шины.
- **[password]**: ключ издателя служебной шины, закодированный в URL-адресе.

> [AZURE.NOTE] Необходимо применить URL-кодирование к паролю вручную. Полезная служебная программа URL-кодирования доступна по адресу [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### Настройка назначений

Эта запись используется для определения назначения в поставщике JNDI файла свойств Qpid в следующем формате:

```
queue.[jndi_name] = [physical_name]
```
или

```
topic.[jndi_name] = [physical_name]
```

где **[jndi\_name]** и **[physical\_name]** имеют следующий смысл:

- **[jndi\_name]**: логическое имя назначения. Это имя, которое будет разрешено в Java-приложении с помощью метода JNDI IntialContext.lookup().
- **[physical\_name]**: имя сущности служебной шины, которой приложение отправляет сообщения или от которого оно получает сообщения.

> [AZURE.NOTE] При получении данных из подписки раздела Service Bus физическое имя, указанное в JNDI, должно быть именем раздела. Имя подписки предоставляется при создании устойчивой подписки в коде приложения JMS. В [руководстве разработчика AMQP 1.0 для служебной шины](service-bus-amqp-dotnet.md) содержатся дополнительные сведения о работе с подписками разделов служебной шины в JMS.

### Написание приложения JMS

Не существует специальных API-интерфейсов или параметров для использования JMS с Service Bus. Однако существует несколько ограничений, которые будут рассмотрены ниже. Как и для любого приложения JMS, первое, что необходимо — это конфигурация среды JNDI, позволяющая разрешать **ConnectionFactory** и назначения.

#### Настройка исходного контекста JNDI

Для настройки среды JNDI хэш-таблица со сведениями о конфигурации передаются в конструктор класса javax.naming.InitialContext. Два обязательных элемента в хэш-таблице — это имя класса фабрики исходного контекста и URL-адрес поставщика. В следующем примере кода показано, как настроить среду JNDI для использования поставщика JNDI на основе файла свойств Qpid с именем **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### Простое приложение JMS, использующее очередь служебной шины

Следующий пример программы отправляет текстовые сообщения JMS в очередь служебной шины с логическим JNDI-именем QUEUE и получает ответные сообщения.

	// SimpleSenderReceiver.java
	
	import javax.jms.*;
	import javax.naming.Context;
	import javax.naming.InitialContext;
	import java.io.BufferedReader;
	import java.io.InputStreamReader;
	import java.util.Hashtable;
	import java.util.Random;
	
	public class SimpleSenderReceiver implements MessageListener {
	    private static boolean runReceiver = true;
	    private Connection connection;
	    private Session sendSession;
	    private Session receiveSession;
	    private MessageProducer sender;
	    private MessageConsumer receiver;
	    private static Random randomGenerator = new Random();
	
	    public SimpleSenderReceiver() throws Exception {
	        // Configure JNDI environment
	        Hashtable<String, String> env = new Hashtable<String, String>();
	        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
	        env.put(Context.PROVIDER_URL, "servicebus.properties");
	        Context context = new InitialContext(env);
	
	        // Lookup ConnectionFactory and Queue
	        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	        Destination queue = (Destination) context.lookup("QUEUE");
	
	        // Create Connection
	        connection = cf.createConnection();
	
	        // Create sender-side Session and MessageProducer
	        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
	        sender = sendSession.createProducer(queue);
	
	        if (runReceiver) {
	            // Create receiver-side Session, MessageConsumer,and MessageListener
	            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
	            receiver = receiveSession.createConsumer(queue);
	            receiver.setMessageListener(this);
	            connection.start();
	        }
	    }
	
	    public static void main(String[] args) {
	        try {
	
	            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
	                runReceiver = false;
	            }
	
	            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
	            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
	
	            while (true) {
	                String s = commandLine.readLine();
	                if (s.equalsIgnoreCase("exit")) {
	                    simpleSenderReceiver.close();
	                    System.exit(0);
	                } else {
	                    simpleSenderReceiver.sendMessage();
	                }
	            }
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	
	    private void sendMessage() throws JMSException {
	        TextMessage message = sendSession.createTextMessage();
	        message.setText("Test AMQP message from JMS");
	        long randomMessageID = randomGenerator.nextLong() >>>1;
	        message.setJMSMessageID("ID:" + randomMessageID);
	        sender.send(message);
	        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
	    }
	
	    public void close() throws JMSException {
	        connection.close();
	    }
	
	    public void onMessage(Message message) {
	        try {
	            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
	            message.acknowledge();
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	}	

### Выполнение приложения

В результате запуска приложения выводятся следующие данные:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
	
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
	
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## Межплатформенный обмен сообщениями между JMS и .NET

В этом руководстве мы рассмотрели, как отправлять и получать сообщения Service Bus с помощью JMS. Однако одно из основных преимуществ использования AMQP 1.0 состоит в том, что этот протокол позволяет приложениям, созданным из компонентов, написанных на разных языках, надежно и точно обмениваться сообщениями.

Используя приведенный выше пример приложения JMS и аналогичное приложение .NET, взятое из связанного руководства [Использование AMQP 1.0 с Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md), можно организовать обмен сообщениями между .NET и Java.

Дополнительные сведения о межплатформенном обмене сообщениями с помощью Service Bus и AMQP 1.0 см. в статье [Руководство разработчика AMQP 1.0 для Service Bus](service-bus-amqp-dotnet.md).

### Из JMS в .NET

Для демонстрации отправки сообщении из JMS в .NET выполните следующее:

* Запустите пример приложения .NET без параметров командной строки.
* Запустите пример приложения Java с параметром командной строки sendonly. В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* В консоли приложения Java несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением .NET.

#### Вывод приложения JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### Вывод приложения .NET

```
> SimpleSenderReceiver.exe	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### Из .NET в JMS

Для демонстрации отправки сообщений из .NET в JMS выполните следующее:

* Запустите пример приложения .NET с параметром командной строки sendonly. В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* Запустите пример приложения Java без параметров командной строки.
* В консоли приложения .NET несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением Java.

#### Вывод приложения .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### Вывод приложения JMS

```
> java SimpleSenderReceiver	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## Неподдерживаемые возможности и ограничения

При использовании JMS по протоколу AMQP 1.0 с Service Bus действуют следующие ограничения:

* Для одного **сеанса** допускается только один **MessageProducer** или **MessageConsumer**. Если требуется создать несколько **MessageProducers** или **MessageConsumers** в приложении, создайте специальный **сеанс** для каждого из них.
* Временные подписки раздела в настоящее время не поддерживаются.
* **MessageSelectors** в настоящее время не поддерживаются.
* Временные назначения, т. е. **TemporaryQueue** и **TemporaryTopic**, в данный момент не поддерживаются, как и API-интерфейсы **QueueRequestor** и **TopicRequestor**, которые используют их.
* Сеансы транзакций и распределенные транзакции не поддерживаются.

## Сводка

В этом практическом руководстве показано использование функций обмена сообщениями, выполняемых посредством Service Bus (очередей и разделов публикации/подписки), из Java с использованием популярного JMS API и протокола AMQP 1.0.

Протокол AMQP 1.0 служебной шины можно также использовать из других языков, в числе которых .NET, C, Python и PHP. Компоненты, созданные с помощью этих языков, могут надежно и точно обмениваться сообщениями, используя AMQP 1.0 в Service Bus. Дополнительные сведения см. в [руководстве разработчика AMQP 1.0 для Service Bus](service-bus-amqp-dotnet.md).

## Дальнейшие действия

* [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md)
* [Как использовать AMQP 1.0 с API .NET служебной шины](service-bus-dotnet-advanced-message-queuing.md)
* [Руководство разработчика AMQP 1.0 для служебной шины](service-bus-amqp-dotnet.md)
* [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md)
 

<!---HONumber=AcomDC_0608_2016-->