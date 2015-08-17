 <properties
	pageTitle="Добавление push-уведомлений в приложение мобильных служб (Xamarin.Forms) — мобильные службы"
	description="Узнайте, как использовать push-уведомления в приложениях Xamarin.Forms с помощью мобильных служб Azure."
	documentationCenter="xamarin"
	authors="normesta"
	manager="stevenpo"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="06/10/2015"
	ms.author="normesta"/>

# Добавление push-уведомлений в приложение Xamarin.Forms
[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Обзор

В этом учебнике показано, как использовать мобильные службы Azure для отправки push-уведомлений в iOS, Android и приложение Windows Phone вашего решения Xamarin.Forms. Начните с создания мобильной службы. Затем загрузите начальный образец, зарегистрируйтесь в соответствующих службах push-уведомлений и добавьте код в решение для получения уведомлений от этих служб.

После прохождения этого учебника мобильная служба будет присылать вам push-уведомление каждый раз при добавлении пользователем задачи в одном из приложений. Вы можете найти готовый образец здесь: [Готовый образец push-уведомления Azure для приложения Xamarin.Forms ].

Для работы с данным учебником требуется следующее:

+ Устройство iOS 8 (в симуляторе iOS невозможно тестировать push-уведомления)
+ Участие в программе для разработчиков на платформе iOS
+ [Xamarin.iOS Studio]
+ [Компонент мобильных служб Azure]
+ Активная учетная запись Google
+ [Компонент клиента Google Cloud Messaging]. Этот компонент будет добавлен при прохождении учебника.

Содержание раздела

1. [Создание мобильной службы](#create-service)
2. [Загрузка и настройка начального образца](#download-starter-sample)
4. [Добавление push-уведомлений в приложение Xamarin.Forms.iOS](#iOS)
5. [Добавление push-уведомлений в приложение Xamarin.Forms.Android](#Android)
6. [Добавление push-уведомлений в приложение Xamarin.Forms.Windows](#Windows)
7. [Обновление скрипта вставки таблицы Azure для отправки push-уведомлений всем приложениям](#all-apps)

## <a name="create-service"></a>Создание мобильной службы

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

Чтобы иметь возможность сохранять данные приложения в новой мобильной службе, необходимо сначала создать новую таблицу.

1. В портале управления нажмите **Мобильные службы**, затем нажмите только что созданную мобильную службу.

2. Нажмите вкладку **Данные**, а затем нажмите **+Создать**.

    ![][123]

   	Откроется диалоговое окно **Создание новой таблицы**.

3. В поле **Имя таблицы** введите _TodoItem_ и нажмите кнопку проверки.

    ![][124]

  	Будет создана новая таблица устройств хранения данных **TodoItem** с набором разрешений по умолчанию; это означает, что любой пользователь приложения сможет получить доступ к данным в таблице и менять их.

    > [AZURE.NOTE]То же имя таблицы используется при быстром запуске мобильных служб. Однако каждая таблица создается в схеме, которая относится к данной мобильной службе. Это необходимо для предотвращения конфликтов данных, когда несколько мобильных служб используют одну и ту же базу данных.

4. Нажмите новую таблицу **TodoItem** и убедитесь, что нет ни одной строки данных.

5. Перейдите на вкладку **Столбцы** и убедитесь, что имеется только один столбец **ID**, который должен быть создан автоматически.

  	Это минимальные требования для таблицы в мобильных службах.

    > [AZURE.NOTE]Если в вашей мобильной службе появится динамическая схема, новые столбцы будут создаваться автоматически при отправлении объектов JSON в мобильную службу при операции вставки или обновления.

Теперь вы готовы использовать новую мобильную службу как хранилище данных для приложения.

## <a name="download-starter-sample"></a>Загрузка и настройка начального образца
Push-уведомления будут добавлены в существующий образец.
  
1. Скачайте образец [Начальный образец push-уведомления Azure для приложения Xamarin.Forms].

2. На портале управления щелкните **Мобильные службы**, а затем выберите мобильную службу. Откройте вкладку **Панель мониторинга** и запишите значение параметра **URL-адрес сайта**. Затем щелкните **Управление ключами** и запишите значение параметра **Ключ приложения**. Эти значения потребуются при обращении к мобильной службе из кода приложения.

3. В проекте **ToDoAzure(Portable)** решения откройте файл **Constants.cs**, замените `ApplicationURL` и `ApplicationKey` URL-адресом сайта и ключом приложения, полученными на предыдущем этапе.

## <a name="iOS"></a>Добавление push-уведомлений в приложение Xamarin.Forms.iOS

Вам предстоит добавить push-уведомления в приложение iOS с помощью службы push-уведомлений Apple (APNS). Вам потребуется активная учетная запись Google и [клиентский компонент Google Cloud Messaging].

>[AZURE.IMPORTANT]В соответствии с требованиями службы push-уведомлений Apple (APNS) необходимо развернуть и протестировать push-уведомления на устройстве с iOS (iPhone или iPad), а не в эмуляторе.

APNS использует сертификаты для аутентификации вашей мобильной службы. Выполните следующие действия, чтобы создать необходимые сертификаты и передать их в мобильную службу. Официальную документацию по APNS см. в разделе [Служба push-уведомлений Apple].

### <a name="certificates"></a>Создание файла запроса подписи сертификата

Сначала необходимо создать файл запроса подписи сертификата (с расширением CSR), используемый Apple для создания подписанного сертификата.

1. Из раздела «Служебные программы» запустите средство **Keychain Access**.

2. Щелкните **Keychain Access**, разверните **Certificate Assistant** (Помощник по сертификатам), а затем щелкните **Request a Certificate from a Certificate Authority...** (Запросить сертификат в центре сертификации...).

    ![][5]

3. Введите **Адрес электронной почты пользователя**, введите **Общее имя**, убедитесь, что установлен флажок **Сохранено на диск**, а затем нажмите кнопку **Продолжить**.

    ![][6]

4. Введите имя файла запроса подписи сертификата (CSR) в поле **Save As** (Сохранить как), выберите расположение в поле **Where** (Папка) и нажмите кнопку **Save** (Сохранить).

    ![][7]

    Запомните выбранное расположение.

Затем вы зарегистрируете ваше приложение в Apple, включите push-уведомления и передадите этот экспортированный CSR-файл для создания сертификата push-уведомлений.

### <a name="register"></a>Регистрация приложения для получения push-уведомлений

Чтобы иметь возможность отправлять push-уведомления в приложение для iOS из мобильных служб, необходимо зарегистрировать ваше приложение в Apple, а также зарегистрировать его для получения push-уведомлений.

1. Если ваше приложение еще не зарегистрировано, перейдите на <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портал подготовки iOS</a> в центре разработчиков Apple, выполните вход с использованием идентификатора Apple, щелкните **Identifiers** (Идентификаторы), затем — **App IDs** (Идентификаторы приложений) и, наконец, щелкните знак **+**, чтобы создать идентификатор приложения.

    ![][102]

2. Введите имя приложения в поле **Описание**, введите и запомните уникальный **идентификатор пакета**, выберите вариант «Push-уведомления» в разделе «Службы приложений», затем нажмите кнопку **Продолжить**. В этом примере используется идентификатор **MobileServices.Quickstart**, но вы не можете повторно использовать тот же идентификатор, поскольку идентификаторы приложений должны быть уникальными для всех пользователей. Поэтому рекомендуется добавить ваше полное имя или инициалы после имени приложения.

    ![][103]

    При этом создается идентификатор вашего приложения и появляется запрос об **отправке** информации. Нажмите кнопку **Submit** (Отправить).

    ![][104]

    После нажатия кнопки **Submit** (Отправить) вы увидите экран **Registration complete** (Регистрация выполнена), представленный ниже. Нажмите кнопку **Done** (Готово).

    ![][105]

3. Найдите созданный вами идентификатор приложения и щелкните его строку.

    ![][106]

    Если щелкнуть идентификатор приложения, отобразятся сведения о приложении и идентификаторе приложения. Нажмите кнопку **Параметры**.

    ![][107]

4. Прокрутите до нижней части экрана и нажмите кнопку **Create Certificate...** (Создать сертификат...) в разделе **Development Push SSL Certificate** (SSL-сертификат разработки push-уведомлений).

    ![][108]

    Откроется помощник "Add iOS Certificate" (Добавление сертификата iOS).

    Примечание. В этом учебнике используется сертификат разработки. Тот же процесс используется при регистрации сертификата производства. Просто убедитесь, что задается тот же тип сертификата при отправке сертификата в мобильные службы.

5. Щелкните **Choose File** (Выбрать файл), перейдите в папку, в которую ранее был сохранен CSR-файл, а затем нажмите кнопку **Generate** (Создать).

    ![][110]

6. После создания сертификата с помощью портала нажмите кнопку **Download** (Загрузить) и щелкните **Done** (Готово).

    ![][111]

    При этом сертификат подписи загружается и сохраняется на вашем компьютере в папке "Загрузки".

    ![][9]

    По умолчанию загруженный файл сертификата разработки называется <strong>aps\_development.cer</strong>.

7. Дважды щелкните скачанный сертификат push-уведомлений **aps\_development.cer**.

    При этом новый сертификат устанавливается в Keychain, как показано ниже:

    ![][10]

    Примечание. Имя вашего сертификата может отличаться, но оно будет начинаться с префикса <strong>Apple Development iOS Push Notification Services:</strong>.

Позже этот сертификат будет использоваться для создания файла .p12 и отправки его в мобильные службы для включения проверки подлинности с помощью APNS.

### <a name="profile"></a>Создание профиля подготовки для приложения

1. На <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">портале подготовки iOS</a> выберите **Профили подготовки**, затем щелкните **Все** и нажмите кнопку **+**, чтобы создать новый профиль. Запустится мастер **добавления профиля подготовки для iOS**.

    ![][112]

2. Выберите пункт **iOS App Development** (Разработка приложений для iOS) в разделе **Development** (Разработка) в качестве типа профиля подготовки и нажмите кнопку **Continue** (Продолжить).

3. Затем выберите идентификатор приложения для приложения быстрого начала работы с мобильными службами в раскрывающемся списке **App ID** (Идентификатор приложения) и нажмите кнопку **Continue** (Продолжить).

    ![][113]

4. На экране **Выбор сертификатов** выберите ранее созданный сертификат и нажмите кнопку **Продолжить**.

    ![][114]

5. Затем выберите **Devices** (Устройства) для тестирования и нажмите кнопку **Continue** (Продолжить).

    ![][115]

6. Наконец, выберите имя профиля в поле **Имя профиля**, нажмите кнопку **Создать**, а затем нажмите кнопку **Готово**.

    ![][116]

    В результате создается новый профиль подготовки.

    ![][117]

7. В Xcode откройте Organizer (Диспетчер), выберите представление Devices (Устройства), выберите **Provisioning Profiles** (Профили подготовки) в разделе **Library** (Библиотека) на левой панели и нажмите кнопку **Refresh** (Обновить) в нижней части средней панели.

### <a name="configure-mobileServices"></a>Настройка мобильных служб для отправки push-запросов

После регистрации приложения в APNS и настройки проекта необходимо настроить мобильную службу для интеграции с APNS.

1. В Keychain Access щелкните правой кнопкой мыши новый сертификат, щелкните **Export** (Экспорт), назовите файл, выберите формат **.p12**, а затем нажмите кнопку **Save** (Сохранить).

    ![][28]

    Запишите имя файла и расположение экспортируемого сертификата.

2. Выполните вход на [портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][18]

3. На вкладке **Push** щелкните **Отправить** в **параметрах службы push-уведомлений Apple**.

    ![][19]

    Откроется диалоговое окно отправки сертификата.

4. Щелкните **Файл**, выберите P12-файл экспортированного сертификата и введите пароль в поле **Пароль**. Убедитесь, что выбран правильный режим в поле **Режим**, щелкните значок галочки, а затем нажмите кнопку **Сохранить**.

    ![][20]

Ваша мобильная служба теперь настроена для работы с APNS.

### <a name="configure-app"></a>Настройка приложения Xamarin.iOS

1. В Xamarin.Studio или Visual Studio откройте файл **Info.plist** и введите в поле **Идентификатор набора** идентификатор, созданный вами ранее.

    ![][121]

2. Прокрутите вниз до раздела **Фоновые режимы** и установите флажки **Разрешить фоновые режимы** и **Удаленные уведомления**.

    ![][122]

3. Дважды щелкните проект на панели решения, чтобы открыть **Параметры проекта**.

4.  Выберите** iOS Bundle Signing** в разделе **Сборка**, выберите соответствующее **Удостоверение** и созданный для данного проекта **Профиль подготовки**.

    ![][120]

    Это обеспечит использование нового профиля для подписывания кода в проекте Xamarin. Официальную документацию по подготовке устройств Xamarin см. в статье [Подготовка устройства Xamarin].

### <a name="add-push"></a>Добавление push-уведомлений в приложение

1. В Xamarin.Studio или Visual Studio разверните проект **ToDoAzure.iOS**, откройте класс **AppDelegate** и вместо события **FinishedLaunching** укажите приведенный ниже код.

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();
            
            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
            
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. В **AppDelegate** переопределите событие **RegisteredForRemoteNotifications**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient;

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };
            
            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("en-US"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag)
        }

7. В **AppDelegate** переопределите событие **ReceivedRemoteNotification**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

Ваше приложение теперь обновлено для поддержки push-уведомлений.

### <a name="update-scripts"></a>Обновление зарегистрированных скриптов вставки на портале управления

1. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][21]

2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][22]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }

    При этом регистрируется новый скрипт вставки, который отправляет push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.

   >[AZURE.NOTE]Этот скрипт задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения всплывающего уведомления.

### <a name="test"></a>Тестирование push-уведомлений в приложении

1. Нажмите кнопку **Выполнить**, чтобы построить проект и запустить приложение на устройстве с iOS, а затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.

   >[AZURE.NOTE]Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.

2. В приложении нажмите кнопку **Добавить**, добавьте название задачи и нажмите кнопку **Сохранить**. 

3. Убедитесь, что уведомление получено, а затем нажмите кнопку **ОК**, чтобы закрыть его.


Вы успешно завершили ознакомление с данным учебником.

## <a name="Android"></a>Добавление push-уведомлений в приложение Xamarin.Forms.Android

Вы будете добавлять push-уведомления в приложение Android с помощью службы Google Cloud Messaging (GCM). Вам потребуется активная учетная запись Google и [клиентский компонент Google Cloud Messaging].

###<a id="register"></a>Включение Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

###<a id="configure"></a>Настройка мобильной службы для отправки push-запросов

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>Обновление зарегистрированного скрипта вставки для отправки уведомлений

>[AZURE.NOTE]Ниже показано, как обновить скрипт, зарегистрированный для операции вставки в таблице TodoItem на портале управления Azure. Вы также можете вызвать и изменить этот скрипт мобильной службы непосредственно в Visual Studio на узле Azure в обозревателе сервера.

На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

   ![][21]

2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

   ![][22]

    This displays the function that is invoked when an insert occurs in the **TodoItem** table.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    При этом регистрируется новый скрипт вставки, который отправляет push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.

   >[AZURE.NOTE]Этот скрипт задерживает отправку уведомления, чтобы вы успели закрыть приложение для получения всплывающего уведомления.


###<a id="configure-app"></a>Настройка существующего проекта для push-уведомлений

1. В представлении решений разверните папку **Компоненты** папки в приложении Xamarin.Android и убедитесь, что установлен пакет мобильных служб Azure. 

2. Щелкните правой кнопкой мыши папку **Компоненты**, выберите пункт **Получить дополнительные компоненты...**, найдите компонент **Клиент Google Cloud Messaging** и добавьте его в проект.

1. Откройте файл проекта MainActivity.cs и добавьте в класс приведенный ниже оператор Using.

		using Gcm.Client;


4.	В классе **MainActivity** добавьте приведенный ниже код в метод **OnCreate** после вызова метода **LoadApplication**.
            
            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

Теперь ваш класс **MainActivity** готов для добавления push-уведомлений.

###<a id="add-push"></a>Добавление кода push-уведомлений в приложение

5. В проекте ToDoAzure.Droid создайте новый класс в проекте с именем `GcmService`.

5. Используя операторы, добавьте приведенные ниже элементы в класс **GcmService**.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. Добавьте следующие разрешения запросов между операторами **using** и объявлением **namespace**:

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. В файл проекта **GcmService.cs** добавьте приведенный ниже класс.
 
        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
        
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	В приведенном выше коде необходимо заменить _`<PROJECT_NUMBER>`_ номером проекта, назначенным службой Google при подготовке приложения к работе на портале разработчика Google.

8. В файл проекта GcmService.cs добавьте приведенный ниже код, который является определяющим для класса **GcmService**.
 
         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	Обратите внимание, что этот класс является производным от **GcmServiceBase**, поэтому к нему необходимо применить атрибут **Service**.

	>[AZURE.NOTE]Класс **GcmServiceBase** реализует методы **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** и **OnError()**. Эти методы необходимо переопределить в классе **GcmService**.

5. Добавьте в класс **GcmService** приведенный ниже код, который переопределяет обработчик событий **OnRegistered**.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;
            
            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }
        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	В этом методе возвращенный идентификатор регистрации GCM используется для регистрации push-уведомлений в службе Azure.

10. Переопределите метод **OnMessage** в **GcmService** с использованием приведенного ниже кода.

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. Добавьте следующие переопределения метода для **OnUnRegistered()** и **OnError()**, необходимые для компиляции проекта.

        protected override void OnError(Context context, string errorId)
        {
              Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>Тестирование push-уведомлений в приложении

Приложение можно проверить, подключив телефон Android напрямую с помощью USB-кабеля или используя виртуальное устройство в эмуляторе.

При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.

> [AZURE.IMPORTANT]Чтобы получать push-уведомления, необходимо настроить учетную запись Google на виртуальном устройстве Google Android (в эмуляторе выберите **Параметры** и **Добавить учетную запись**). Кроме того, убедитесь, что эмулятор подключен к Интернету.

1. В меню **Средства** нажмите **Открыть диспетчер эмулятора Android**, выберите свое устройство и нажмите кнопку **Изменить**.
    
    ![][125]

2. Выберите **API-интерфейсы Google** в поле **Цель**, а затем нажмите кнопку **OK**.
    
    ![][126]

3. На верхней панели инструментов нажмите кнопку **Запуск** и выберите приложение. При этом запускается эмулятор и выполняется приложение.

  Приложение получает значение *registrationId* из GCM и регистрируется в центре уведомлений.

1. Добавьте новую задачу в приложении.

2. Проведите пальцем вниз с верхней части экрана, чтобы открыть центр уведомлений для просмотра уведомления.

	![][127]

## <a name="Windows"></a>Добавление push-уведомлений в приложение Xamarin.Forms.Windows

В этом учебнике показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Windows Phone Silverlight, которое является частью решения Xamarin.Forms.

###<a id="update-app"></a> Обновление приложения для регистрации в целях получения уведомлений

Прежде чем приложение сможет получать push-уведомления, необходимо зарегистрировать канал уведомлений.

1. В Visual Studio откройте файл App.xaml.cs и добавьте следующую инструкцию `using`:

        using Microsoft.Phone.Notification;

3. Добавьте в App.xaml.cs следующий код:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    Этот код получает свойство ChannelURI для приложения из службы push-уведомлений Майкрософт (MPNS), которое используется Windows Phone Silverlight 8.x, и регистрирует полученное свойство ChannelURI для push-уведомлений.

	>[AZURE.NOTE]В этом учебнике мобильная служба отправляет на устройство всплывающее уведомление. Когда вы отправляете всплывающее уведомление, необходимо вместо этого вызвать метод **BindToShellTile** в канале.

4. В верхней части обработчика событий **Application\_Launching** в файле App.xaml.cs добавьте в новый метод **AcquirePushChannel** следующий вызов:

        AcquirePushChannel();

	Это гарантирует, что регистрация запрашивается каждый раз при загрузке страницы. В вашем приложении можно сделать так, чтобы эта регистрация выполнялась только время от времени для гарантии актуальности регистрации.

5. Нажмите клавишу **F5**, чтобы запустить приложение. Отображается всплывающее диалоговое окно с ключом регистрации.
  
6.	В обозревателе решений разверните узел **Свойства**, откройте файл WMAppManifest.xml, щелкните вкладку **Возможности** и убедитесь, что установлен флажок для возможности **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION**.

   	![Включение уведомлений в оболочке VS](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	Это гарантирует, что приложение сможет создавать всплывающие уведомления.

###<a id="update-scripts"></a> Обновление серверных скриптов для отправки push-уведомлений

Наконец, необходимо обновить скрипт, зарегистрированный для операции вставки в таблице TodoItem для отправки уведомлений.

1. На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][21]

2. В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][22]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3. Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:
          
          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {                      
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';            

              // Write the default response and send a notification
              // to all platforms.            
              push.send(null, payload, {               
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },              
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }           
               });                 
              }
           });   
          }


    При этом регистрируется новый скрипт вставки, который отправляет push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.

