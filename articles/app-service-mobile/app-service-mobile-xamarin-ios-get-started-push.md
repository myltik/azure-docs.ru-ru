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
	ms.date="05/05/2016"
	ms.author="wesmc"/>

# Добавление push-уведомлений в приложение Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##Обзор

Этот учебник использует материал [краткого руководства по Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md), который необходимо предварительно изучить. В нем описывается добавление push-уведомлений в проект учебника по [быстрому запуску Xamarin.iOS], чтобы при вставке каждой новой записи отправлялось push-уведомление. Если вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Предварительные требования

* Ознакомьтесь с [кратким руководством по Xamarin.iOS](app-service-mobile-xamarin-ios-get-started.md).

* Физическое устройство iOS. Push-уведомления не поддерживаются в симуляторе iOS.

##Зарегистрируйте приложение для push-уведомлений на портале разработчика Apple.

[AZURE.INCLUDE [Включение push-уведомлений Apple через концентраторы уведомлений Xamarin](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##Настройка мобильного приложения для отправки push-уведомлений

Чтобы настроить в приложении отправку уведомлений, создайте новый центр уведомлений и настройте его для работы со службами уведомлений платформы, которые будут использоваться.

1. На [портале Azure](https://portal.azure.com/) выберите **Обзор** > **Мобильные приложения** > ваше мобильное приложение > **Параметры** > **Мобильные службы** > **Push-уведомления** > **Концентратор уведомлений** > **+ Концентратор уведомлений**, а затем укажите имя и пространство имен для нового концентратора уведомлений и нажмите кнопку **ОК**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. В колонке «Создание концентратора уведомлений» нажмите кнопку **Создать**.

3. Выберите **Push-уведомления** > **Apple (APNS)** > **Отправка сертификата**. Отправьте экспортированный ранее P12-файл сертификата push-уведомлений. Если вы создали сертификат push-уведомлений для разработки и тестирования, не забудьте выбрать параметр **Песочница**. В противном случае выберите параметр **Рабочая среда**.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

Теперь ваша служба настроена для работы с push-уведомлениями в iOS.

##Обновление серверного проекта для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Настройка проекта Xamarin.iOS

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
		using Newtonsoft.Json.Linq;

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

3. В том же файле переопределите событие **RegisteredForRemoteNotifications**. В этом коде регистрируется простое шаблонное уведомление, которое будет рассылаться сервером по всем поддерживаемым платформам.

	Дополнительные сведения о шаблонах центров уведомлений см. в статье [Шаблоны](../notification-hubs/notification-hubs-templates.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
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

	> [AZURE.NOTE] Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите задачу, а затем щелкните значок плюса (**+**).

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

4. Повторите шаг 2 и сразу закройте приложение, затем убедитесь, что уведомление отображается.

Вы успешно завершили ознакомление с данным учебником.

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0511_2016-->