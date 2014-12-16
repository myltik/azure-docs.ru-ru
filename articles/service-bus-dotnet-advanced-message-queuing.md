<properties urlDisplayName="Service Bus AMQP" pageTitle="Использование протокола AMQP 1.0 с .NET API служебной шины - Azure" metaKeywords="" description="Learn how to use Advanced Message Queuing Protodol (AMQP) 1.0 with the Azure .NET Service Bus API." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to use AMQP 1.0 with the Service Bus .NET API" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />






# Использование протокола AMQP 1.0 с API Service Bus .NET

<h2>Введение</h2>

AMQP 1.0 - это эффективный и надежный протокол обмена сообщениями на уровне соединения, который можно использовать для создания надежных кроссплатформенных приложений для обмена сообщениями.

Поддержка AMQP 1.0 в Service Bus означает, что с помощью эффективного двоичного протокола можно на различных платформах использовать возможности очередей и обмена сообщениями с публикацией/подпиской у брокера. Кроме того, можно создавать приложения, содержащие компоненты, созданные с использованием разнообразных языков, платформ и операционных систем.

Это практическое руководство объясняет использование функций сообщений (очереди и разделы о публикации и подписке) шины обслуживания в приложениях .NET, с использованием .NET API служебной шины. В связанном руководстве объясняется, как выполнить те же действия, используя стандартный API службы JMS (Java Message Service). Эти два руководства можно использовать совместно для изучения обмена сообщениями между различными платформами с помощью AMQP 1.0.

<h2>Приступая к работе с шиной обслуживания</h2>

