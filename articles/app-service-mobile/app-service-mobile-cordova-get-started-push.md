---
title: "Добавление push-уведомлений в приложение Apache Cordova с помощью функции \"Мобильные приложения\" службы приложений Azure | Документация Майкрософт"
description: "Использование функции \"Мобильные приложения\" для отправки push-уведомлений в приложение Apache Cordova."
services: app-service\mobile
documentationcenter: javascript
manager: crdun
editor: 
author: conceptdev
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 6af5fa51f2e6553431b9f0aa2dbb368651e7e209
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Добавление push-уведомлений в приложение Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Обзор
В этом руководстве описывается добавление push-уведомлений в [ознакомительный проект Apache Cordova][5], чтобы при вставке каждой новой записи отправлялось push-уведомление.

Если вы не используете скачанный ознакомительный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure][1].

## <a name="prerequisites"></a>Предварительные требования
В этом руководстве предполагается наличие приложения Apache Cordova, разработанного с помощью Visual Studio 2015. Можно использовать Google Android Emulator, устройство с Android, Windows или iOS.

Для работы с этим учебником необходимы указанные ниже компоненты.

* Компьютер с [Visual Studio Community 2015][2] или более поздней версии. 
* [Набор средств Visual Studio для Apache Cordova][4].
* [Активная учетная запись Azure][3].
* Завершенный [ознакомительный проект Apache Cordova][5].
* Для Android — [учетная запись Google][6] с подтвержденным адресом электронной почты.
* Для iOS — [участие в программе разработки решений для Apple][7] и устройство iOS (симулятор iOS не поддерживает push-уведомления).
* Для Windows — [учетная запись разработчика приложений для Магазина Windows][8] и устройство с Windows 10.

## <a name="configure-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Просмотрите видео, в котором показаны действия, описанные в этом разделе][9].

## <a name="update-the-server-project"></a>Обновление серверного проекта
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Изменение приложения Cordova
Чтобы убедиться, что проект приложения Apache Cordova готов для обработки push-уведомлений, установите подключаемый модуль push-уведомлений Cordova, а также все службы push-уведомлений для конкретной платформы.

#### <a name="update-the-cordova-version-in-your-project"></a>Обновление версии Cordova в проекте
Если в проекте используется более ранняя версия Apache Cordova, чем 6.1.1, обновите клиентский проект. Чтобы обновить проект, сделайте следующее. 

* Чтобы открыть конструктор конфигураций, щелкните правой кнопкой мыши файл `config.xml`.
* Перейдите на вкладку **Платформы**.
* В текстовом поле **Cordova CLI** выберите **6.1.1**. 
* Чтобы обновить проект, выберите **Построение**, а затем щелкните **Собрать решение**.

#### <a name="install-the-push-plugin"></a>Установка подключаемого модуля push-уведомлений
Приложения Apache Cordova не обрабатывают возможностей устройства или сети изначально.  Эти возможности обеспечиваются подключаемыми модулями, которые публикуются в [npm][10] или на портале GitHub. Подключаемый модуль `phonegap-plugin-push` обрабатывает push-уведомления сети.

Подключаемый модуль push-уведомлений можно установить одним из следующих способов.

**из командной строки:**

Выполните следующую команду:

    cordova plugin add phonegap-plugin-push

**в Visual Studio:**

1. В обозревателе решений откройте файл `config.xml`. Затем выберите **Plugins** (Подключаемые модули) > **Custom** (Пользовательские). Используйте **Git** в качестве источника установки. 
    
2. В качестве источника введите `https://github.com/phonegap/phonegap-plugin-push`.

    ![Открытие файла config.xml в обозревателе решений][img1]

3. Щелкните стрелку рядом с источником установки.

4. В поле **SENDER_ID** добавьте числовой идентификатор проекта Google Developer Console, если он уже есть. В противном случае введите значение заполнителя, например 777777. Если планируется использовать устройство Android, это значение можно позже обновить в файле config.xml.

    >[!NOTE]
    >Начиная с версии 2.0.0, файл google-services.json необходимо устанавливать в корневую папку проекта, чтобы настроить идентификатор отправителя. Дополнительные сведения см. в [документации по установке](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md).
