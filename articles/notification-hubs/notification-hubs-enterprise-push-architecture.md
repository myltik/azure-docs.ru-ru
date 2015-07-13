<properties 
	pageTitle="Центры уведомлений — корпоративная архитектура системы push-уведомлений" 
	description="Руководство по использованию центров уведомлений Azure в корпоративной среде" 
	services="notification-hubs" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="wesmc"/>

# Руководство по архитектуре push-уведомлений

Сегодня предприятия постепенно переходят к созданию мобильных приложений для конечных пользователей (внешних) или сотрудников (внутренних). У них есть серверные системы, размещенные на мэйнфреймах, или бизнес-приложения, которые нужно интегрировать в архитектуру приложений для мобильных устройств. В этом руководстве рассказывается об оптимальном подходе к такой интеграции и излагаются рекомендации относительно возможных решений для распространенных сценариев.

Одно из наиболее распространенных требований — возможность отправки push-уведомлений пользователям с помощью мобильных приложений при определенных событиях в серверной системе. Например, клиент банка, у которого есть банковское приложение на iPhone, хочет получать уведомления при списании со счет сумм, превышающих определенное значение, а сотрудник из финансового отдела, у которого есть приложение для утверждения бюджета на устройстве Windows Phone, хочет получать по интрасети уведомления при получении запроса на утверждение.
 
Обслуживание банковского счета и обработка утверждений, как правило, осуществляются в серверной системе, которая должна инициировать отправку push-уведомлений пользователю. Таких серверных систем может быть несколько, а для реализации push-уведомлений, отправка которых инициируется наступлением определенных событий, все они должны быть созданы на основе одной и той же логики. Сложность в том, как интегрировать несколько серверных систем с одной системой push-уведомлений, где конечные пользователи могут подписываться на различные уведомления и даже использовать различные мобильные приложения, например в случае интрасети, где одно мобильное приложение может получать уведомления из нескольких таких серверных систем. Серверным системам неизвестна или не должна быть известна семантика или технология системы отправки push-уведомлений, поэтому в таких случаях традиционным решением было внедрение компонента, который опрашивает серверные системы, чтобы определить, не произошло ли то или иное событие, и отвечает за отправку push-уведомлений клиенту. Но решение, которое мы обсудим здесь, еще лучше. В нем используется Azure Service Bus — модель «раздел-подписка», которая позволяет снизить сложность, обеспечив при этом масштабируемое решение.

Ниже описана общая архитектура решения. Изложенная здесь информация касается общего случая нескольких мобильных приложений, однако она в равной мере применима в случае, если такое приложение только одно.

## Архитектура

![][1]

Основной элемент архитектуры на этой схеме — это служебная шина Azure, которая обеспечивает модель программирования «разделы и подписки» (подробнее об этом: [Использование разделов и подписок Service Bus]). Получатель (в данном случае это серверная часть мобильной службы, обычно — [мобильная служба Azure], которая будет инициировать отправку push-уведомлений для мобильных приложений) не получает сообщения напрямую от серверных систем. Эти уведомления поступают к нему с промежуточного уровня абстракции, предоставляемого [служебной шиной Azure], что позволяет серверной части мобильной службы получать сообщения из одной или нескольких серверных систем. Для каждой серверной системы должен быть создан раздел Service Bus. Примерами таких серверных систем могут служить системы бухгалтерии, отдела кадров и финансового отдела. По сути, это те интересующие пользователей «разделы», которые инициируют отправку сообщений в виде push-уведомлений. Серверные системы будут отправлять сообщения в эти разделы. Серверная часть мобильной службы может подписаться на один или несколько таких разделов, создав подписку Service Bus. Это даст серверной части мобильной службы возможность получать уведомления от соответствующей серверной системы. Серверная часть мобильной службы постоянно ожидает поступления сообщений по своим подпискам, и как только они поступают, сразу же пересылает их в виде уведомлений в свой центр уведомлений. В конечном итоге центры уведомлений доставляют сообщение в мобильное приложение. Итак, у нас есть следующие ключевые компоненты:

1. Серверные системы (бизнес-системы или устаревшие системы)
	- Создает раздел Service Bus
	- Отправляет сообщение
2. Серверная часть мобильной службы
	- Создает подписку на службу
	- Получает сообщение (из серверной системы)
	- Отправляет уведомление клиентам (через центр уведомлений Azure)
3. Мобильное приложение
	- Получает и отображает уведомление
		
###Преимущества:

1. Разделение на получателя (мобильное приложение или служба через центр уведомлений) и отправителя (серверные системы) позволяет интегрировать дополнительные серверные системы с минимальными изменениями.
2. Упрощается ситуация, когда необходимо, чтобы уведомления о событиях в одной или нескольких серверных системах получали несколько мобильных приложений.  

## Пример:

###Предварительные требования
Чтобы ознакомиться с основными понятиями и общими процедурами создания и настройки, необходимо пройти следующие учебники:

1. [Использование разделов и подписок Service Bus] — в этом руководстве подробно описывается работа с разделами и подписками служебной шины, создание пространства имен для хранения разделов и подписок, а также отправка и получение сообщений от них. 
2. [Учебник по центрам уведомлений для Windows Universal] — в этом учебнике рассказывается о том, как настроить приложение Магазина Windows и использовать центры уведомлений для регистрации и последующего получения уведомлений. 

###Пример кода

Полный пример кода доступен в [коллекции примеров центра уведомлений]. Он состоит из трех компонентов:

1. **EnterprisePushBackendSystem**
	
	а. Этот проект использует пакет NuGet *WindowsAzure.ServiceBus* и основан на материале, изложенном в статье [Использование разделов и подписок Service Bus].

	b. Это простое консольное приложение на C# для моделирования бизнес-систем, которое инициирует доставку сообщений мобильному приложению.
	
		static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }
	
	c. `CreateTopic` используется для создания раздела служебной шины, в который будут отправляться сообщения.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

	d. `SendMessage` используется для отправки сообщений в этот раздел служебной шины. В данном примере мы будем периодически отправлять набор случайных сообщений в этот раздел. Как правило эту функцию будет выполнять серверная система, которая будет отправлять сообщения при наступлении события.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages = 
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

	а. Этот проект использует пакеты NuGet *WindowsAzure.ServiceBus* и *Microsoft.Web.WebJobs.Publish* и основан на материале, изложенном в статье [Использование разделов и подписок Service Bus].

	b. Это еще одно консольное приложение на C#, которое мы будем запускать в качестве [веб-задания Azure], так как оно должно выполняться непрерывно для прослушивания сообщений из бизнес-систем и серверных систем. Оно будет входить в состав серверной части мобильной службы.

	    static void Main(string[] args)
	    {
	        string connectionString =
	                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
	
	        // Create the subscription which will receive messages
	        CreateSubscription(connectionString);   
	
	        // Receive message
	        ReceiveMessageAndSendNotification(connectionString);
	    }

	c. `CreateSubscription` используется для создания подписки служебной шины для раздела, в который серверная система будет отправлять сообщения. В зависимости от бизнес-сценария этот компонент будет создавать одну или несколько подписок на соответствующие разделы (например, некоторые получают сообщения из системы отдела кадров, другие — из системы финансового отдела и т. д.).

	    static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

	г) Для чтения сообщения из раздела с помощью подписки используется ReceiveMessageAndSendNotification, и если считывание заканчивается успешно, создается уведомление (в этом примере сценария — собственное всплывающее уведомление Windows), которое отправляется мобильному приложению с помощью центров уведомлений Azure.

		static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
            
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription 
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            } 
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

	д. Чтобы опубликовать этот процесс в виде **веб-задания**, щелкните правой кнопкой мыши решение в Visual Studio и выберите **Опубликовать как веб-задание**.

	![][2]

	Е. Выберите профиль публикации и создайте новый веб-сайт Azure для размещения веб-задания (если он еще не существует), а когда веб-сайт будет создан, щелкните **Опубликовать**.
	
	![][3]

	g. Настройте «непрерывное выполнение» задания, чтобы при входе на портал управления Azure отображалось примерно следующее:

	![][4]


3. **EnterprisePushMobileApp**

	а. Это приложение Магазина Windows, которое будет получать всплывающие уведомления из задания WebJob, выполняющегося в составе серверной части мобильной службы, и отображать это уведомление. Оно основано на материале [учебника по центрам уведомлений для Windows Universal].
	
	b. Убедитесь, что в приложении включено получение всплывающих уведомлений.

	c. Убедитесь, что при запуске приложения вызывается следующий код регистрации центров уведомлений (после замены *HubName* и *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### Запуск примера

1. Убедитесь, что задание WebJob успешно запущено и для него запланировано «непрерывное выполнение». 
2. Запустите **EnterprisePushMobileApp**, чтобы запустить приложение Магазина Windows. 
3. Запустите консольное приложение **EnterprisePushBackendSystem**, которое будет имитировать серверную часть бизнес-системы и начнет отправлять сообщения. Вы должны увидеть примерно такие всплывающие уведомления: 

	![][5]

4. Первоначально сообщения были отправлены в разделы Service Bus, которые отслеживаются по подпискам Service Bus в задании WebJob. При получении сообщения было создано уведомление, которое затем было отправлено в мобильное приложение. Чтобы убедиться в правильности выполнения обработки, можно просмотреть журналы задания WebJob, перейдя по ссылке «Журналы» на портале управления Azure для соответствующего задания WebJob:

	![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[коллекции примеров центра уведомлений]: https://github.com/Azure/azure-notificationhubs-samples
[мобильная служба Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[служебной шиной Azure]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Использование разделов и подписок Service Bus]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[веб-задания Azure]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Учебник по центрам уведомлений для Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[учебника по центрам уведомлений для Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
 

<!---HONumber=July15_HO1-->