В этом руководстве предполагается, что уже создано пространство имен Service Bus, содержащее очередь с именем "queue1". Если это не так, можно создать пространство имен и в очередь, используя [портал управления Azure](http://manage.windowsazure.com). Для получения дополнительной информации о создании пространства имен и очередей шины обслуживания, см. практическое руководство под названием [Использование очередей шины обслуживания](https://www.windowsazure.com/ru-ru/develop/net/how-to-guides/service-bus-queues/)

<h2>Загрузка пакета Service Bus SDK.</h2>

Поддержка AMQP 1.0 доступна в пакете Service Bus SDK версии 2.1 или более поздней. Последний пакет SDK можно загрузить из NuGet по ссылке: [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

<h2>Создание приложений .NET</h2>

По умолчанию клиентская библиотеке Service Bus .NET взаимодействует со службой Service Bus, используя специализированный протокол на базе SOAP. Чтобы использовать протокол AMQP 1.0 вместо протокола по умолчанию, требуется явно настроить строку подключения Service Bus, как описано в следующем разделе. Помимо этих изменений, код приложения остается в прежнем состоянии при использовании AMQP 1.0.

В данном выпуске присутствуют функции интерфейса API, не поддерживаемые при использовании AMQP. Эти неподдерживаемые функции перечислены ниже в разделе "Неподдерживаемые функции и ограничения". Кроме того, при использовании AMQP отличается значение некоторых дополнительных параметров конфигурации. Эти настройки не используются в данном практическом руководстве, но вы можете получить дополнительную информацию из [Руководства разработчика AMQP 1.0 для Service Bus](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj841071.aspx).

### Настройка с помощью App.config

Рекомендуется использовать файл конфигурации App.config для хранения настроек при работе с приложениями. Для приложений Service Bus можно использовать файл App.config, чтобы хранить параметры настройки строки подключения (**ConnectionString**) Service Bus. Кроме того, в этом примере также сохраняется имя сущности Service Bus, работающей с сообщениями.

Ниже приводится пример файла App.config:

	?xml version="1.0" encoding="utf-8" ?
	<configuration>
	  	<appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
        	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedSecretIssuer=[issuer name];SharedSecretValue=[issuer key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
  		</appSettings>
	</configuration>

### Настройка строки подключения Service Bus

Значение свойства **Microsoft.ServiceBus.ConnectionString** является строкой подключения Service Bus, используемой для настройки подключения к шине обслуживания. Формат является следующим:

	Endpoint=sb://[namespace].servicebus.windows.net;SharedSecretIssuer=[issuer name];SharedSecretValue=[issuer key];TransportType=Amqp

Где [пространство имен], [имя издателя]и [ключ издателя] можно получить на портале управления Azure. Для получения подробной информации, см. [Использование очередей Service Bus][].

При использовании AMQP в конец строки подключения добавляется ";TransportType=Amqp", указывая клиентской библиотеке на использование AMQP 1.0 для подключения к Service Bus.

### Настройка имени объекта

В примере приложения для настройки имени очереди, с которой приложение обменивается сообщениями, используется свойство EntityName в разделе **appSettings** файла конфигурации App.config.

### Простое приложение .NET, использующее очередь Service Bus

В следующем примере выполняется обмен сообщениями (отправка и получение) с очередью Service Bus.

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
	            BrokeredMessage message = 
	                new BrokeredMessage("Test AMQP message from .NET");
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

### Запуск приложения

Запущенное приложение выводит следующие данные:

	> SimpleSenderReceiver.exe
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
	Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
	Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
	Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
	Received message with MessageID = f27f79ec124548c196fd0db8544bca49
	Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
	exit

<h2>Межплатформенный обмен сообщениями между JMS и .NET</h2>

Руководство демонстрирует как отправлять сообщения к шине обслуживания с помощью платформы .NET, а также как получать сообщения посредством .NET. Однако одно из основных преимуществ использования AMQP 1.0 состоит в том, что этот протокол позволяет приложениям, созданным из компонентов, написанных на разных языках, надежно и точно обмениваться сообщениями.

Используя приведенный выше пример приложения .NET и аналогичное приложение Java, взятое из связанного руководства [Использование интерфейса API службы сообщений Java с Service Bus и AMQP 1.0](http://aka.ms/ll1fm3), можно организовать обмен сообщениями между .NET и Java. 

Дополнительные сведения о межплатформенном обмене сообщениями с помощью Service Bus и AMQP 1.0 см. в статье [Руководство разработчика AMQP 1.0 для Service Bus](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj841071.aspx).

### Из JMS в .NET

Для демонстрации отправки сообщении из JMS в .NET выполните следующее:

* Запустите пример приложения .NET без параметров командной строки.
*  Запустите пример приложения Java с параметром командной строки "sendonly". В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* В консоли приложения Java несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением .NET.

#### Вывод приложения JMS

	> java SimpleSenderReceiver sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with JMSMessageID = ID:4364096528752411591
	Sent message with JMSMessageID = ID:459252991689389983
	Sent message with JMSMessageID = ID:1565011046230456854
	exit

#### Вывод приложения .NET

	> SimpleSenderReceiver.exe	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with MessageID = 4364096528752411591
	Received message with MessageID = 459252991689389983
	Received message with MessageID = 1565011046230456854
	exit

### Из .NET в JMS

Для демонстрации отправки сообщений из .NET в JMS выполните следующее:

*  Запустите пример приложения .NET с параметром командной строки "sendonly". В этом режиме приложение не будет получать сообщения из очереди, оно будет только отправлять сообщения.
* Запустите пример приложения Java без параметров командной строки.
* В консоли приложения .NET несколько раз нажмите клавишу **ВВОД**, что приведет к отправке сообщений.
* Эти сообщения принимаются приложением Java.

#### Вывод приложения .NET

	> SimpleSenderReceiver.exe sendonly
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
	Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
	Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
	exit


#### Вывод приложения JMS

	> java SimpleSenderReceiver	
	Press [enter] to send a message. Type 'exit' + [enter] to quit.
	Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
	Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
	Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
	exit

<h2>Неподдерживаемые возможности и ограничения</h2>

Следующие функции API-интерфейса Service Bus .NET при использовании AMQP не поддерживаются:

* Транзакции
* Отправка через место назначения передачи
* Получение по порядковому номеру сообщения
* Обзор сообщений и сеансов
* Состояние сеанса
* API-интерфейсы на основе пакетов
* Масштабируемый прием
* Изменение правил подписки во время выполнения
* Возобновление блокировки сеанса
* Ряд незначительных отличий в поведении

Для получения дополнительной информации, см. [Руководство разработчика AMQP 1.0 для Service Bus](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj841071.aspx). В данном разделе приводится подробный список неподдерживаемых интерфейсов API.

<h2>Сводка</h2>

В этом практическом руководстве показывается, как получать доступ к функциям обмена сообщениями при посредстве Service Bus (очереди и разделы публикации/подписки) из приложений .NET с помощью AMQP 1.0 и API Service Bus .NET.

Протокол AMQP 1.0 Service Bus можно также использовать из других языков, в числе которых Java, C, Python и PHP. Компоненты, созданные с помощью этих языков, могут надежно и точно обмениваться сообщениями, используя поддержку AMQP 1.0 в Service Bus. Дополнительные сведения см. в [Руководстве разработчика Service Bus AMQP 1.0](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj841071.aspx).

<h2>Дополнительные сведения</h2>

* [Поддержка AMQP 1.0 в Azure Service Bus](http://aka.ms/pgr3dp)
* [Использование API JMS с Service Bus и AMQP 1.0](http://aka.ms/ll1fm3)
* [Руководство разработчика AMQP 1.0 для Service Bus](http://msdn.microsoft.com/ru-ru/library/windowsazure/jj841071.aspx)
* [Использование очередей Service Bus](http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/service-bus-queues/)

[Использование очередей Service Bus]: http://www.windowsazure.com/ru-ru/develop/net/how-to-guides/service-bus-queues/
