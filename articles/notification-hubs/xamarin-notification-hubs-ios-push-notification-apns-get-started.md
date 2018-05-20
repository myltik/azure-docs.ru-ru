---
title: Отправка push-уведомлений в приложения Xamarin.iOS с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.iOS.
services: notification-hubs
keywords: push-уведомления для IOS, push-сообщения, push-уведомления, push-сообщение
documentationcenter: xamarin
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: babd6bff3cec38318cacc0d55394a7563f8e69a4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Руководство по отправке push-уведомлений в приложения Xamarin.iOS с помощью Центров уведомлений Azure
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Обзор
В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение на платформе iOS. Вы создадите пустое приложение Xamarin.iOS, получающее push-уведомления с помощью [Службы push-уведомлений Apple (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html). 

По завершении вы сможете рассылать push-уведомления на все устройства, где запущено ваше приложение, с помощью центра уведомлений. Готовый код доступен в примере [приложения NotificationHubs][GitHub].

В рамках этого руководства создается или обновляется код для выполнения следующих задач: 

> [!div class="checklist"]
> * создание файла запроса на подпись сертификата;
> * Регистрация приложения для работы с push-уведомлениями
> * Создание профиля подготовки для приложения
> * настройка push-уведомлений iOS в центре уведомлений;
> * отправка тестовых push-уведомлений.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
- последняя версия [Xcode][Install Xcode];
- устройство под управлением iOS 10 (или более поздней версии);
- [программе для разработчиков на платформе Apple](https://developer.apple.com/programs/) .
- [Visual Studio для Mac]
  
  > [!NOTE]
  > Из-за требований к конфигурации push-уведомлений для устройств iOS развертывание и тестирование примера приложения необходимо выполнять на физическом устройстве iOS (iPhone или iPad), а не в симуляторе.

Изучение этого руководства важно для понимания всех других руководств, посвященных Центрам уведомлений для приложений Xamarin.iOS.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Настройка push-уведомлений iOS в центре уведомлений
В этом разделе приведены пошаговые инструкции по созданию центра уведомлений и настройке аутентификации с помощью службы APNS, при которой используется раннее созданный вами сертификат push-уведомлений (файл с расширением **P12**). Если вы хотите использовать уже созданный центр уведомлений, перейдите к шагу 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-ios-settings-for-the-notification-hub"></a>Настройка параметров iOS для концентратора уведомлений
1. Выберите **Apple (APN)** в группе **Параметры уведомлений**. 
2. Выберите вкладку **Сертификат**, щелкните значок **файла** и выберите файл **P12**, экспортированный ранее. 
3. Укажите **пароль** для сертификата. 
4. Выберите режим **Песочница**. Используйте режим **рабочей среды**, только если push-уведомления нужно отправлять пользователям, которые приобрели приложение в магазине.

    ![Настройка APNS на портале Azure][6]

    ![Настройка сертификации APNS на портале Azure][7]

Центр уведомлений теперь подключен к службе APNS, и у вас есть строки подключения, с помощью которых вы сможете зарегистрировать свое приложение и отправлять push-уведомления.

## <a name="connect-your-app-to-the-notification-hub"></a>Подключение приложения к центру уведомлений
#### <a name="create-a-new-project"></a>Создание нового проекта
1. В Visual Studio создайте проект iOS и выберите шаблон **Приложение одного представления**, а затем нажмите кнопку **Далее**.
   
     ![Выбор типа приложения в Visual Studio][31]

2. Введите имя приложения и идентификатор организации, затем нажмите кнопку **Далее** и **Создать**.

3. В представлении решения дважды щелкните *Info.plist* и убедитесь, что идентификатор пакета соответствует указанному при создании профиля подготовки. Для этого просмотрите сведения в разделе **Идентификатор**. В разделе **Подписи** укажите учетную запись разработчика в поле **Команда**, установите флажок Automatically manage signing (Автоматическое управление подписями) и убедитесь, что сертификат для подписи и профиль подготовки заданы автоматически.

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

8. В файле **AppDelegate.cs** обновите метод **FinishedLaunching()**, как в приведенном ниже коде:
   
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

## <a name="send-test-push-notifications"></a>Отправка тестовых push-уведомлений
Можно проверить, поступают ли в приложение уведомления, с помощью параметра *Тестовая отправка* на [портале Azure]. Этот параметр позволяет отправить на устройство тестовое push-уведомление.

![Портал Azure — тестовая отправка][30]

Push-уведомления обычно отправляются во внутренней службе, например мобильных приложениях или службе ASP.NET, с помощью совместимой библиотеки. Ели для серверной части библиотека недоступна, для отправки уведомлений также можно напрямую использовать REST API.

## <a name="next-steps"></a>Дополнительная информация
В рамках этого руководства вы отправили широковещательные уведомления на все устройства iOS, зарегистрированные в серверной части. Сведения о том, как отправлять push-уведомления на конкретные устройства iOS, см. в следующем руководстве: 

> [!div class="nextstepaction"]
>[Отправка push-уведомлений на конкретные устройства](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

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
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio для Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[портал Azure]: https://portal.azure.com
