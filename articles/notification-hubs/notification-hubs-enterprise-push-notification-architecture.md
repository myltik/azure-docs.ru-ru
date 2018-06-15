---
title: Центры уведомлений — корпоративная архитектура системы push-уведомлений
description: Руководство по использованию центров уведомлений Azure в корпоративной среде
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d7066b58330d35e5dba66cfe6ed5cfaddff4b68a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778068"
---
# <a name="enterprise-push-architectural-guidance"></a>Руководство по архитектуре push-уведомлений
Сегодня предприятия постепенно переходят к созданию мобильных приложений для конечных пользователей (внешних) или сотрудников (внутренних). У них есть серверные системы, размещенные на мэйнфреймах, или бизнес-приложения, которые нужно интегрировать в архитектуру приложений для мобильных устройств. В этом руководстве рассказывается об оптимальном подходе к такой интеграции и излагаются рекомендации относительно возможных решений для распространенных сценариев.

Одно из наиболее распространенных требований — возможность отправки push-уведомлений пользователям с помощью мобильных приложений при определенных событиях в серверной системе. Например, клиент банка, у которого есть банковское приложение на iPhone, хочет получать уведомления при списании со счета сумм, превышающих определенное значение, а сотрудник из финансового отдела, у которого есть приложение для утверждения бюджета на устройстве Windows Phone, хочет получать по интрасети уведомления при получении запроса на утверждение.

Обслуживание банковского счета и обработка утверждений, как правило, осуществляются в серверной системе, которая должна инициировать отправку push-уведомлений пользователю. Таких серверных систем может быть несколько, а для реализации push-уведомлений, отправка которых инициируется наступлением определенных событий, все они должны быть созданы на основе одной и той же логики. Сложность в том, как интегрировать несколько серверных систем с одной системой push-уведомлений, где конечные пользователи могут подписываться на различные уведомления и даже использовать различные мобильные приложения. Например, в случае интрасети, где одно мобильное приложение может получать уведомления из нескольких таких серверных систем. Серверным системам неизвестна или не должна быть известна семантика или технология системы отправки push-уведомлений, поэтому в таких случаях традиционным решением было внедрение компонента, который опрашивает серверные системы, чтобы определить, не произошло ли то или иное событие, и отвечает за отправку push-уведомлений клиенту.

Лучшим решением является использование служебной шины Azure — модели разделов и подписок, которая обеспечивает простоту и масштабируемость.

Ниже описана общая архитектура решения. Изложенная здесь информация касается общего случая нескольких мобильных приложений, однако она в равной мере применима в случае, если такое приложение только одно.

## <a name="architecture"></a>Архитектура
![][1]

Основной элемент на этой схеме архитектуры — это служебная шина Azure, которая предоставляет модель программирования разделов и подписок (подробнее об этом написано в статье [Как использовать разделы и подписки служебной шины]). Получатель (в данном случае это серверная часть мобильной службы, обычно — [мобильная служба Azure], которая будет инициировать отправку push-уведомлений для мобильных приложений) не получает сообщения напрямую от серверных систем. Эти уведомления поступают к нему с промежуточного уровня абстракции, предоставляемого служебной шиной Azure, что позволяет серверной части мобильной службы получать сообщения из одной или нескольких серверных систем. Раздел "Служебная шина" необходимо создать для каждой из серверных систем, например "Бухгалтерия", "Отдел кадров" и "Финансы", по сути являющиеся "разделами", которые инициируют отправку сообщений в виде push-уведомлений. Серверные системы отправляют сообщения в эти разделы. Мобильный внутренний сервер может подписаться на один или несколько таких разделов, создав подписку на служебную шину. Это дает серверной части мобильной службы возможность получать уведомления от соответствующей серверной системы. Этот сервер продолжает прослушивать сообщения по своим подпискам, и как только сообщение поступает, он отправляет его в виде уведомления в свой центр уведомлений. В конечном итоге центры уведомлений доставляют сообщение в мобильное приложение. Ниже приведен список основных компонентов.

1. Серверные системы (бизнес-системы или устаревшие системы)
   * Создает раздел Service Bus
   * Отправляет сообщение
2. Серверная часть мобильной службы
   * Создает подписку на службу
   * Получает сообщение (из серверной системы)
   * Отправляет уведомление клиентам (через центр уведомлений Azure)
3. Мобильное приложение
   * Получает и отображает уведомление

### <a name="benefits"></a>Преимущества:
1. Разделение на получателя (мобильное приложение или служба через центр уведомлений) и отправителя (серверные системы) позволяет интегрировать дополнительные серверные системы с минимальными изменениями.
2. Упрощается ситуация, когда необходимо, чтобы уведомления о событиях в одной или нескольких серверных системах получало несколько мобильных приложений.  

