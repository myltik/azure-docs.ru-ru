---
title: Как использовать AMQP 1.0 с API .NET служебной шины | Microsoft Docs
description: Узнайте, как использовать протокол AMQP 1.0 с API .NET для служебной шины Azure.
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/29/2016
ms.author: sethm

---
# <a name="how-to-use-amqp-1.0-with-the-service-bus-.net-api"></a>Как использовать AMQP 1.0 с API .NET служебной шины
AMQP 1.0 — это эффективный и надежный протокол обмена сообщениями на уровне соединения, который можно использовать для создания надежных кроссплатформенных приложений для обмена сообщениями.

Поддержка AMQP 1.0 в Service Bus означает, что с помощью эффективного двоичного протокола можно на различных платформах использовать возможности очередей и обмена сообщениями с публикацией/подпиской у брокера. Кроме того, можно создавать приложения, содержащие компоненты, созданные с использованием разнообразных языков, платформ и операционных систем.

В этой статье описывается, как использовать функции обмена сообщениями через брокер служебной шины (очереди и разделы публикации или подписки) из приложений .NET с помощью API служебной шины для .NET. Существует [сопутствующая статья](service-bus-java-how-to-use-jms-api-amqp.md), в которой объясняется, как выполнить те же действия с помощью стандартного API службы JMS (Java Message Service). Эти два руководства можно использовать совместно для изучения обмена сообщениями между различными платформами с помощью AMQP 1.0.

## <a name="get-started-with-service-bus"></a>Приступая к работе со служебной шиной
В этой статье предполагается, что вы уже создали пространство имен служебной шины, содержащее очередь с именем queue1. Если это не так, можно создать пространство имен и очередь с помощью [портала Azure][]. Дополнительные сведения о создании пространства имен и очередей служебной шины см. в статье [Приступая к работе с очередями служебной шины](service-bus-dotnet-get-started-with-queues.md#1-create-a-namespace-using-the-Azure-portal).

## <a name="download-the-service-bus-sdk"></a>Загрузка пакета SDK служебной шины
Поддержка AMQP 1.0 доступна в пакете служебной шины SDK версии 2.1 или более поздней. Последние версии пакетов можно загрузить из NuGet по адресу: [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## <a name="code-.net-applications"></a>Создание приложений .NET
По умолчанию клиентская библиотеке служебной шины .NET взаимодействует со службой служебной шины, используя специализированный протокол на базе SOAP. Чтобы использовать протокол AMQP 1.0 вместо протокола по умолчанию, требуется явно настроить строку подключения Service Bus, как описано в следующем разделе. Помимо этих изменений, код приложения остается в прежнем состоянии при использовании AMQP 1.0.

Текущий выпуск содержит некоторые функции интерфейса API, не поддерживаемые при использовании AMQP. Эти неподдерживаемые функции перечислены ниже в разделе [Неподдерживаемые функции и ограничения](#unsupported-features-and-restrictions). Кроме того, при использовании AMQP отличается значение некоторых дополнительных параметров конфигурации. В статье не используется ни один из этих параметров, но дополнительные сведения о них доступны в [обзоре AMQP для служебной шины](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### <a name="configure-via-app.config"></a>Настройка с помощью файла App.config
Рекомендуется использовать файл конфигурации App.config для хранения настроек при работе с приложениями. Для приложений служебной шины строку подключения (**ConnectionString**) служебной шины можно хранить в файле App.config. Также в этом примере приложения используется файл App.config для хранения имени сущности обмена сообщениями в служебной шине.

Вот пример файла App.config:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
            <add key="EntityName" value="queue1" />
    </appSettings>
</configuration>
```

### <a name="configure-the-service-bus-connection-string"></a>Настройка строки подключения служебной шины
Значение **Microsoft.ServiceBus.ConnectionString** представляет собой строку подключения служебной шины, используемую для настройки подключения к служебной шине. Используется следующий формат:

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Здесь `[namespace]` и `[SAS key]` получены с [портала Azure][]. Дополнительные сведения см. в статье [Использование очередей служебной шины][].

При использовании AMQP в конец строки подключения добавляется `;TransportType=Amqp`, что указывает клиентской библиотеке использовать AMQP 1.0 для подключения к служебной шине.

### <a name="configure-the-entity-name"></a>Настройка имени сущности
В примере приложения для настройки имени очереди, с которой приложение обменивается сообщениями, используется параметр `EntityName` в разделе **appSettings** файла App.config.

### <a name="a-simple-.net-application-using-a-service-bus-queue"></a>Простое приложение .NET, использующее очередь служебной шины
В следующем примере выполняется обмен сообщениями (отправка и получение) с очередью Service Bus.

```
// SimpleSenderReceiver.cs

using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;

        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;

                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();

                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");

                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }

        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);

            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }

        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }

        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }

    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }

        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }

        public void RequestStop()
        {
            _shouldStop = true;
        }

        private volatile bool _shouldStop;
    }
}
```

### <a name="run-the-application"></a>Выполнение приложения
Запущенное приложение выводит следующие данные:

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf

Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06

Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## <a name="cross-platform-messaging-between-jms-and-.net"></a>Межплатформенный обмен сообщениями между JMS и .NET
В этом разделе показано, как отправлять сообщения в служебную шину с помощью платформы .NET, а также как получать сообщения с помощью .NET. Однако одно из основных преимуществ использования AMQP 1.0 состоит в том, что этот протокол позволяет приложениям, созданным из компонентов, написанных на разных языках, надежно и точно обмениваться сообщениями.

Используя приведенный выше пример приложения .NET и аналогичное приложение Java, взятое из связанного руководства [Использование API JMS со служебной шиной и AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), можно организовать обмен сообщениями между .NET и Java. 

Дополнительные сведения о кроссплатформенном обмене сообщениями с помощью служебной шины и AMQP 1.0 см. в [обзоре AMQP 1.0 для служебной шины](service-bus-amqp-overview.md).

### <a name="jms-to-.net"></a>Из JMS в .NET
Для демонстрации отправки сообщении из JMS в .NET выполните следующее:

* Запустите пример приложения .NET без параметров командной строки.
* Запустите пример приложения Java с параметром командной строки sendonly. В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* В консоли приложения Java несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением .NET.

### <a name="output-from-jms-application"></a>Вывод приложения JMS
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### <a name="output-from-.net-application"></a>Вывод приложения .NET
```
> SimpleSenderReceiver.exe  
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## <a name=".net-to-jms"></a>Из .NET в JMS
Для демонстрации отправки сообщений из .NET в JMS выполните следующее:

