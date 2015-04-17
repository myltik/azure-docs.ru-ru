<properties 
	pageTitle="Добавление push-уведомлений в приложение Xamarin iOS с помощью службы приложений Azure" 
	description="Использование службы приложений Azure для отправки push-уведомлений в приложение Xamarin iOS" 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="yuaxu"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="02/22/2015" 
	ms.author="yuaxu"/>

# Добавление push-уведомлений в приложение Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

В этом разделе показано, как использовать службу приложений Azure для отправки push-уведомлений в приложение Xamarin iOS 8. В этом учебнике вы добавите push-уведомления, используя службу push-уведомлений Apple (APNs) в проекте [Начало работы с мобильными приложениями службы приложений]. По завершении мобильный внутренний сервер будет отправлять push-уведомление при каждой вставке кода.

Для работы с данным учебником требуется следующее:

+ Устройство iOS 8
+ Участие в программе для разработчиков на платформе iOS
+ [Xamarin.iOS Studio]
+ [Компонент мобильных служб Azure]

   > [AZURE.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Служба push-уведомлений Apple (APNs) использует сертификаты для проверки подлинности мобильного приложения. Следуйте этим инструкциям, чтобы создать нужные сертификаты и отправить их в мобильное приложение. Официальную документацию по APNs см. в разделе [Служба push-уведомлений Apple].

## <a name="certificates"></a>Создание файла запроса на подпись сертификата

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. Из раздела служебных программ запустите **средство Keychain Access**.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

    ![][5]

3. Заполните поля **Адрес электронной почты пользователя**, **Общее имя**, убедитесь, что установлен флажок **Сохранено на диск**, затем щелкните **Продолжить**.

    ![][6]

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

    ![][7]
  
    Запомните выбранное расположение.

Затем вы зарегистрируете ваше приложение в Apple, включите push-уведомления и передадите этот экспортированный CSR-файл для создания сертификата push-уведомлений.

## <a name="register"></a>Регистрация приложений для получения push-уведомлений

Чтобы иметь возможность получить push-уведомления на устройство iOS из мобильного приложения, необходимо зарегистрировать приложение в службе Apple и зарегистрироваться для получения push-уведомлений. 

1. Если вы еще не зарегистрировали приложение, перейдите на портал подготовки iOS</a> в Apple Developer Center, войдите с помощью Apple ID, щелкните **Identifiers** (Идентификаторы), затем **App IDs** (Идентификаторы приложения) и щелкните значок **+**, чтобы создать идентификатор приложения.
    
    ![][102]

2. Введите название приложения в поле **Description** (Описание), введите и запомните уникальный **Bundle Identifier** (Идентификатор пакета), выберите вариант "Push-уведомления" в разделе "Службы приложений", затем нажмите **Continue** (Продолжить). В этом примере используется идентификатор **MobileServices.Quickstart**, но вы не можете повторно использовать тот же идентификатор, поскольку идентификаторы приложений должны быть уникальными для всех пользователей. Поэтому рекомендуется добавить ваше полное имя или инициалы после имени приложения. 

    ![][103]
   
    При этом создается идентификатор вашего приложения и появляется запрос об **отправке** информации. Нажмите кнопку **Submit** (Отправить).
   
    ![][104] 
   
    После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).
   
    ![][105]    

3. Найдите созданный вами идентификатор приложения и щелкните его строку. 

    ![][106]
   
    Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения. Нажмите кнопку **Settings** (Параметры).
   
    ![][107] 
   
4. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

    ![][108] 

    Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).
   
    Примечание. В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Убедитесь, что вы задали тот же тип сертификата при отправке сертификата в мобильное приложение.

5. Щелкните **Выбрать файл**, перейдите к папке, где ранее был сохранен CSR-файл, а затем щелкните **Создать**. 

    ![][110]
  
6. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить) и щелкните **Done** (Готово).
 
    ![][111]  

    При этом сертификат подписи скачивается и сохраняется на вашем компьютере в папке "Загрузки". 

    ![][9] 

    Примечание. По умолчанию скачанный файл сертификата разработки называется <strong>aps_development.cer</strong>.

7. Дважды щелкните скачанный сертификат push-уведомлений **aps_development.cer**.

    При этом новый сертификат устанавливается в Keychain, как показано ниже:

    ![][10]

    Примечание. Имя сертификата может отличаться, но оно не будет иметь префикс <strong>Службы push-уведомлений iOS разработки для Apple:</strong>.

Далее вы используете этот сертификат для создания P12-файла и отправки его в мобильное приложение, чтобы включить проверку подлинности с помощью APNs.