## <a name="sample"></a>Пример:
### <a name="prerequisites"></a>предварительным требованиям
Чтобы ознакомиться с основными понятиями и общими процедурами создания и настройки, необходимо изучить следующие руководства.

1. [Как использовать разделы и подписки служебной шины]. В этом руководстве подробно описывается работа с разделами и подписками служебной шины, создание пространства имен для хранения разделов и подписок, отправка им сообщений и получение сообщений от них.
2. [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows]. В этом руководстве рассказывается о том, как настроить приложение Магазина Windows и использовать центры уведомлений для регистрации и последующего получения уведомлений.

### <a name="sample-code"></a>Пример кода
Полный пример кода доступен в [коллекции примеров центра уведомлений]. Он состоит из трех компонентов:

1. **EnterprisePushBackendSystem**
   
    a. Этот проект использует пакет NuGet *WindowsAzure.ServiceBus* и основан на материале, изложенном в статье [Как использовать разделы и подписки служебной шины].
   
    Б. Это простое консольное приложение на C# для моделирования бизнес-систем, которое инициирует доставку сообщений мобильному приложению.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` используется для создания раздела служебной шины.
   
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
   
    d. `SendMessage` используется для отправки сообщений в этот раздел служебной шины. Данный код просто периодически отправляет набор случайных сообщений в этот раздел. Как правило, эту функцию выполняет серверная система, которая отправляет сообщения при наступлении события.
   
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
   
    a. Этот проект использует пакеты NuGet *WindowsAzure.ServiceBus* и *Microsoft.Web.WebJobs.Publish* и основан на материале, изложенном в статье [Как использовать разделы и подписки служебной шины].
   
    Б. Приведенное ниже консольное приложение запускается в качестве [веб-задания Azure], так как оно должно выполняться непрерывно для ожидания передачи сообщений из бизнес-систем и серверных систем. Это приложение входит в состав серверной части мобильного приложения.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription that receives messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` используется для создания подписки служебной шины для раздела, в который серверная система отправляет сообщения. В зависимости от бизнес-сценария этот компонент создает одну или несколько подписок на соответствующие разделы (например, одни будут получать сообщения из системы "Отдел кадров", другие — из системы "Финансы" и т. д.)
   
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
   
    d. ReceiveMessageAndSendNotification используется для чтения сообщения из раздела с использованием подписки. Если операция считывания выполнена успешно, создается уведомление (в этом примере сценария — собственное всплывающее уведомление Windows), которое необходимо отправить в мобильное приложение с помощью Центров уведомлений Azure.
   
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
   
    д. Чтобы опубликовать это приложение в виде **веб-задания**, щелкните правой кнопкой мыши решение в Visual Studio и выберите **Publish as WebJob** (Опубликовать как веб-задание).
   
    ![][2]
   
    f. Выберите профиль публикации и создайте новый веб-сайт Azure для размещения веб-задания (если он еще не существует), а когда веб-сайт будет создан, щелкните **Опубликовать**.
   
    ![][3]
   
    ж. Настройте непрерывное выполнение задания, чтобы при входе на [портале Azure] отображалось примерно следующее:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Это приложение Магазина Windows, которое будет получать всплывающие уведомления из задания WebJob, выполняющегося в составе серверной части мобильной службы, и отображать это уведомление. Оно основано на материалах [Начало работы с Центрами уведомлений для приложений универсальной платформы Windows].  
   
    Б. Убедитесь, что в приложении включено получение всплывающих уведомлений.
   
    c. Убедитесь, что при запуске приложения вызывается следующий код регистрации Центров уведомлений (после замены *HubName* и *DefaultListenSharedAccessSignature*:
   
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

### <a name="running-sample"></a>Запуск примера
1. Убедитесь, что веб-задание успешно запущено и для него запланировано непрерывное выполнение.
2. Запустите **EnterprisePushMobileApp**, чтобы запустить приложение Магазина Windows.
3. Запустите консольное приложение **EnterprisePushBackendSystem**, которое имитирует серверную часть бизнес-системы. Оно начнет отправлять сообщения. Вы должны увидеть всплывающие уведомления, как на рисунке ниже.
   
    ![][5]
4. Первоначально сообщения были отправлены в разделы служебной шины, которые отслеживаются по подпискам служебной шины в веб-задании. При получении сообщения было создано уведомление, которое затем было отправлено в мобильное приложение. Чтобы убедиться в правильности выполнения обработки, можно просмотреть журналы веб-заданий, перейдя по ссылке "Журналы" на [портале Azure] для соответствующего веб-задания:
   
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
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Как использовать разделы и подписки служебной шины]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[веб-задания Azure]: ../app-service/web-sites-create-web-jobs.md
[Начало работы с Центрами уведомлений для приложений универсальной платформы Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[портале Azure]: https://portal.azure.com/
