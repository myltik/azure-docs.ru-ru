<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-ios" urlDisplayName="Приступая к работе с push-уведомлениями" pageTitle="Приступая к работе с push-уведомлениями (Xamarin.iOS) — мобильные службы" metaKeywords="" description="Узнайте, как использовать push-уведомления в приложениях Xamarin.iOS с помощью мобильных служб Azure." metaCanonical="" disqusComments="0" umbracoNaviHide="1" editor="mollybos" documentationCenter="Mobile" title="Приступая к работе с push-уведомлениями" authors="" />

# Приступая к работе с push-уведомлениями в мобильных службах
<div class="dev-center-tutorial-selector sublanding"><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet" title="Магазин Windows C#">Магазин Windows C#</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<p>В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Xamarin.iOS. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого начала работы с помощью службы push-уведомлений Apple (APNS). В результате ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.</p>

   <div class="dev-callout"><b>Примечание.</b>
   <p>В этом учебнике демонстрируется упрощенный способ отправки push-уведомлений путем присоединения маркера устройства push-уведомления к вставленной записи. Рекомендуем вам изучить следующий учебник, чтобы получить более полное представление о том, встроить push-уведомления в реальные приложения.</p>
   </div>

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

1. [Создание запроса подписи сертификата] 
2. [Регистрация приложения и включение push-уведомлений]
3. [Создание профиля подготовки для приложения]
3. [Настройка мобильных служб]
4. [Добавление push-уведомлений в приложение]
5. [Обновление скриптов для отправки push-уведомлений]
6. [Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

+ [XCode 5.0][Install Xcode] 
+ Устройство с iOS 5.0 (или более поздней версии)
+ Участие в программе для разработчиков на платформе iOS
+ [Xamarin.iOS]
+ [Компонент мобильных служб Azure]

   <div class="dev-callout"><b>Примечание.</b>
   <p>В соответствии с требованиями к конфигурации push-уведомлений необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.</p>
   </div>

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами]. 

Служба push-уведомлений Apple (APNS) использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple].

<h2><a name="certificates"></a><span class="short-header">Создание файла CSR</span>Создание файла запроса подписи сертификата</h2>

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. В папке "Служебные программы" запустите средство Keychain Access.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации).

  	![][5]

3. Выберите **User Email Address** (Адрес электронной почты пользователя), введите значения **Common Name** (Общее имя) и **CA Email Address** (Адрес электронной почты ЦС), убедитесь, что установлен флажок **Saved to disk** (Сохранено на диск), а затем щелкните **Continue** (Продолжить).

  	![][6]

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

  	![][7]
  
  	При этом CSR-файл сохраняется в выбранном месте; по умолчанию — на рабочем столе. Запомните расположение, выбранное для этого файла.

Затем вы зарегистрируете ваше приложение в Apple, включите push-уведомления и передадите этот экспортированный CSR-файл для создания сертификата push-уведомлений.

<h2><a name="register"></a><span class="short-header">Регистрация приложения</span>Регистрация приложения для получения push-уведомлений</h2>

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в Apple, а также зарегистрировать его для получения push-уведомлений.  

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием вашего идентификатора Apple, щелкните **Identifiers** (Идентификаторы), затем — **App IDs** (Идентификаторы приложений) и, наконец, щелкните знак **+**.

   	![][102] 

2. Введите имя приложения в поле **Description** (Описание), введите значение _MobileServices.Quickstart_ в поле **Bundle Identifier** (Идентификатор набора), выберите вариант "Push Notifications" (Push-уведомления) в разделе "App Services" (Службы приложений), а затем щелкните **Continue** (Продолжить). В этом примере используется идентификатор **MobileServices.Quickstart**, но вы не можете повторно использовать тот же идентификатор, поскольку идентификаторы приложений должны быть уникальными для всех пользователей. Поэтому рекомендуется добавить ваше полное имя или инициалы после имени приложения. 

   	![][103]
   
   	При этом создается идентификатор вашего приложения и появляется запрос об **отправке** информации. Нажмите кнопку **Submit** (Отправить).
   
   	![][104] 
   
   	После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).
   
   	![][105]

	Примечание. Если вы хотите указать значение **Bundle Identifier** (Идентификатор набора), отличное от *MobileServices.Quickstart*, необходимо также обновить значение идентификатора набора в проекте Xcode.
    

3. Найдите созданный вами идентификатор приложения и щелкните его строку. 

   	![][106]
   
   	Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения. Нажмите кнопку **Settings** (Параметры).
   
   	![][107] 
   
4. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

   	![][108] 

   	Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).
   
   	![][108] 

    Примечание. В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.

5. Щелкните **Choose File** (Выбрать файл), перейдите к папке, где был сохранен CSR-файл, созданный в первом задании, а затем щелкните **Generate** (Создать). 

  	![][110]
  
6. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить) и **Done** (Готово).
 
  	![][111]  

   	При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке "Загрузки". 

  	![][9] 

    Примечание. По умолчанию загруженный файл сертификата разработки называется <strong>aps_development.cer</strong>.

7. Дважды щелкните загруженный сертификат push-уведомлений **aps_development.cer**.

   	При этом новый сертификат устанавливается в Keychain, как показано ниже:

   	![][10]

    Примечание. Имя вашего сертификата может отличаться, но оно будет начинаться с префикса <strong>Apple Development iOS Push Notification Services:</strong>.

Позже этот сертификат будет использоваться для создания файла .p12 и отправки его в мобильные службы для включения проверки подлинности с помощью APNS.

<h2><a name="profile"></a><span class="short-header">Подготовка приложения</span>Создание профиля подготовки для приложения</h2>
 
1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Provisioning Profiles** (Профили подготовки), затем выберите **All** (Все) и нажмите кнопку **+**, чтобы создать новый профиль. Запустится мастер **Add iOS Provisiong Profile** (Добавление профиля подготовки для iOS).

   	![][112]

2. Выберите **iOS App Development** (Разработка приложений для iOS) в разделе **Development** (Разработка) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

   	![][113]

3. Затем выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами в раскрывающемся списке **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).

   	![][114]

4. На экране **Select certificates** (Выбор сертификатов) выберите ранее созданный сертификат и нажмите кнопку **Continue** (Продолжить).
  
   	![][115]

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

   	![][116]

6. Наконец, выберите имя профиля в поле **Profile Name** (Имя профиля), щелкните **Generate** (Создать) и нажмите кнопку **Done** (Готово).

   	![][117]
  
  	В результате создается новый профиль подготовки.

7. В Xcode откройте "Organizer" (Диспетчер), выберите представление "Devices" (Устройства), выберите **Provisioning Profiles** (Профили подготовки) в разделе **Library** (Библиотека) на левой панели и нажмите кнопку **Refresh** (Обновить) в нижней части средней панели. 

   	![][101]

8. В разделе **Targets** (Цели) щелкните **Quickstart** (Быстрый запуск), разверните **Code Signing Identity** (Удостоверения подписи кода), а затем в разделе **Debug** (Отладка) выберите новый профиль.

   	![][17]

Это гарантирует, что проект Xcode использует новый профиль для подписи кода. Затем необходимо загрузить этот сертификат в мобильные службы.

<a name="configure"></a><h2><span class="short-header">Настройка службы</span>Настройка мобильных служб для отправки push-запросов</h2>

После регистрации приложения в APNS и настройки проекта необходимо настроить мобильную службу для интеграции с APNS.

1. В Keychain Access щелкните правой кнопкой мыши новый сертификат, щелкните **Export** (Экспорт), назовите файл QuickstartPusher, выберите формат **.p12**, а затем нажмите кнопку **Save** (Сохранить).

   	![][28]

  	Запишите имя файла и расположение экспортируемого сертификата.

    <div class="dev-callout"><b>Примечание.</b>
	<p>В этом учебнике создается файл QuickstartPusher.p12. Имя файла и расположение могут отличаться.</p>
    </div>

2. Выполните вход на [портал управления Azure], щелкните **Мобильные службы**, а затем щелкните свое приложение.

   	![][18]

3. Перейдите на вкладку **Push** (Push-уведомления) и щелкните **Upload** (Отправить).

   	![][19]

   	Откроется диалоговое окно отправки сертификата.

4. Щелкните **File** (Файл), выберите файл экспортированного сертификата QuickstartPusher.p12, введите пароль в поле **Password** (Пароль), убедитесь, что выбран правильный режим в поле **Mode** (Режим), щелкните значок галочки, а затем нажмите кнопку **Save** (Сохранить).

   	![][20] 

    <div class="dev-callout"><b>Примечание.</b>
	<p>В этом учебнике используются сертификаты разработки.</p>
    </div>

Ваша мобильная служба теперь настроена для работы с APNS.

<a name="add-push"></a><h2><span class="short-header">Добавление push-уведомлений</span>Добавление push-уведомлений в приложение</h2>

1. В Xamarin.Studio откройте файл AppDelegate.cs и добавьте следующее свойство:

        public string DeviceToken { get; set; }

