---
title: Как использовать AMQP 1.0 с API служебной шины Java | Документация Майкрософт
description: Как использовать службу сообщений Java (JMS) со служебной шиной Azure и протоколом AMQP 1.0.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23044179"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Как использовать API службы сообщений Java (JMS) со служебной шиной и AMQP 1.0
AMQP 1.0 — это эффективный и надежный протокол обмена сообщениями на уровне соединения, который можно использовать для создания надежных кроссплатформенных приложений для обмена сообщениями.

Поддержка AMQP 1.0 в Service Bus означает, что с помощью эффективного двоичного протокола можно на различных платформах использовать возможности очередей и обмена сообщениями с публикацией/подпиской у брокера. Кроме того, можно создавать приложения, содержащие компоненты, созданные с использованием разнообразных языков, платформ и операционных систем.

В этой статье описывается использование функций обмена сообщениями через служебную шину (очередей и публикации разделов или подписки на них) из приложений Java, использующих популярный стандарт API JMS. В [связанной статье](service-bus-amqp-dotnet.md) объясняется, как выполнить те же действия, используя API .NET служебной шины. Эти два руководства можно использовать совместно для изучения обмена сообщениями между различными платформами с помощью AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Приступая к работе со служебной шиной
В этом руководстве предполагается, что вы уже создали пространство имен служебной шины, содержащее очередь с именем **queue1**. Если это не так, можно [создать пространство имен и очередь](service-bus-create-namespace-portal.md), используя [портал Azure](https://portal.azure.com). Дополнительные сведения о создании пространства имен и очередей служебной шины см. в статье [Приступая к работе с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Секционированные очереди и разделы также поддерживают AMQP. Дополнительные сведения см. в статьях [Секционированные сущности обмена сообщениями](service-bus-partitioning.md) и [Поддержка AMQP 1.0 для секционированных очередей и разделов служебной шины](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Загрузка клиентской библиотеки AMQP 1.0 JMS
Информацию о том, где скачать последнюю версию клиентской библиотеки Apache Qpid JMS AMQP 1.0, см. на странице [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

При построении и запуске приложений JMS с использованием служебной шины необходимо добавить следующие 4 JAR-файла из архива распространения Apache Qpid JMS AMQP 1.0 в Java CLASSPATH:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-amqp-1-0-client-[version].jar
* qpid-amqp-1-0-client-jms-[version].jar
* qpid-amqp-1-0-common-[version].jar

## <a name="coding-java-applications"></a>Создание приложений Java
### <a name="java-naming-and-directory-interface-jndi"></a>Интерфейс JNDI
JMS использует интерфейс JNDI для разделения логических и физических имен. С помощью JNDI разрешаются два типа объектов JMS: ConnectionFactory и Destination. JNDI использует модель поставщика, к которой можно подключить различные службы каталогов для обработки заданий разрешения имен. Библиотека Apache Qpid JMS AMQP 1.0 поставляется с простым файловым поставщиком JNDI, настроенным с помощью файла свойств в следующем формате:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Настройка ConnectionFactory
Эта запись используется для определения **ConnectionFactory** в поставщике JNDI файла свойств Qpid в следующем формате:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Где **[jndi_name]** и **[ConnectionURL]** означают следующее:

* **[jndi_name]**: логическое имя ConnectionFactory. Это имя, которое будет разрешено в Java-приложении с помощью метода JNDI IntialContext.lookup().
* **[ConnectionURL]**: URL-адрес, предоставляющий библиотеке JMS сведения, необходимые брокеру AMQP.

Формат **ConnectionURL** выглядит следующим образом:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Где **[namespace]**, **[SASPolicyName]** и **[SASPolicyKey]** означают следующее:

* **[namespace]**: пространство имен служебной шины;
* **[SASPolicyName]**: имя политики подписанного URL-адреса;
* **[SASPolicyKey]**: ключ политики подписанного URL-адреса.

> [!NOTE]
> Необходимо применить URL-кодирование к паролю вручную. Полезная служебная программа URL-кодирования доступна по адресу [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).
> 
> 

#### <a name="configure-destinations"></a>Настройка назначений
Эта запись используется для определения назначения в поставщике JNDI файла свойств Qpid в следующем формате:

```
queue.[jndi_name] = [physical_name]
```

или

```
topic.[jndi_name] = [physical_name]
```

Где **[jndi\_name]** и **[physical\_name]** означают следующее:

* **[jndi_name]**: логическое имя назначения. Это имя, которое будет разрешено в Java-приложении с помощью метода JNDI IntialContext.lookup().
* **[physical_name]**: имя сущности служебной шины, которой приложение отправляет сообщения или от которого оно получает сообщения.

> [!NOTE]
> При получении данных из подписки раздела служебной шины физическое имя, указанное в JNDI, должно быть именем раздела. Имя подписки предоставляется при создании устойчивой подписки в коде приложения JMS. В [руководстве разработчика AMQP 1.0 для служебной шины](service-bus-amqp-dotnet.md) содержатся дополнительные сведения о работе с разделами служебной шины в JMS.
> 
> 

### <a name="write-the-jms-application"></a>Написание приложения JMS
Не существует специальных API-интерфейсов или параметров для использования JMS с Service Bus. Однако существует несколько ограничений, которые будут рассмотрены ниже. Как и для любого приложения JMS, первое, что необходимо — это конфигурация среды JNDI, позволяющая разрешать **ConnectionFactory** и назначения.

#### <a name="configure-the-jndi-initialcontext"></a>Настройка исходного контекста JNDI
Для настройки среды JNDI хэш-таблица со сведениями о конфигурации передаются в конструктор класса javax.naming.InitialContext. Два обязательных элемента в хэш-таблице — это имя класса фабрики исходного контекста и URL-адрес поставщика. В следующем примере кода показано, как настроить среду JNDI для использования поставщика JNDI на основе файла свойств Qpid с именем **servicebus.properties**.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Простое приложение JMS, использующее очередь служебной шины
Следующий пример программы отправляет текстовые сообщения JMS в очередь Service Bus с логическим JNDI-именем QUEUE и получает ответные сообщения.

```java
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

        // Look up ConnectionFactory and Queue
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
```

### <a name="run-the-application"></a>Выполнение приложения
Запущенное приложение выводит следующие данные:

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

## <a name="cross-platform-messaging-between-jms-and-net"></a>Межплатформенный обмен сообщениями между JMS и .NET
В этом руководстве мы рассмотрели, как отправлять и получать сообщения Service Bus с помощью JMS. Однако одно из основных преимуществ использования AMQP 1.0 состоит в том, что этот протокол позволяет приложениям, созданным из компонентов, написанных на разных языках, надежно и точно обмениваться сообщениями.

Используя пример приложения JMS выше и аналогичное приложение .NET, взятое из связанной статьи [Использование служебной шины на платформе .NET с протоколом AMQP 1.0](service-bus-amqp-dotnet.md), можно организовать обмен сообщениями между .NET и Java. В этой статье представлены сведения о кроссплатформенном обмене сообщениями с помощью служебной шины и AMQP 1.0.

### <a name="jms-to-net"></a>Из JMS в .NET
Для демонстрации отправки сообщении из JMS в .NET выполните следующее:

* Запустите пример приложения .NET без параметров командной строки.
* Запустите пример приложения Java с параметром командной строки sendonly. В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* В консоли приложения Java несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением .NET.

#### <a name="output-from-jms-application"></a>Вывод приложения JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Вывод приложения .NET
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>Из .NET в JMS
Для демонстрации отправки сообщений из .NET в JMS выполните следующее:

* Запустите пример приложения .NET с параметром командной строки sendonly. В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* Запустите пример приложения Java без параметров командной строки.
* В консоли приложения .NET несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением Java.

#### <a name="output-from-net-application"></a>Вывод приложения .NET
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Вывод приложения JMS
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Неподдерживаемые возможности и ограничения
При использовании JMS по протоколу AMQP 1.0 с Service Bus действуют следующие ограничения:

* Для одного **сеанса** допускается только один **MessageProducer** или **MessageConsumer**. Если требуется создать несколько **MessageProducers** или **MessageConsumers** в приложении, создайте специальный **сеанс** для каждого из них.
* Временные подписки раздела в настоящее время не поддерживаются.
* **MessageSelectors** сейчас не поддерживаются.
* Временные назначения, например **TemporaryQueue** и **TemporaryTopic**, сейчас не поддерживаются, как и использующие их API-интерфейсы **QueueRequestor** и **TopicRequestor**.
* Сеансы транзакций и распределенные транзакции не поддерживаются.

## <a name="summary"></a>Сводка
В этом практическом руководстве показано использование функций обмена сообщениями, выполняемых посредством служебной шины (очередей и разделов публикации/подписки), из Java с использованием популярного JMS API и протокола AMQP 1.0.

Протокол AMQP 1.0 Service Bus можно также использовать из других языков, в числе которых .NET, C, Python и PHP. Компоненты, созданные с помощью этих языков, могут надежно и точно обмениваться сообщениями, используя AMQP 1.0 в Service Bus.

## <a name="next-steps"></a>Дополнительная информация
* [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md)
* [Как использовать AMQP 1.0 с API .NET служебной шины](service-bus-dotnet-advanced-message-queuing.md)
* [Руководство разработчика AMQP 1.0 для служебной шины](service-bus-amqp-dotnet.md)
* [Начало работы с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md)
* [Центр разработчиков Java](https://azure.microsoft.com/develop/java/)