3. Откройте вкладку **Push-уведомления**, установите флажок **Включить push-уведомления без проверки подлинности**, затем щелкните **Сохранить**.

	Это позволяет мобильной службе подключаться к MPNS в режиме без проверки подлинности для отправки push-уведомлений.

	>[AZURE.NOTE]В этом учебнике используется MPNS в режиме без проверки подлинности. В этом режиме MPNS ограничивает количество уведомлений, которые могут быть отправлены в канал устройства. Чтобы снять это ограничение, необходимо создать и отправить сертификат, щелкнув **Отправить** и выбрав нужный сертификат. Дополнительные сведения о создании сертификата см. в разделе [Настройка веб-службы с проверкой подлинности для отправки push-уведомлений в Windows Phone].

###<a id="test"></a> Тестирование push-уведомлений в приложении

1. В Visual Studio нажмите клавишу F5, чтобы запустить приложение.

    >[AZURE.NOTE]При проверке в эмуляторе Windows Phone может возникнуть исключение "401 Не санкционировано" RegistrationAuthorizationException. Это может произойти при выполнении вызова `RegisterNativeAsync()` из-за способа синхронизации часов в эмуляторе Windows Phone с главным компьютером. В результате маркер безопасности может быть не принят. Чтобы устранить эту проблему, вручную переведите часы в эмуляторе перед тестированием.

5. В приложении создайте новую задачу с названием **Hello push**, а затем сразу же нажмите кнопку «Запустить» или кнопку «Назад», чтобы выйти из приложения.

  	При этом в мобильную службу будет отправлен запрос на сохранение добавленного элемента. Заметьте, что устройство получает всплывающее уведомление с текстом **hello push**.

	![Получено всплывающее уведомление](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]Вы не получите уведомление, пока находитесь в приложении. Чтобы получать всплывающие уведомления, когда приложение активно, необходимо обработать событие [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx).
   
<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Служба push-уведомлений Apple]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Подготовка устройства Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[портал управления Azure]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Компонент клиента Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[клиентский компонент Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Начальный образец push-уведомления Azure для приложения Xamarin.Forms]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[Готовый образец push-уведомления Azure для приложения Xamarin.Forms ]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms
 

<!---HONumber=August15_HO6-->