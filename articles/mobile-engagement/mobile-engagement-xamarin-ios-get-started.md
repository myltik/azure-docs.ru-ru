---
title: Начало работы с Azure Mobile Engagement для Xamarin.iOS
description: Узнайте, как использовать Azure Mobile Engagement с аналитическими функциями и push-уведомлениями для приложений Xamarin.iOS.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo

---
# <a name="get-started-with-azure-mobile-engagement-for-xamarin.ios-apps"></a>Начало работы с Azure Mobile Engagement для приложений Xamarin.iOS
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как использовать Azure Mobile Engagement для получения общих сведений об использовании приложения и для отправки push-уведомлений сегментированным пользователям в приложении Xamarin.iOS.
В этом руководстве вы создадите пустое приложение Xamarin.iOS, которое собирает основные данные и получает push-уведомления с помощью службы push-уведомлений Apple (APNs).

Для работы с данным учебником требуется следующее:

* [Xamarin Studio](http://xamarin.com/studio). Вы также можете использовать Visual Studio с расширением Xamarin, но в этом руководстве используется Xamarin Studio. Инструкции см. в руководстве по [установке и настройке Visual Studio и Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
* [пакет Xamarin SDK для Mobile Engagement](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a name="<a-id="setup-azme"></a>setup-mobile-engagement-for-your-ios-app"></a><a id="setup-azme"></a>Настройка Mobile Engagement для вашего приложения iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="<a-id="connecting-app"></a>connect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Подключение приложения к серверной части Mobile Engagement
В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений.

Мы создадим базовое приложение при помощи Xamarin, чтобы продемонстрировать интеграцию.

### <a name="create-a-new-xamarin.ios-project"></a>Создание нового проекта Xamarin.iOS
1. Запустите Xamarin Studio. Последовательно выберите элементы **File** -> **New** -> **Solution** (Файл — Создать — Решение). 
   
    ![][1]
2. Щелкните **Single View App** (Приложение с одним представлением), выберите язык **C#** и нажмите кнопку **Next** (Далее).
   
    ![][2]
3. Заполните поля **App Name** (Имя приложения) и **Organization Identifier** (Идентификатор организации), а затем нажмите кнопку **Next** (Далее). 
   
    ![][3]
   
   > [!IMPORTANT]
   > Убедитесь, что в профиле публикации, который вы впоследствии будете использовать для развертывания приложения iOS, используется идентификатор приложения, точно совпадающий с имеющимся идентификатором пакета. 
   > 
   > 
4. Если требуется, обновите значения в полях **Project Name** (Имя проекта), **Solution Name** (Имя решения) и **Location** (Расположение), а затем нажмите кнопку **Create** (Создать).
   
    ![][4]

Xamarin Studio создаст демоверсию приложения, в которое мы интегрируем Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Подключение приложения к серверной части Mobile Engagement
1. В окне решения правой кнопкой мыши щелкните папку **Packages** (Пакеты) и выберите пункт **Add Packages...** (Добавить пакеты).
   
    ![][5]
2. Найдите пакет **Microsoft Azure Mobile Engagement Xamarin SDK** и добавьте его в свое решение.  
   
    ![][6]
3. В файле **AppDelegate.cs** добавьте следующую инструкцию using:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. В метод **FinishedLaunching** добавьте следующую команду, чтобы инициализировать подключение к внутреннему серверу Mobile Engagement. Обязательно добавьте **строку подключения**. В этом коде также используется фиктивный элемент **NotificationIcon** , добавленный пакетом Mobile Engagement SDK, который вы, возможно, захотите заменить. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a name="<a-id="monitor"></a>enabling-real-time-monitoring"></a><a id="monitor"></a>Включение мониторинга в реальном времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран на внутренний сервер Mobile Engagement.

1. В файле **ViewController.cs** добавьте следующую инструкцию using:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Замените класс, от которого наследуется `ViewController`, указав `EngagementViewController` вместо `UIViewController`. 

## <a name="<a-id="monitor"></a>connect-app-with-real-time-monitoring"></a><a id="monitor"></a>Подключение приложения с возможностью его отслеживания в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="<a-id="integrate-push"></a>enable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении
Mobile Engagement позволяет взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Mobile Engagement этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### <a name="modify-your-application-delegate"></a>Изменение делегата приложения
1. В файле **AppDelegate.cs** добавьте следующую инструкцию using:
   
        using System; 
2. Теперь внутри метода `FinishedLaunching` после `EngagementAgent.init(...)` добавьте следующий код для регистрации push-сообщений.
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Наконец обновите или добавьте следующие методы:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. В файле **Info.plist** в решении убедитесь, что **идентификатор пакета** соответствует **идентификатору приложения**, указанному в профиле подготовки в центре разработки Apple. 
   
    ![][7]
5. В том же файле **Info.plist** установите флажки **Enable Background Modes** (Разрешить фоновые режимы) и **Remote Notifications** (Удаленные уведомления). 
   
    ![][8]
6. Запустите приложение на устройстве, связанном с этим профилем публикации. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png



<!--HONumber=Oct16_HO2-->


