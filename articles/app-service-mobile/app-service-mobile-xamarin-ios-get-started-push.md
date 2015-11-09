<properties 
	pageTitle="Добавление push-уведомлений в приложение Xamarin.iOS с помощью службы приложений Azure" 
	description="Использование службы приложений Azure для отправки push-уведомлений в приложение Xamarin.iOS" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="08/22/2015" 
	ms.author="wesmc"/>

# Добавление push-уведомлений в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

В этом учебнике описывается добавление push-уведомлений в проект учебника по [быстрому запуску Xamarin.iOS], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал учебника по [быстрому запуску Xamarin.iOS], который необходимо пройти в первую очередь. Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), поэтому необходимо использовать физическое устройство под управлением iOS. Вам также необходимо зарегистрироваться для [участия в программе разработки решений для Apple](https://developer.apple.com/programs/ios/).

##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас еще нет учетной записи, зарегистрируйтесь для использования пробной версии Azure и получите до 10 бесплатных мобильных приложений. Вы сможете использовать их даже после окончания срока действия пробной версии. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* Компьютер Mac с установленным программным обеспечением [Xamarin Studio] и [Xcode] 4.4 или более поздней версии. Учтите, что проще всего запустить приложение Xamarin.iOS на компьютере Mac с помощью Xamarin Studio. Запустить приложение Xamarin.iOS можно в Visual Studio на компьютере Windows, если хотите, но это немного сложнее, так как необходимо подключиться к компьютеру Mac с доступом к сети. Если вам это интересно, см. раздел [Установка Xamarin.iOS в Windows].

* Физическое устройство iOS.

* Изучение [краткого руководства](../app-service-mobile-xamarin-ios-get-started.md).

## <a id="register"></a>Регистрация приложения для работы с push-уведомлениями

[AZURE.INCLUDE [Включение push-уведомлений Apple](../../includes/enable-apple-push-notifications.md)]

## Настройка Azure для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a id="update-server"></a>Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="publish-the-service"></a>Развертывание серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

## <a name="configure-app"></a>Настройка проекта Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push"></a>Добавление push-уведомлений в приложение

1. В **QSTodoService** добавьте следующее свойство, чтобы **AppDelegate** мог получить мобильный клиент:
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Добавьте следующий оператор `using` в верхнюю часть файла **AppDelegate.cs**:

        using Microsoft.WindowsAzure.MobileServices;

2. В **AppDelegate** переопределите событие **FinishedLaunching**:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. В том же файле переопределите событие **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken);
        }

4. Затем переопределите событие **DidReceivedRemoteNotification**:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## <a name="test"></a>Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.
	
	> [AZURE.NOTE]Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите задачу, а затем щелкните значок «Плюс» (**+**).

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

4. Повторите шаг 2 и сразу закройте приложение, затем убедитесь, что уведомление отображается.

Вы успешно завершили ознакомление с данным учебником.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Установка Xamarin.iOS в Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[Azure Management Portal]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=Nov15_HO1-->