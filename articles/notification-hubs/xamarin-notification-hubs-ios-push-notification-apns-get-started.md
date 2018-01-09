---
title: "Начало работы с Центрами уведомлений Azure для приложений Xamarin.iOS | Документация Майкрософт"
description: "Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.iOS."
services: notification-hubs
keywords: "push-уведомления для IOS, push-сообщения, push-уведомления, push-сообщение"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Начало работы с Центрами уведомлений Azure для приложений Xamarin.iOS
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. Вам предстоит создать пустое приложение Xamarin.iOS, получающее push-уведомления с помощью [службы push-уведомлений Apple (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В этом руководстве описывается простой сценарий рассылки push-сообщений с использованием центров уведомлений.

## <a name="prerequisites"></a>предварительным требованиям
Для работы с данным учебником требуется следующее:

* последняя версия [Xcode][Install Xcode];
* устройство под управлением iOS 10 (или более поздней версии);
* [программе для разработчиков на платформе Apple](https://developer.apple.com/programs/) .
* [Visual Studio для Mac].
  
  > [!NOTE]
  > Из-за требований к конфигурации push-уведомлений для устройств iOS развертывание и тестирование примера приложения необходимо выполнять на физическом устройстве iOS (iPhone или iPad), а не в симуляторе.
  > 
  > 

Изучение этого руководства важно для понимания всех других руководств, посвященных Центрам уведомлений для приложений Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Настройка push-уведомлений iOS в центре уведомлений 
В этом разделе приведены пошаговые инструкции по созданию центра уведомлений и настройке аутентификации с помощью службы APNS, при которой используется раннее созданный вами сертификат push-уведомлений (файл с расширением **P12**). Если вы хотите использовать уже созданный центр уведомлений, перейдите к шагу 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Нажмите кнопку <b>Notification Services</b> и выберите <b>Apple (APNS)</b>. Выберите <b>Сертификат</b>, щелкните значок файла и выберите файл <b>P12</b>, экспортированный ранее. Кроме того, необходимо указать правильный пароль.</p>

<p>Выберите режим <b>песочницы</b>, так как это необходимо для разработки. Используйте <b>рабочую среду</b>, только если push-уведомления нужно отправлять пользователям, выполнившим покупку приложения в магазине.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Настройка APNS на портале Azure][6]

&emsp;&emsp;&emsp;&emsp;![Настройка сертификации APNS на портале Azure][7]

Центр уведомлений теперь подключен к службе APNS, и у вас есть строки подключения, с помощью которых вы сможете зарегистрировать свое приложение и отправлять push-уведомления.

## <a name="connect-your-app-to-the-notification-hub"></a>Подключение приложения к центру уведомлений
#### <a name="create-a-new-project"></a>Создание нового проекта
1. В Visual Studio создайте проект iOS и выберите шаблон **Приложение одного представления**, а затем нажмите кнопку **Далее**.
   
     ![Выбор типа приложения в Visual Studio][31]

2. Введите имя приложения и идентификатор организации, затем нажмите кнопку **Далее** и **Создать**.

3. В представлении решения дважды щелкните *Into.plist* и убедитесь, что идентификатор пакета соответствует указанному при создании профиля подготовки. Для этого просмотрите сведения в разделе **Идентификатор**. В разделе **Подписи** укажите учетную запись разработчика в поле **Команда**, установите флажок Automatically manage signing (Автоматическое управление подписями) и убедитесь, что сертификат для подписи и профиль подготовки заданы автоматически.

    ![Настройка приложения iOS в Visual Studio][32]

4. Добавьте пакет службы сообщений Azure. В представлении решения щелкните проект правой кнопкой мыши и последовательно выберите **Добавить** > **Add NuGet Packages** (Добавить пакеты NuGet). Выполните поиск пакета **Xamarin.Azure.NotificationHubs.iOS** и добавьте его в проект.

5. Добавьте новый файл в класс и присвойте ему имя **Constants.cs**. Добавьте указанные далее переменные и замените буквенные заполнители *именем центра* и значением *DefaultListenSharedAccessSignature*, записанным ранее.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. В файле **AppDelegate.cs**добавьте следующий оператор using:
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. Объявите экземпляр **SBNotificationHub**:
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. В файле **AppDelegate.cs** обновите метод **FinishedLaunching()**, как указано ниже.
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. Переопределите метод **RegisteredForRemoteNotifications()** в файле **AppDelegate.cs**.
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. Переопределите метод **ReceivedRemoteNotification()** в файле **AppDelegate.cs**.
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. Создайте следующий метод **ProcessNotification()** в файле **AppDelegate.cs**:
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > Вы можете переопределить метод **FailedToRegisterForRemoteNotifications()** для обработки различных ситуаций, таких как отсутствие сетевого подключения. Это особенно важно в тех случаях, когда пользователь может запустить приложение в автономном режиме (например, при использовании режима "В самолете") и вам нужно обрабатывать отправку push-сообщений в соответствии с требованиями вашего приложения.
  

12. Запустите приложение на устройстве.

## <a name="sending-test-push-notifications"></a>Отправка тестовых push-уведомлений
Можно проверить, поступают ли в приложение уведомления, с помощью параметра *Тестовая отправка* на [портале Azure]. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка][30]

Push-уведомления обычно отправляются во внутренней службе, например мобильных приложениях или службе ASP.NET, с помощью совместимой библиотеки. Ели для серверной части библиотека недоступна, для отправки уведомлений также можно напрямую использовать REST API.

Рекомендуем ознакомиться с дальнейшими инструкциями по отправке уведомлений из серверной части ASP.NET в руководстве по [отправке уведомлений с помощью Центров уведомлений](notification-hubs-aspnet-backend-ios-apple-apns-notification.md). Можно использовать следующие способы отправки уведомлений.

Ниже приведен список других учебников, касающихся отправки уведомлений:
* Интерфейс REST. [Интерфейс REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) поддерживает работу с push-уведомлениями на любой серверной платформе.
* **Пакет SDK .NET для Центров уведомлений Microsoft Azure**. В диспетчере пакетов NuGet для Visual Studio выполните команду [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* Node.js. [Отправка push-уведомлений с помощью Центров уведомлений Azure и Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* Java или PHP**. Примеры отправки push-уведомлений с помощью REST API см. в статьях об использовании центров уведомлений из [Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md).

## <a name="next-steps"></a>Дополнительная информация
В этом простом примере мы отправили push-уведомления на все устройства iOS. Инструкции по их отправке определенным целевым пользователям, см. в руководстве [Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]. Если необходимо разделить пользователей по группам интересов, см. раздел [Использование концентраторов уведомлений для передачи экстренных новостей]. Дополнительные сведения об использовании центров уведомлений см. в статьях [Общие сведения о концентраторах уведомлений] и [Notification Hubs How-To for iOS] (Инструкции по использованию центров уведомлений для iOS).

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Общие сведения о концентраторах уведомлений]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio для Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Уведомление пользователей посредством концентраторов уведомлений с помощью серверной части .NET]: /manage/services/notification-hubs/notify-users-aspnet
[Использование концентраторов уведомлений для передачи экстренных новостей]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[портале Azure]: https://portal.azure.com