5. Выберите **Добавить**.

Теперь подключаемый модуль push-уведомлений установлен.

#### <a name="install-the-device-plugin"></a>Установка подключаемого модуля устройства
Выполните те же действия, что и для установки подключаемого модуля push-уведомлений. Добавьте подключаемый модуль устройства из списка основных подключаемых модулей. (Чтобы найти его, выберите **Plugins** (Подключаемые модули) > **Core** (Основные).) Этот подключаемый модуль требуется, чтобы получить имя платформы.

#### <a name="register-your-device-when-the-application-starts"></a>Регистрация устройства при запуске приложения 
Сначала мы добавим минимальный требуемый код для Android. Позже можно внести изменения в приложение для запуска в iOS или Windows 10.

1. Добавьте вызов **egisterForPushNotifications** в обратный вызов при входе в систему. Кроме того, его можно добавить в конец метода **onDeviceReady**.

        // Log in to the service.
        client.login('google')
            .then(function () {
                // Create a table reference.
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems.
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item.
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    В этом примере показан вызов метода **registerForPushNotifications** после успешной проверки подлинности. Метод `registerForPushNotifications()` можно вызывать каждый раз при необходимости.

2. Добавьте новый метод **registerForPushNotifications** , как показано ниже.

        // Register for push notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }
3. Для Android: в приведенном выше коде замените значение `Your_Project_ID` числовым идентификатором проекта своего приложения, полученным на сайте [Google Developer Console][18].

## <a name="optional-configure-and-run-the-app-on-android"></a>Настройка и запуск приложения в Android (необязательно)
В этом разделе описано, как включить поддержку push-уведомлений для платформы Android.

#### <a name="enable-gcm"></a>Включение Firebase Cloud Messaging
Так как изначально проект предназначен для платформы Google Android, необходимо включить Firebase Cloud Messaging.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>Настройка серверной части мобильного приложения для отправки push-запросов с использованием FCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Настройка приложения Cordova для Android
В приложении Cordova откройте файл config.xml. Затем замените значение `Your_Project_ID` числовым идентификатором проекта приложения, полученным в консоли [Google Developer Console][18].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Откройте файл index.js. Добавьте в код числовой идентификатор проекта.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Настройка устройства Android для отладочного режима USB
Перед развертыванием приложения на устройстве Android необходимо включить отладку USB. На телефоне Android сделайте следующее.

1. Выберите **Настройки** > **О телефоне**. Коснитесь пункта **Build number** (Номер сборки) и дождитесь включения режима разработчика (примерно семь секунд).
2. Выберите **Настройки** > **Developer Options** (Параметры разработчика) и включите **отладочный режим USB**. Затем подключите телефон Android к компьютеру для разработки с помощью кабеля USB.

Мы проверили этот способ, используя устройство Google Nexus 5X с Android 6.0 (Marshmallow). Все описанные приемы подходят для любой современной версии платформы Android.

#### <a name="install-google-play-services"></a>Установка служб Google Play
Подключаемый модуль push-уведомлений передает push-уведомления через службы Google Play Android.

1. В Visual Studio выберите **Инструменты** > **Android** > **Диспетчер пакетов SDK Android**. Разверните папку **Extras** (Дополнительно). Установите флажки напротив следующих пакетов SDK.

   * Android 2.3 или более поздней версии.
   * Репозиторий Google версии 27 или более поздней.
   * Службы Google Play 9.0.2 или более поздней.

2. Выберите элемент **Установить пакеты**. Затем дождитесь завершения процесса установки.

Необходимые на данный момент библиотеки перечислены в [документации по установке подключаемого модуля push-уведомлений PhoneGap][19].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Тестирование push-уведомлений в приложении для Android
Теперь вы можете проверить работу push-уведомлений путем запуска приложения и вставки элементов в таблицу TodoItem. Проверку можно выполнить на том же (или другом) устройстве при условии, что используется одна серверная часть. Проверьте работу приложения Cordova на платформе Android одним из следующих способов.

* *На физическом устройстве.* Подключите устройство Android к компьютеру разработчика с помощью USB-кабеля.  Вместо **Эмулятор Google Android** выберите **Устройство**. Visual Studio развернет приложение на устройстве и запустит его. После этого вы сможете работать с приложением на устройстве.

  В разработке приложений Android вам помогут приложения для демонстрации экрана, например [Mobizen][20]. Приложение Mobizen транслирует экран Android в веб-браузер на компьютере.

* *На эмуляторе Android.* Для использования эмулятора необходимо выполнить дополнительную настройку.

    Убедитесь, что для развертывания используется виртуальное устройство, на котором в качестве места назначения заданы интерфейсы Google API, как показано в диспетчере виртуальных устройств Android (AVD).

    ![Диспетчер виртуальных устройств Android](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Если вы хотите использовать более быстрый эмулятор x86, [установите драйвер HAXM][11], а затем настройте его использование в эмуляторе.

    Добавьте учетную запись Google на устройстве Android, щелкнув **Приложения** > **Настройки** > **Add account** (Добавить учетную запись). Затем следуйте инструкциям на экране.

    ![Добавление учетной записи Google на устройстве Android](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Запустите приложение todolist, как ранее, и вставьте новый элемент списка дел. На этот раз в области уведомлений отображается значок уведомления. Вы можете открыть панель уведомлений, чтобы просмотреть полный текст уведомления.

    ![Просмотр уведомления](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>Настройка и запуск проекта в iOS (необязательно)
В этом разделе описано, как запустить проект Cordova для устройств под управлением iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Установка и запуск агента удаленной сборки iOS на компьютере Mac или в облачной службе
Прежде чем запускать приложение Cordova в iOS с помощью Visual Studio, выполните действия из [руководства по установке iOS][12], чтобы установить и запустить агент удаленной сборки.

Убедитесь, что вы можете создать приложение для iOS. Приведенные в этом руководстве шаги по установке необходимы для сборки приложения для iOS в Visual Studio. Если у вас нет компьютера Mac, можно использовать агент удаленной сборки в такой службе, как MacInCloud. Дополнительные сведения см. в статье [Build and simulate a Cordova iOS app in the cloud][21] (Сборка и имитация приложения Cordova на iOS в облаке).

> [!NOTE]
> XCode 7 или более поздней версии требуется для использования подключаемого модуля push-уведомлений в iOS.

#### <a name="find-the-id-to-use-as-your-app-id"></a>Поиск идентификатора, который будет использоваться как идентификатор приложения
Прежде чем зарегистрировать приложение для работы с push-уведомлениями, откройте файл config.xml в приложении Cordova, найдите значение атрибута `id` в элементе мини-приложения, а затем скопируйте его для последующего использования. В приведенном ниже коде XML идентификатором выступает `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Вам потребуется использовать этот идентификатор при создании идентификатора приложения на портале разработчика Apple. Если на портале разработчика вы создали другой идентификатор приложения, вам понадобится выполнить дополнительные действия, описанные далее в этом руководстве. Идентификатор элемента мини-приложения должен соответствовать идентификатору приложения на портале разработчика.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Зарегистрируйте приложение для push-уведомлений на портале разработчика Apple.
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>Настройка Azure для отправки push-уведомлений
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>Проверка, соответствует ли идентификатор приложения указанному в приложении Cordova
Если идентификатор приложения, созданный в вашей учетной записи разработчика Apple, совпадает с идентификатором элемента мини-приложения в файле config.xml, этот шаг можно пропустить. Однако если идентификаторы не совпадают, сделайте следующее:

1. Удалите папку platforms из проекта.
2. Удалите папку plugins из проекта.
3. Удалите папку node_modules из проекта.
4. В качестве значения атрибута идентификатора для элемента мини-приложения в файле config.xml укажите идентификатор приложения, созданный в вашей учетной записи разработчика Apple.
5. Перестройте свой проект.

##### <a name="test-push-notifications-in-your-ios-app"></a>Тестирование push-уведомлений в приложении iOS
1. В Visual Studio убедитесь, что в качестве цели развертывания выбрано **iOS**. Затем выберите **Устройство** для запуска push-уведомлений на подключенном устройстве iOS.

    Вы можете запустить push-уведомления на устройстве iOS, подключенном к ПК с помощью iTunes. Симулятор iOS не поддерживает push-уведомления.

2. Нажмите кнопку **Запуск** или клавишу **F5** в Visual Studio, чтобы выполнить сборку проекта и запустить приложение на устройстве iOS. Затем нажмите кнопку **ОК**, чтобы принять push-уведомления.

   > [!NOTE]
   > При первом запуске приложение запросит подтверждение для приема push-уведомлений.

3. В приложении введите задачу, а затем щелкните знак "плюс" **(+)**.
4. Должно отобразиться соответствующее уведомление. Нажмите кнопку **ОК**, чтобы закрыть уведомление.

## <a name="optional-configure-and-run-on-windows"></a>Настройка и запуск проекта в Windows (необязательно)
В этом разделе описывается запуск проекта приложения Apache Cordova на устройствах с Windows 10 (в Windows 10 поддерживается подключаемый модуль push-уведомлений PhoneGap). Пропустите этот раздел, если вы не работаете с устройствами Windows.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>Регистрация приложения Windows для получения push-уведомлений с помощью WNS
Чтобы использовать возможности Store в Visual Studio, выберите версию платформы Windows из списка платформ решения, например **Windows-x64** или **Windows-x86**. (Не используйте **Windows-AnyCPU** для push-уведомлений.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Просмотрите видео, демонстрирующее аналогичные действия][13]

#### <a name="configure-the-notification-hub-for-wns"></a>Настройка концентратора уведомлений для WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Настройка поддержки push-уведомлений Windows в приложении Cordova
Щелкните правой кнопкой мыши файл **config.xml**, чтобы открыть конструктор конфигураций. Выберите **Конструктор представлений**. Затем перейдите на вкладку **Windows** и выберите **Windows 10** в разделе **Целевая версия Windows**.

Для поддержки push-уведомлений в сборках по умолчанию (отладка) откройте файл build.json. Скопируйте конфигурацию в разделе release в конфигурацию отладки.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

После обновления файл build.json должен содержать следующий код.

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Выполните сборку приложения и убедитесь в отсутствии ошибок. Теперь клиентское приложение должно зарегистрироваться для получения уведомлений из серверной части мобильных приложений. Повторите действия из этого раздела для каждого проекта Windows, входящего в ваше решение.

#### <a name="test-push-notifications-in-your-windows-app"></a>Тестирование push-уведомлений в приложении Windows
В Visual Studio выберите платформу Windows, например **Windows-x64** или **Windows-x86**, в качестве целевого объекта развертывания. Чтобы запустить приложение на компьютере с Windows 10, на котором имеется приложение Visual Studio, выберите **Локальный компьютер**.

1. Нажмите кнопку **Запуск** для создания проекта и запуска приложения.

2. В приложении введите имя нового объекта TodoItem и добавьте его, щелкнув значок плюса **(+)**.

После добавления элемента должно отобразиться соответствующее уведомление.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о push-уведомлениях см. в статье о [центрах уведомлений][17].
* Продолжите работу с руководством и [добавьте проверку подлинности][14] в приложение Apache Cordova, если вы этого еще не сделали.

Сведения об использовании следующих пакетов SDK:

* [Использование клиентской библиотеки Apache Cordova для мобильных приложений Azure][15]
* [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure][1]
* [Использование пакета SDK Node.js для мобильных приложений Azure][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: http://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: http://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: http://taco.visualstudio.com/en-us/docs/build_ios_cloud/