## <a name="profile"></a>Создание профиля подготовки для приложения
 
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> последовательно выберите пункты **Профили подготовки**, **Все**, а затем нажмите кнопку **+**, чтобы создать новый профиль. Запустится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).

    ![][112]

2. Выберите **iOS App Development** (Разработка приложений для iOS) в разделе **Development** (Разработка) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

3. Далее выберите идентификатор приложения для приложения краткого руководства по мобильным приложениям из раскрывающегося списка **Идентификатор приложения** и щелкните **Продолжить**.

    ![][113]

4. На экране **Выбор сертификатов** выберите сертификат, созданный ранее, и щелкните **Продолжить**.

    ![][114]

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).
  
    ![][115]

6. Наконец, выберите имя профиля в поле **Profile Name** (Имя профиля), щелкните **Generate** (Создать) и нажмите кнопку **Done** (Готово).

    ![][116]

    В результате создается новый профиль подготовки.

    ![][117]

## <a name="configure-appServiceMobile"></a>Настройка мобильного внутреннего сервера службы приложений для отправки push-уведомлений

[AZURE.INCLUDE [app-service-mobile-apns-configure-push-preview](../includes/app-service-mobile-apns-configure-push-preview.md)]

##<a id="update-server"></a>Обновление сервера для отправки push-уведомлений

1. В Visual Studio щелкните правой кнопкой по решению, после чего щелкните **Управление пакетами NuGet**.

2. Выполните поиск **Microsoft.Azure.NotificationHubs** и щелкните **Установить** для всех проектов в решении.

3. В обозревателе решений Visual Studio откройте папку **Контроллеры** в проекте мобильного внутреннего сервера. Откройте файл TodoItemController.cs. Добавьте следующие операторы `using` в начало файла:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Добавьте следующий фрагмент в метод `PostTodoItem` после вызова **InsertAsync**:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // iOS payload
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

        try
        {
            await Hub.Push.SendAppleNativeNotificationAsync(appleNotificationPayload);
        }
        catch (System.Exception ex)
        {
            throw;
        }

    Этот код запрашивает в концентраторе уведомлений, связанном с этим мобильным приложением, отправку push-уведомления после вставки элемента задачи.


<h2><a name="publish-the-service"></a>Публикация мобильного внутреннего сервера в Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="configure-app"></a>Настройка приложения Xamarin.iOS

1. В Xamarin.Studio откройте **Info.plist**, обновите **Идентификатор пакета**, указав свой ранее созданный идентификатор.

    ![][121]

2. Прокрутите вниз до **Background Modes** (Фоновые режимы), выберите **Enable Background Modes** (Разрешить фоновые режимы) и **Remote notifications** (Удаленные уведомления). 

    ![][122]

3. Дважды щелкните проект на панели решения, чтобы открыть **Параметры проекта**.

4.  Выберите **iOS Bundle Signing** (Подпись пакета iOS) в разделе **Build** (Сборка), выберите соответствующее **Identity** (Удостоверение) и созданный для данного проекта **Provisioning profile** (Профиль подготовки). 

    ![][120]

    Это обеспечит использование нового профиля для подписывания кода в проекте Xamarin. Официальная документация по подготовке устройств Xamarin: [Подготовка устройств Xamarin].

## <a name="add-push"></a>Добавление push-уведомлений в приложение

1. В **QSTodoService** переопределите существующее объявление клиента, чтобы элемент **AppDelegate** мог получить мобильный клиент:
        
        public MobileServiceClient client { get; private set; }

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


<h2><a name="publish-the-service"></a>Публикация мобильного внутреннего сервера в Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## <a name="test"></a>Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Run** (Запуск), чтобы выполнить сборку проекта и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.
	
	> [AZURE.NOTE] Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите задачу, затем щелкните знак "плюс" (**+**).

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

4. Повторите шаг 2 и сразу закройте приложение, затем убедитесь, что уведомление отображается.

Вы успешно завершили ознакомление с данным учебником.

<!-- Images. -->

[24]: ./media/mobile-services-ios-get-started-push/mobile-services-quickstart-push2-ios.png
[Начало работы с мобильными приложениями службы приложений]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[5]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[25]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

[120]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-20.png
[121]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-21.png
[122]:./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-services-ios-push-22.png

[Xamarin.iOS Studio]: http://xamarin.com/platform
[Установка Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Пакет SDK для мобильных служб iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Начало работы с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
[Начало работы с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Начало работы с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Начало работы с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push-уведомления для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Подготовка устройств Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Портал управления Azure]: https://manage.windowsazure.com/
[Объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331303


<!--HONumber=49-->