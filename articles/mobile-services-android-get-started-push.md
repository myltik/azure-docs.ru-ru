<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="Get started with push notifications (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Приступая к работе с push-уведомлениями в мобильных службах (принудительная отправка устаревшего типа)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-dotnet" title="Магазин Windows C#">Магазин Windows C#</a>
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-js" title="Магазин Windows JavaScript">Магазин Windows JavaScript</a>
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/ru-ru/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ru-ru/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/ru-ru/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ru-ru/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Серверная часть .NET">Серверная часть .NET</a> | <a href="/ru-ru/documentation/articles/mobile-services-android-get-started-push/"  title="Серверная часть JavaScript" class="current">Серверная часть JavaScript</a></div>

В этом разделе показано, как использовать мобильные службы Azure для отправки push-уведомлений в приложение Android. В этом учебнике вам предстоит добавить push-уведомления в проект быстрого запуска с помощью службы Google Cloud Messaging (GCM). По завершении работы ваша мобильная служба будет отправлять push-уведомление каждый раз при вставке записи.

> [WACOM.NOTE]Указания в этом разделе приведены с учетом использования *имеющихся* мобильных служб, которые *пока еще не были обновлены* до версии, поддерживающей интеграцию с центрами уведомлений. При создании *новой* мобильной службы интегрированные функции включаются автоматически. Информацию о работе с мобильными службами последних версий см. в разделе [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].
>
> Мобильные службы теперь интегрируются с центрами уведомлений Azure для поддержки дополнительных функций push-уведомлений, таких как шаблоны, использование нескольких платформ и лучшее масштабирование. *Если это возможно, обновите имеющиеся мобильные службы, чтобы использовать центры уведомлений*. После обновления ознакомьтесь с этой версией раздела [Приступая к работе с push-уведомлениями][Приступая к работе с push-уведомлениями].

В этом учебнике рассматриваются следующие основные шаги для включения push-уведомлений:

-   [Включение Google Cloud Messaging][Включение Google Cloud Messaging]
-   [Настройка мобильных служб][Настройка мобильных служб]
-   [Добавление push-уведомлений в приложение][Добавление push-уведомлений в приложение]
-   [Обновление скриптов для отправки push-уведомлений][Обновление скриптов для отправки push-уведомлений]
-   [Вставка данных для получения уведомлений][Вставка данных для получения уведомлений]

Для работы с данным учебником требуется следующее:

-   [Android SDK для мобильных служб][Android SDK для мобильных служб]
-   Активная учетная запись Google

Этот учебник создан на основе краткого руководства по мобильным службам. Перед началом работы с учебником необходимо пройти задания учебника [Приступая к работе с мобильными службами][Приступая к работе с мобильными службами].

## <span id="register"></span></a>Включение Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Далее вы будете использовать это значение ключа API, чтобы предоставить мобильным службам возможность выполнять аутентификацию с использованием GCM и отправлять push-уведомления от имени вашего приложения.

## <span id="configure"></span></a>Настройка мобильных служб для отправки push-запросов

1.  Выполните вход на [портал управления Azure][портал управления Azure], щелкните элемент **Мобильные службы**, а затем щелкните свое приложение.

    ![][0]

2.  Перейдите на вкладку **Push-уведомления**, введите значение **Ключ API**, полученное от GCM при выполнении предыдущей процедуры, а затем нажмите кнопку **Сохранить**.

    ![][1]

Ваша мобильная служба теперь настроена для работы с GCM для отправки push-уведомлений.

## <span id="add-push"></span></a>Добавление push-уведомлений в приложение

### Добавление служб Google Play в проект

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### Добавление кода

1.  Откройте файл проекта **AndroidManifest.xml**. Google Cloud Messaging предъявляет некоторые требования к минимальному уровню API для разработки и тестирования, которым должно удовлетворять свойство **minSdkVersion** в манифесте. Обратитесь к руководству [Настройка пакета SDK служб Google Play][Настройка пакета SDK служб Google Play], чтобы определить, насколько малым можно задать это значение, если вам требуется задать его ниже 16, поскольку вы используете более старое устройство. Задайте значение этого свойства соответствующим образом.

2.  Убедитесь, что в элементе `uses-sdk` для **targetSdkVersion** задан номер установленной платформы пакета SDK (шаг 1). Рекомендуется задать новейшую версию.

3.  В зависимости от параметров, заданных на предыдущих этапах, тег **uses-sdk** может выглядеть следующим образом:

        <uses-sdk
            android:minSdkVersion="17"
            android:targetSdkVersion="19" />

4.  В коде в следующих двух действиях замените *`**my_app_package**`* именем пакета приложения для вашего проекта, которое является значением атрибута `package` тега `manifest`.

5.  Добавьте следующие новые разрешения после существующего элемента `uses-permission`:

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6.  Добавьте следующий код после открывающего тега `application`:

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

7.  Откройте файл ToDoItem.java и добавьте следующий код в класс **TodoItem**:

            @com.google.gson.annotations.SerializedName("handle")
            private String mHandle;

            public String getHandle() {
                return mHandle;
            }

            public final void setHandle(String handle) {
                mHandle = handle;
            }

    Этот код создает новое свойство, содержащее идентификатор регистрации.

    <div class="dev-callout"><b>Примечание.</b>
<p>Когда в вашей мобильной службе включена динамическая схема, при вставке нового элемента, содержащего это свойство, в таблицу <strong>TodoItem</strong> автоматически добавляется новый столбец <strong>handle</strong>.</p>
</div>

