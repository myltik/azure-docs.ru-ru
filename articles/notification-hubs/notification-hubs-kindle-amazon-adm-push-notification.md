---
title: Отправка push-уведомлений в приложения Kindle с помощью Центров уведомлений Azure | Документация Майкрософт
description: Из этого учебника вы узнаете, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Kindle.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 346fc8e5-294b-4e4f-9f27-7a82d9626e93
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-kindle
ms.devlang: Java
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: af2619a403046bd4f064b958df225e4d42a205f4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Приступая к работе с Центрами уведомлений для приложений Kindle
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

В этом учебнике показано, как использовать Центры уведомлений Azure для отправки push-уведомлений в приложение Kindle. Вы создадите пустое приложение Kindle, которое получает push-уведомления с помощью Amazon Device Messaging (ADM).

В рамках этого руководства создается или обновляется код для выполнения следующих задач: 

> [!div class="checklist"]
> * Добавление нового приложения на портал разработчика
> * Создание ключа API
> * Добавление учетных данных для центра
> * Настройка приложения
> * Создание обработчика сообщений ADM
> * Добавление ключа API в приложение
> * Запуск приложения
> * отправка проверочного уведомления. 

## <a name="prerequisites"></a>предварительным требованиям

* Скачайте пакет SDK для Android (предполагается, что вы используете Eclipse) с <a href="http://go.microsoft.com/fwlink/?LinkId=389797">сайта Android</a>.
* Следуйте указаниям, приведенным в статье <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Настройка среды разработки</a>, для настройки среды разработки для Kindle.

## <a name="add-a-new-app-to-the-developer-portal"></a>Добавление нового приложения на портал разработчика
1. Для начала создайте приложение на [портале разработчика Amazon].
   
    ![][0]
2. Скопируйте **ключ приложения**.
   
    ![][1]
3. На портале щелкните название вашего приложения, затем перейдите на вкладку **Device Messaging** .
   
    ![][2]
4. Щелкните **Создать новый профиль безопасности**, затем создайте профиль безопасности (например, **Профиль безопасности TestAdm**). Нажмите кнопку **Сохранить**.
   
    ![][3]
5. Щелкните **Security Profiles** (Профили безопасности), чтобы просмотреть только что созданный профиль. Скопируйте значения **Код клиента** и **Секрет клиента** для последующего использования.
   
    ![][4]

## <a name="create-an-api-key"></a>Создание ключа API
1. Откройте окно командной строки с правами администратора.
2. Перейдите в папку пакета Android SDK.
3. Введите следующую команду:
   
        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore
   
    ![][5]
4. Для пароля **keystore** введите **android**.
5. Скопируйте отпечаток **MD5** .
6. Вернувшись на портал разработчика, на вкладке **Обмен сообщениями** щелкните **Android/Kindle**, введите имя пакета для вашего приложения (например, **com.sample.notificationhubtest**) и значение **MD5**, а затем щелкните **Создать ключ API**.

## <a name="add-credentials-to-the-hub"></a>Добавление учетных данных для центра
На портале добавьте секрет клиента и код клиента на вкладку **Настройка** центра уведомлений.

## <a name="set-up-your-application"></a>Настройка приложения
> [!NOTE]
> При создании приложения используйте API уровня не ниже 17.

Добавьте библиотеки ADM в проект Eclipse.

1. Чтобы получить библиотеку ADM, [загрузите пакет SDK]. Распакуйте ZIP-файл пакета SDK.
2. В Eclipse щелкните правой кнопкой мыши по проекту и выберите **Свойства**. Слева выберите **Java Build Path** (Путь сборки Java), а затем вверху откройте вкладку **Libraries** (Библиотеки). Щелкните **Add External Jar** (Добавить внешний JAR-файл) и выберите файл `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` из каталога, в который вы распаковали пакет SDK Amazon.
3. Загрузите пакет NotificationHubs Android SDK (ссылка).
4. Извлеките содержимое пакета, а затем перетащите файл `notification-hubs-sdk.jar` в папку `libs` в Eclipse.

Изменение манифеста приложения для поддержки ADM

1. Добавьте пространство имен Amazon в корневом элементе манифеста.

    ```xml
        xmlns:amazon="http://schemas.amazon.com/apk/res/android"
    ```

