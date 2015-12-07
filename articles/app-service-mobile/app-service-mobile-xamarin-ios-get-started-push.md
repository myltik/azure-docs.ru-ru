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
	ms.date="11/23/2015" 
	ms.author="wesmc"/>

# Добавление push-уведомлений в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Обзор

Этот учебник использует материал [руководства по быстрому запуску Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md), который необходимо пройти в первую очередь. В нем описывается добавление push-уведомлений в проект учебника по [быстрому запуску Xamarin.iOS], чтобы при вставке каждой новой записи отправлялось push-уведомление. Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

[Симулятор iOS не поддерживает push-уведомления](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html), поэтому необходимо использовать физическое устройство под управлением iOS. Вам также необходимо зарегистрироваться для [участия в программе разработки решений для Apple](https://developer.apple.com/programs/ios/).

##Предварительные требования

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если у вас еще нет учетной записи, зарегистрируйтесь для использования пробной версии Azure и получите до 10 бесплатных мобильных приложений. Вы сможете использовать их даже после окончания срока действия пробной версии. См. [Бесплатная пробная версия Azure](http://azure.microsoft.com/pricing/free-trial/).

* Компьютер Mac с установленным программным обеспечением [Xamarin Studio] и [Xcode] 4.4 или более поздней версии. Запустить приложение Xamarin.iOS при необходимости можно в Visual Studio на компьютере Windows, но это немного сложнее, так как необходимо подключиться к компьютеру Mac с доступом к сети, на котором выполняется узел сборки Xamarin.iOS. Если вам это интересно, см. статью [Установка Xamarin.iOS в Windows].

* Физическое устройство iOS. Push-уведомления не поддерживаются в симуляторе iOS.

* Ознакомьтесь с [руководством по быстрому запуску Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md).


##Зарегистрируйте приложение для push-уведомлений на портале разработчика Apple.

[AZURE.INCLUDE [Включение push-уведомлений Apple через концентраторы уведомлений Xamarin](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Настройка мобильного приложения для отправки push-уведомлений

Чтобы настроить в приложении отправку уведомлений, создайте новый центр уведомлений и настройте его для работы со службами уведомлений платформы, которые будут использоваться.

1. На портале Azure последовательно выберите пункты **Обзор** > **Мобильные приложения** > ваше мобильное приложение > **Параметры** > **Мобильные службы** > **Push-уведомления** > **Центр уведомлений** > **+ Центр уведомлений**, а затем укажите имя и пространство имен для нового центра уведомлений и нажмите кнопку **ОК**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. В колонке "Создание концентратора уведомлений" нажмите кнопку **Создать**.

3. Выберите пункты **Push-уведомления** > **Apple (APN)** > **Отправка сертификата**. Отправьте экспортированный ранее P12-файл сертификата push-уведомлений. Если вы создали сертификат push-уведомлений для разработки и тестирования, не забудьте выбрать параметр **Песочница**. В противном случае выберите параметр **Рабочая среда**. Теперь ваша служба настроена для работы с push-уведомлениями в iOS!

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

##Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Развертывание серверного проекта в Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Настройка проекта Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##Добавление push-уведомлений в приложение

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

1. Добавьте следующий оператор `using` в верхнюю часть файла **AppDelegate.cs**.

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

2. В приложении введите задачу, а затем щелкните значок "плюс" (**+**).

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


 

<!---HONumber=AcomDC_1125_2015-->