8.  Загрузите и распакуйте [Android SDK мобильных служб][Android SDK для мобильных служб], откройте папку **notifications**, скопируйте файл **notifications-1.0.1.jar** в папку *libs* вашего проекта Eclipse и обновите папку *libs*.

    <div class="dev-callout"><b>Примечание.</b>
<p>Числа в конце этого имени файла могут меняться в последующих выпусках SDK.</p>
</div>

9.  Откройте файл *ToDoItemActivity.java* и добавьте следующий оператор импорта:

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. Добавьте в класс следующую закрытую переменную, где *`<PROJECT_NUMBER>`* — номер проекта, назначенный Google вашему приложению в предыдущей процедуре:

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. В методе **onCreate** перед созданием экземпляра MobileServiceClient добавьте следующий код, который регистрирует обработчик уведомлений для устройства:

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. Добавьте следующую строку кода в метод **addItem** перед вставкой элемента в таблицу:

        item.setHandle(MyHandler.getHandle());

    Этот код задает для свойства `handle` элемента значение, совпадающее с идентификатором регистрации устройства.

13. В обозревателе пакетов щелкните правой кнопкой мыши пакет (под узлом `src`), щелкните команду **Создать**, выберите **Класс**.

14. В поле **Имя** введите `MyHandler`, в поле **Суперкласс** укажите `com.microsoft.windowsazure.notifications.NotificationsHandler`, затем щелкните кнопку **Готово**

    ![][2]

    При этом создается новый класс MyHandler.

15. Добавьте следующие операторы импорта:

        import android.content.Context;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. Добавьте следующий код:

        @com.google.gson.annotations.SerializedName("handle")
        private static String mHandle;

        public static String getHandle() {
            return mHandle;
        }

        public static final void setHandle(String handle) {
            mHandle = handle;
        }

17. Переопределите существующий метод **onRegistered** с использованием следующего кода:

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            setHandle(gcmRegistrationId);
        }

Ваше приложение теперь обновлено для поддержки push-уведомлений.

## <span id="update-scripts"></span></a>Обновление зарегистрированных скриптов вставки на портале управления

1.  На портале управления щелкните вкладку **Данные**, а затем щелкните таблицу **TodoItem**.

    ![][3]

2.  В **todoitem** перейдите на вкладку **Скрипт** и выберите **Вставка**.

    ![][4]

    При этом отображается функция, вызываемая при выполнении вставки в таблице **TodoItem**.

3.  Замените функцию вставки следующим кодом и нажмите кнопку **Сохранить**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) {
                            console.log('Push notification sent: ', response);
                        }, error: function(error) {
                            console.log('Error sending push notification: ', error);
                        }
                    });
                }
            });
        }

    При этом регистрируется новый скрипт вставки, в котором используется [объект gcm][объект gcm] для отправки push-уведомления (вставленный текст) на устройство, указанное в запросе вставки.

## <span id="test"></span></a>Тестирование push-уведомлений в приложении

<div class="dev-callout"><b>Примечание.</b>
    <p>При запуске этого приложения в эмуляторе убедитесь, что используется виртуальное устройство на платформе Android (AVD), поддерживающее API-интерфейсы Google.</p>
</div>

1.  Перезапустите Eclipse, в обозревателе пакетов щелкните правой кнопкой мыши проект, щелкните **Свойства**, выберите **Android**, установите флажок **API-интерфейсы Google**, а затем нажмите кнопку **ОК**.

    ![][5]

    Проект будет предназначен для API-интерфейсов Google.

2.  В **Окно** выберите **Диспетчер виртуальных устройств Android**, выберите ваше устройство, щелкните **Изменить**.

    ![][6]

3.  Выберите **API-интерфейсы Google** в **Цель**, а затем нажмите кнопку ОК.

    ![][7]

    При этом виртуальное устройство на платформе Android будет предназначено для использования API-интерфейсов Google.

4.  В меню **Выполнить** щелкните **Выполнить** для запуска приложения.

5.  В приложении введите содержательный текст (например, *Новая задача для мобильных служб*, а затем нажмите кнопку **Добавить**.

    ![][8]

6.  В нижней части экрана появится окно уведомления черного цвета.

    ![][9]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.    ![][27]-->

Вы успешно завершили ознакомление с данным учебником.

## <a name="next-steps"></a>Дальнейшие действия

В этом простом примере пользователь получает push-уведомление с только что вставленными данными. Маркер устройства, используемый GCM, передается клиентом мобильной службе в запросе. В следующем учебнике [Принудительная отправка уведомлений пользователям приложения][Принудительная отправка уведомлений пользователям приложения] вы будете создавать отдельную таблицу "Устройства" для хранения маркеров устройств и отправлять push-уведомление всем сохраненным каналам при выполнении вставки.



  [Приступая к работе с push-уведомлениями]: /ru-ru/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Включение Google Cloud Messaging]: #register
  [Настройка мобильных служб]: #configure
  [Добавление push-уведомлений в приложение]: #add-push
  [Обновление скриптов для отправки push-уведомлений]: #update-scripts
  [Вставка данных для получения уведомлений]: #test
  [Android SDK для мобильных служб]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Приступая к работе с мобильными службами]: /ru-ru/develop/mobile/tutorials/get-started-android
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [портал управления Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [Настройка пакета SDK служб Google Play]: http://go.microsoft.com/fwlink/?LinkId=389801
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [4]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [объект gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [8]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [9]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
  [Принудительная отправка уведомлений пользователям приложения]: /ru-ru/develop/mobile/tutorials/push-notifications-to-users-android