2. Откройте класс **TodoItem** и добавьте следующее свойство:

        [DataMember(Name = "deviceToken")]
        public string DeviceToken { get; set; }

    <div class="dev-callout"><b>Примечание.</b>
	<p>Когда в вашей мобильной службе включена динамическая схема, новый столбец "deviceToken" автоматически добавляется в таблицу <strong>TodoItem</strong> при вставке нового элемента, содержащего это свойство.</p>
    </div>

3. В **AppDelegate** переопределите событие **FinishedLaunching**: 

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | 
                UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes); 

            return true;
        }

4. В **AppDelegate** переопределите событие **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            string trimmedDeviceToken = deviceToken.Description;
            if (!string.IsNullOrWhiteSpace(trimmedDeviceToken))
            {
                trimmedDeviceToken = trimmedDeviceToken.Trim('<');
                trimmedDeviceToken = trimmedDeviceToken.Trim('>');
            }
            DeviceToken = trimmedDeviceToken;
        }

5. В **AppDelegate** переопределите событие **ReceivedRemoteNotification**:

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

6. В **TodoListViewController** измените действие **OnAdd**, чтобы получить маркер устройства, хранящегося в **AppDelegeate**, и сохранить его в добавляемый элемент **TodoItem**.

      string deviceToken = ((AppDelegate)UIApplication.SharedApplication.Delegate).DeviceToken;

			var newItem = new TodoItem() 
			{
				Text = itemText.Text, 
				Complete = false,
                DeviceToken = deviceToken
			};

Ваше приложение теперь обновлено для поддержки push-уведомлений.

<h2><a name="update-scripts"></a><span class="short-header">Обновление сценария вставки</span>Обновление зарегистрированного сценария вставки на портале управления</h2>

1. На портале управления откройте вкладку **Данные**, а затем щелкните таблицу **TodoItem**. 

   	![][21]

2. В **todoitem** откройте вкладку **Скрипт** и выберите **Вставка**.
   
  	![][22]

   	При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

   	При этом регистрируется новый скрипт вставки, в котором используется [объект apns] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки. 


   	<div class="dev-callout"><b>Примечание.</b>
   <p>Этот скрипт задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения всплывающего уведомления.</p>
   </div> 

<h2><a name="test"></a><span class="short-header">Тестирование приложения</span>Тестирование push-уведомлений в приложении</h2>

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

  	![][23]

    <div class="dev-callout"><b>Примечание.</b>
    <p>Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.</p>
    </div>

2. В приложении введите содержательный текст (например, _Новая задача для мобильных служб_) и щелкните знак плюс (**+**).

  	![][24]

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.

  	![][25]

4. Повторите шаг 2 и немедленно закройте приложение, а затем проверьте, что отображается следующее всплывающее уведомление.

  	![][26]

Вы успешно завершили ознакомление с данным учебником.

## Получение полного примера
Загрузите [полный пример проекта]. Не забудьте обновить переменные **applicationURL** и **applicationKey** с использованием своих параметров Azure. 

## <a name="next-steps"> </a>Дальнейшие действия

В этом простом примере пользователь получает push-уведомление с только что вставленными данными. Маркер устройства, используемый APNS, передается клиентом мобильной службе в запросе. В следующем учебнике [Принудительная отправка уведомлений пользователям приложения] вы будете создавать отдельную таблицу "Устройства" для хранения маркеров устройств и отправлять push-уведомление всем сохраненным каналам при выполнении вставки. 

<!-- Anchors. -->
[Создание запроса подписи сертификата]: #certificates
[Регистрация приложения и включение push-уведомлений]: #register
[Создание профиля подготовки для приложения]: #profile
[Настройка мобильных служб]: #configure
[Обновление скриптов для отправки push-уведомлений]: #update-scripts
[Добавление push-уведомлений в приложение]: #add-push
[Вставка данных для получения уведомлений]: #test
[Дальнейшие действия]:#next-steps

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

[Установить Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Портал подготовки iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[SDK мобильных служб для iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-xamarin-ios
[Приступая к работе с данными]: /ru-ru/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Приступая к работе с аутентификацией]: /ru-ru/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Приступая к работе с push-уведомлениями]: /ru-ru/develop/mobile/tutorials/get-started-with-push-xamarin-ios
[Push-уведомлений для пользователей приложений]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-ios
[Авторизация пользователей с помощью скриптов]: /ru-ru/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios


[Портал управления Azure]: https://manage.windowsazure.com/
[объект apns]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[Полный пример проекта]: http://go.microsoft.com/fwlink/p/?LinkId=331303

