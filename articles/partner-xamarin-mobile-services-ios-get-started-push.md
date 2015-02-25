<properties 
	pageTitle="Приступая к работе с push-уведомлениями (Xamarin.iOS) - Мобильные службы" 
	description="Узнайте, как использовать push-уведомления в приложениях Xamarin.iOS с помощью мобильных служб Azure." 
	documentationCenter="xamarin" 
	authors="ysxu" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/20/2014" 
	ms.author="yuaxu"/>

# Добавление push-уведомлений в приложение мобильной службы

[AZURE.INCLUDE [mobile-services-selector-get-started-push-xamarin](../includes/mobile-services-selector-get-started-push-xamarin.md)]

<p>В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Xamarin.iOS 8. В этом учебнике вам предстоит добавить push-уведомления в проект [Приступая к работе с мобильными службами] с помощью службы push-уведомлений Apple (APNS). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.</p>

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата] 
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
4. [Настройка мобильных служб]
5. [Настройка приложения Xamarin.iOS]
6. [Добавление push-уведомлений в приложение]
7. [Обновление сценариев для отправки push-уведомлений]
8. [Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

+ Устройство iOS 8
+ Участие в программе для разработчиков на платформе iOS
+ [Xamarin.iOS Studio]
+ [Компонент мобильных служб Azure]

   > [AZURE.NOTE] В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple].

## <a name="certificates"></a>Создание файла запроса подписи сертификата

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. Из раздела "Служебные программы" запустите **средство Keychain Access**.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

    ![][5]

3. Заполните поля **Адрес электронной почты пользователя**, **Общее имя**, убедитесь, что установлен флажок **Сохранено на диск**, затем щелкните **Продолжить**.

    ![][6]

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

    ![][7]
  
    Запомните выбранное расположение.

Затем вы зарегистрируете ваше приложение в Apple, включите push-уведомления и передадите этот экспортированный CSR-файл для создания сертификата push-уведомлений.

## <a name="register"></a>Регистрация приложения для получения push-уведомлений

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в Apple, а также зарегистрировать его для получения push-уведомлений. 

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием идентификатора Apple, щелкните **Identifiers** (Идентификаторы), затем - **App IDs** (Идентификаторы приложений) и, наконец, щелкните знак **+**, чтобы создать идентификатор приложения.
    
    ![][102]

2. Введите название приложения в поле **Описание**, введите и запомните уникальный **Идентификатор пакета**, выберите вариант "Push-уведомления" в разделе "Службы приложений", затем нажмите **Продолжить**. В этом примере используется идентификатор **MobileServices.Quickstart**, но вы не можете повторно использовать тот же идентификатор, поскольку идентификаторы приложений должны быть уникальными для всех пользователей. Поэтому рекомендуется добавить ваше полное имя или инициалы после имени приложения. 

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
   
    Примечание. В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.

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

    Примечание. Имя вашего сертификата может отличаться, но оно будет начинаться с префикса <strong>Apple Development iOS Push Notification Services:</strong>.

Позже этот сертификат будет использоваться для создания файла .p12 и отправки его в мобильные службы для включения проверки подлинности с помощью APNS.

## <a name="profile"></a>Создание профиля подготовки для приложения
 
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Профили подготовки**, затем щелкните **Все** и нажмите кнопку **+**, чтобы создать новый профиль. Запустится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).

    ![][112]

2. Выберите **iOS App Development** (Разработка приложений для iOS) в разделе **Development** (Разработка) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

3. Затем выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами в раскрывающемся списке **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).

    ![][113]

4. На экране **Select certificates** (Выбор сертификатов) выберите ранее созданный сертификат и нажмите кнопку **Continue** (Продолжить).

    ![][114]

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).
  
    ![][115]

6. Наконец, выберите имя профиля в поле **Profile Name** (Имя профиля), щелкните **Generate** (Создать) и нажмите кнопку **Done** (Готово).

    ![][116]

    В результате создается новый профиль подготовки.

    ![][117]

## <a name="configure-mobileServices"></a>Настройка мобильных служб для отправки push-запросов

После регистрации приложения в APNS и настройки проекта необходимо настроить мобильную службу для интеграции с APNS.