1. Добавьте разрешения в качестве первого элемента в элементе манифеста. Вместо **[YOUR PACKAGE NAME]** укажите пакет, который вы используете для создания приложения.
   
    ```xml
        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />
   
        <uses-permission android:name="android.permission.INTERNET"/>
   
        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />
   
        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />
   
        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />
    ```
2. Вставьте приведенный ниже элемент в качестве первого потомка элемента приложения. Не забудьте заменить **[YOUR SERVICE NAME]** именем обработчика сообщений ADM, который будет создан в следующем разделе (включая пакет), а **[YOUR PACKAGE NAME]** именем пакета, с помощью которого создается приложение.
   
    ```xml
        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />
   
        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />
   
            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >
   
            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />
   
          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>
    ```

## <a name="create-your-adm-message-handler"></a>Создание обработчика сообщений ADM
1. Создайте новый класс с наследованием от `com.amazon.device.messaging.ADMMessageHandlerBase` и назовите его `MyADMMessageHandler`, как показано на следующем рисунке.
   
    ![][6]
2. Добавьте следующие операторы `import` :
   
    ```java
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub
    ```
3. Добавьте в созданный класс приведенный ниже код. Не забудьте подставить имя центра и строку подключения (listen).
   
    ```java
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
          private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }
   
        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }
   
            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }
   
            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();
   
                mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
   
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                  new Intent(ctx, MainActivity.class), 0);
   
            NotificationCompat.Builder mBuilder =
                  new NotificationCompat.Builder(ctx)
                  .setSmallIcon(R.mipmap.ic_launcher)
                  .setContentTitle("Notification Hub Demo")
                  .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                  .setContentText(msg);
   
             mBuilder.setContentIntent(contentIntent);
             mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    ```
4. Добавьте в метод `OnMessage()` следующий код:
   
    ```java
        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);
    ```
5. Добавьте в метод `OnRegistered` следующий код:
   
    ```java
        try {
            getNotificationHub(getApplicationContext()).register(registrationId);
        } catch (Exception e) {
            Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
        }
    ```
6. Добавьте в метод `OnUnregistered` следующий код:
   
    ```java
         try {
             getNotificationHub(getApplicationContext()).unregister();
         } catch (Exception e) {
             Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
         }
    ```
7. Затем в методе `MainActivity` добавьте следующую инструкцию import:
   
    ```java
        import com.amazon.device.messaging.ADM;
    ```
8. В конец метода `OnCreate` добавьте следующий код:
   
    ```java
        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                         MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }
    ```
    
## <a name="add-your-api-key-to-your-app"></a>Добавление ключа API в приложение
1. В Eclipse создайте новый файл с именем **api_key.txt** в активах каталога проекта.
2. Откройте файл и скопируйте ключ API, созданный на портале разработчика Amazon.

## <a name="run-the-app"></a>Запуск приложения
1. Запустите эмулятор.
2. В эмуляторе сверху щелкните **Параметры**, затем **Моя учетная запись** и зарегистрируйтесь с использованием действующей учетной записи Amazon.
3. В Eclipse запустите приложение.

> [!NOTE]
> Если возникает проблема, проверьте время эмулятора (или устройства). Значение времени должно быть точным. Для изменения времени эмулятора Kindle выполните следующую команду из каталога средств для платформы Android SDK:
> 

```
adb shell  date -s "yyyymmdd.hhmmss"
```

## <a name="send-a-notification-message"></a>Отправка уведомления

Чтобы отправить сообщение с помощью .NET, используйте следующий код:

```csharp
static void Main(string[] args)
{
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

    hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
}
```

![][7]

## <a name="next-steps"></a>Дополнительная информация
В рамках этого руководства вы отправили широковещательные уведомления на все устройства Kindle, зарегистрированные в серверной части. Чтобы научиться отправлять push-уведомления на конкретные устройства Kindle, перейдите к следующему руководству (в следующем руководстве объясняется, как отправлять push-уведомления на конкретные устройства Android, но можно применить ту же логику при отправке push-уведомлений на конкретные устройства Kindle). 

> [!div class="nextstepaction"]
>[Отправка push-уведомлений на конкретные устройства](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- URLs. -->
[портале разработчика Amazon]: https://developer.amazon.com/home.html
[загрузите пакет SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