* Запустите пример приложения .NET с параметром командной строки sendonly. В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* Запустите пример приложения Java без параметров командной строки.
* В консоли приложения .NET несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением Java.

#### <a name="output-from-.net-application"></a>Вывод приложения .NET
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
Следующие функции API-интерфейса Service Bus .NET при использовании AMQP не поддерживаются:

* Транзакции
* Отправка через место назначения передачи

Дополнительные сведения см. в разделе [Неподдерживаемые функции, ограничения и различия в поведении](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## <a name="summary"></a>Сводка
В этой статье показано, как получить доступ к функциям обмена сообщениями через брокер служебной шины (очереди и разделы публикации или подписки) из приложений .NET с помощью AMQP 1.0 и API служебной шины для .NET.

Протокол AMQP 1.0 служебной шины можно также использовать из других языков, в числе которых Java, C, Python и PHP. Компоненты, созданные с помощью этих языков, могут надежно и точно обмениваться сообщениями, используя AMQP 1.0 в Service Bus. Дополнительные сведения см. в [обзоре AMQP для служебной шины](service-bus-amqp-dotnet.md).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы прочитали общие сведения об использовании служебной шины и AMQP с .NET, воспользуйтесь следующими ссылками, чтобы получить дополнительную информацию.

* [Поддержка AMQP 1.0 в служебной шине](service-bus-amqp-overview.md)
* [Как использовать API JMS со служебной шиной и AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Как использовать очереди служебной шины](service-bus-dotnet-get-started-with-queues.md)

[портал Azure]: https://portal.azure.com



<!--HONumber=Oct16_HO2-->