1. В Keychain Access щелкните правой кнопкой мыши новый сертификат, щелкните **Export** (Экспорт), назовите файл , выберите формат **.p12**, а затем нажмите кнопку **Save** (Сохранить).

    ![][28]

    Запишите имя файла и расположение экспортируемого сертификата.

2. Войдите на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем выберите нужное приложение.

    ![][18]

3. На вкладке **Push** щелкните **Отправить** в **параметрах службы push-уведомлений Apple**.

    ![][19]

    Откроется диалоговое окно отправки сертификата.

4. Щелкните **Файл**, выберите файл экспортированного сертификата .p12 и введите пароль в поле **Пароль**. Убедитесь, что выбран правильный режим в поле **Режим**, щелкните значок галочки, а затем нажмите кнопку **Сохранить**.

    ![][20] 

Ваша мобильная служба теперь настроена для работы с APNS.

## <a name="configure-app"></a>Настройка приложения Xamarin.iOS

1. В Xamarin.Studio откройте **Info.plist**, обновите **Идентификатор пакета**, указав свой ранее созданный идентификатор.

    ![][121]

2. Прокрутите вниз до **Фоновые режимы**, выберите **Разрешить фоновые режимы** и **Удаленные уведомления**. 

    ![][122]

3. Дважды щелкните проект на панели решения, чтобы открыть **Параметры проекта**.

4.  Выберите **Подпись пакета iOS** в разделе **Сборка**, выберите соответствующее **удостоверение** и созданный для данного проекта **Профиль подготовки**. 

    ![][120]

    Это обеспечит использование нового профиля для подписывания кода в проекте Xamarin. Официальная документация по подготовке устройств Xamarin: [Xamarin Device Provisioning].

## <a name="add-push"></a>Добавление push-уведомлений в приложение

1. В Xamarin.Studio откройте файл AppDelegate.cs и добавьте следующее свойство:

        public string DeviceToken { get; set; }

2. Откройте класс **TodoItem** и добавьте следующее свойство:

        [JsonProperty(PropertyName = "deviceToken")]
        public string DeviceToken { get; set; }

3. В **QSTodoService** переопределите существующее объявление клиента:
        
        public MobileServiceClient client { get; private set; }

4. Затем добавьте следующий метод, чтобы **AppDelegate** мог потом получить клиента для регистрации push-уведомлений:

        public MobileServiceClient GetClient {
            get{ 
                return client;
            }
        }

5. В **AppDelegate** переопределите событие **FinishedLaunching**: 

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

6. В **AppDelegate** переопределите событие **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            DeviceToken = deviceToken.Description;
            DeviceToken = DeviceToken.Trim ('<', '>').Replace (" ", "");

            // Get Mobile Services client
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            var push = client.GetPush ();
            push.RegisterNativeAsync (DeviceToken, tag);
        }

7. В **AppDelegate** переопределите событие **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            Debug.WriteLine(userInfo.ToString());
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

8. В **TodoListViewController** измените действие **OnAdd**, чтобы получить маркер устройства, хранящегося в **AppDelegeate**, и сохранить его в добавляемый элемент **TodoItem**.

        string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

        var newItem = new TodoItem() 
        {
            Text = itemText.Text, 
            Complete = false,
            DeviceToken = deviceToken
        };

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## <a name="update-scripts"></a>Обновление зарегистрированных сценариев вставки на портале управления

1. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

    ![][21]

2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.
   
    ![][22]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send("uniqueTag", {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    При этом регистрируется новый сценарий вставки, в котором используется [объект apns] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки. 

   > [AZURE.NOTE] Этот скрипт задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения всплывающего уведомления.

## <a name="test"></a>Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

    ![][23]

   > [AZURE.NOTE] Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_) и щелкните значок "плюс" (**+**).

    ![][24]

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

    ![][25]

4. Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее всплывающее уведомление.

    ![][26]

Вы успешно завершили ознакомление с данным учебником.

<!-- Anchors. -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Настройка мобильных служб]: #configure-mobileServices
[Настройка приложения Xamarin.iOS]: #configure-app
[Обновление сценариев для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test

<!-- Images. -->

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
[24]: ./media/partner-xamarin-mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
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
[Пакет SDK для мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с проверкой подлинности]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Подготовка устройства Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Портал управления Azure]: https://manage.windowsazure.com/
[объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331303


<!--HONumber=42-->
