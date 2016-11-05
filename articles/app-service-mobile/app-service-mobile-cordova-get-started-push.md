---
title: Добавление push-уведомлений в приложение Apache Cordova с помощью мобильных приложений Azure | Microsoft Docs
description: Узнайте, как использовать мобильные приложения Azure для отправки push-уведомлений в приложение Apache Cordova.
services: app-service\mobile
documentationcenter: javascript
manager: ggailey777
editor: ''
author: adrianhall

ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 08/11/2016
ms.author: glenga

---
# Добавление push-уведомлений в приложение Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Обзор
В этом учебнике описывается добавление push-уведомлений в [ознакомительный проект Apache Cordova], чтобы при вставке каждой новой записи отправлялось push-уведомление. Этот учебник использует материал [ознакомительного проекта Apache Cordova], инструкции из которого необходимо выполнить в первую очередь. Если у вас есть серверная часть ASP.NET и вы не используете скачанный проект сервера быстрого запуска, в проект необходимо добавить пакет расширений для push-уведомлений. Дополнительную информацию о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure].

## <a name="prerequisites"></a>Предварительные требования
В этом руководстве используется приложение Apache Cordova, разработанное в Visual Studio 2015 и выполняемое в эмуляторе Google Android, а также устройства Android, Windows и iOS.

Для работы с этим учебником требуется:

* компьютер с [Visual Studio Community 2015] или более поздней версии;
* [набор средств Visual Studio для Apache Cordova];
* [Активная учетная запись Azure](https://azure.microsoft.com/pricing/free-trial/).
* выполненный [ознакомительный проект Apache Cordova]. Другие руководства (например, по [проверке подлинности]) могут быть пройдены раньше, но это не является обязательным условием;
* (для Android) [учетная запись Google] с подтвержденным адресом электронной почты и устройство Android;
* (для iOS) участие в программе разработки решений для Apple и устройство iOS (симулятор iOS не поддерживает push-уведомления);
* (для Windows) учетная запись разработчика приложений для Магазина Windows и устройство Windows 10.

Несмотря на то, что эмуляторы Android поддерживают push-уведомления, они работают нестабильно, поэтому тестирование push-уведомлений на эмуляторах не рекомендуется.

## <a name="create-hub"></a>Создание концентратора уведомлений
[!INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

## Обновление серверного проекта для отправки push-уведомлений
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Изменение настроек приложения Cordova для получения push-уведомлений
Необходимо убедиться в том, что проект приложения Apache Cordova готов для обработки push-уведомлений. Необходимо установить подключаемый модуль push-уведомлений Cordova, а также все службы push-уведомлений для конкретной платформы.

#### Обновление версии Cordova в проекте
Рекомендуется обновить версию Cordova в клиентском проекте до выпуска 6.1.1, если он настроен с использованием более старой версии. Чтобы обновить проект, щелкните файл config.xml правой кнопкой мыши и выберите конструктор конфигурации. Перейдите на вкладку "Платформы" и выберите 6.1.1 в текстовом поле **Cordova CLI**.

Чтобы обновить проект, выберите **Сборка**, а затем — **Собрать решение**.

#### Установка подключаемого модуля push-уведомлений
Приложения Apache Cordova не обрабатывают возможностей устройства или сети изначально. Эти возможности обеспечиваются подключаемыми модулями, которые публикуются в [npm](https://www.npmjs.com/) или на сайте GitHub. Подключаемый модуль `phonegap-plugin-push` используется для обработки push-уведомлений сети.

Подключаемый модуль push-уведомлений можно установить одним из следующих способов:

**из командной строки:**

Выполните следующую команду.

    cordova plugin add phonegap-plugin-push

**в Visual Studio:**

1. В обозревателе решений откройте файл `config.xml`, щелкните **Подключаемые модули** > **Настраиваемые**, выберите **Git** как источник установки, а затем в качестве источника введите `https://github.com/phonegap/phonegap-plugin-push`.
   
   ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)
2. Щелкните стрелку рядом с источником установки.
3. В поле **SENDER\_ID** добавьте числовой идентификатор проекта Google Developer Console, если он уже есть. В противном случае введите значение заполнителя, например 777777. Если планируется использовать устройство Android, это значение можно позже обновить в файле config.xml.
4. Щелкните **Добавить**.

Теперь подключаемый модуль push-уведомлений установлен.

#### Установка подключаемого модуля устройства
Выполните ту же процедуру, что и для установки подключаемого модуля push-уведомлений. Подключаемый модуль устройства содержится в списке основных подключаемых модулей (щелкните **Подключаемые модули** > **Основные**). Этот подключаемый модуль требуется, чтобы получить имя платформы (`device.platform`).

#### Регистрация устройства для получения push-уведомлений при запуске
Сначала мы добавим минимальный требуемый код для Android. Позднее мы внесем в него небольшие изменения для запуска на iOS или Windows 10.

1. Добавьте вызов метода **registerForPushNotifications** в обратный вызов при входе в систему или в конец метода **onDeviceReady**.
   
        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');
   
                // Refresh the todoItems
                refreshDisplay();
   
                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);
   
                    // Added to register for push notifications.
                registerForPushNotifications();
   
            }, handleError);
   
    В этом примере показан вызов **registerForPushNotifications** после успешной аутентификации, что рекомендуется при использовании в приложении push-уведомлений и аутентификации.
2. Добавьте новый метод **registerForPushNotifications**, как показано ниже.
   
        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
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
3. В приведенном выше коде замените значение `Your_Project_ID` числовым идентификатором проекта своего приложения, полученным на сайте [Google Developer Console] \(для Android).

## Настройка и запуск приложения в Android (необязательно)
В этом разделе описано, как включить поддержку push-уведомлений для платформы Android.

#### <a name="enable-gcm"></a>Включение Google Cloud Messaging
Так как изначально проект предназначен для платформы Google Android, необходимо включить Google Cloud Messaging. Если проект предназначен для устройств Microsoft Windows, включите поддержку WNS.

[!INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-4-Set-up-gcm-for-push)

#### <a name="configure-backend"></a>Настройка серверной части мобильного приложения для отправки push-запросов с использованием GCM
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### Настройка приложения Cordova для Android
В приложении Cordova откройте файл config.xml и замените значение `Your_Project_ID` числовым идентификатором проекта своего приложения, полученным на сайте [Google Developer Console].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Откройте файл index.js и добавьте в код числовой идентификатор своего проекта.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

#### <a name="configure-device"></a>Настройка устройства Android для отладочного режима USB
Перед развертыванием приложения на устройстве Android необходимо включить отладку USB. На телефоне Android выполните следующие действия:

1. Последовательно выберите пункты **Параметры** > **О телефоне**, коснитесь пункта **Номер сборки** и дождитесь включения режима разработчика (примерно 7 секунд).
2. Вернитесь в раздел **Параметры** > **Параметры разработчика**, включите **отладочный режим USB**, а затем подключите телефон Android к компьютеру разработки с помощью кабеля USB.

Мы проверили этот способ, используя устройство Google Nexus 5X с Android 6.0 (Marshmallow). Все описанные приемы подходят для любой современной версии платформы Android.

#### Установка служб Google Play
Подключаемый модуль push-уведомлений передает push-уведомления через службы Google Play Android.

1. В **Visual Studio** щелкните **Инструменты** > **Android** > **Android SDK Manager** (Диспетчер пакетов SDK для Android), разверните папку **Дополнения** и установите соответствующие флажки, чтобы обеспечить установку всех следующих пакетов SDK.
   
   * Репозиторий поддержки Android версии 20 или более поздней
   * Службы Google Play версии 27 или более поздней
   * Репозиторий Google версии 22 или более поздней
2. Щелкните **Install Packages** (Установить пакеты) и дождитесь завершения установки.

Необходимые на данный момент библиотеки перечислены в [документации по установке подключаемого модуля push-уведомлений PhoneGap].

#### Тестирование push-уведомлений в приложении для Android
Теперь вы можете проверить работу push-уведомлений путем запуска приложения и вставки элементов в таблицу TodoItem. Это делается на том же (или другом) устройстве при условии, что используется одна серверная часть. Проверьте работу приложения Cordova на платформе Android одним из следующих способов.

* **На физическом устройстве**. Подключите устройство Android к компьютеру разработчика с помощью кабеля USB. Вместо **Эмулятор Google Android** выберите **Устройство**. Visual Studio выполнит развертывание приложения на устройстве и запустит его. После этого вы сможете работать с приложением на устройстве. Усовершенствуйте интерфейс разработки. В разработке приложения Android вам помогут приложения для демонстрации экрана, например [Mobizen], транслирующие экран Android в браузер на компьютере.
* **На эмуляторе Android**. Для запуска в эмуляторе необходимо выполнить дополнительную настройку.
  
    Убедитесь, что для развертывания или отладки используется виртуальное устройство, на котором в качестве назначения заданы интерфейсы Google API, как показано ниже в диспетчере виртуальных устройств Android (AVD).
  
    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)
  
    Если вы хотите использовать более быстрый эмулятор x86, [установите драйвер HAXM](https://taco.visualstudio.com/ru-RU/docs/run-app-apache/#HAXM) и настройте его использование в эмуляторе.
  
    Добавьте учетную запись Google на устройство Android, щелкнув **Приложения** > **Параметры** > **Добавить учетную запись**. Следуйте указаниям, чтобы использовать на устройстве имеющуюся учетную запись Google (рекомендуется) или создать новую.
  
    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)
  
    Запустите приложение todolist, как ранее, и вставьте новый элемент списка дел. На этот раз в области уведомлений отображается значок уведомления. Вы можете открыть панель уведомлений, чтобы просмотреть полный текст уведомления.
  
    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## Настройка и запуск проекта в iOS (необязательно)
В этом разделе описано, как запустить проект Cordova для устройств под управлением iOS. Пропустите этот раздел, если вы не работаете с устройствами iOS.

#### Установка и запуск агента удаленной сборки iOS на Mac или в облачной службе
Прежде чем запускать приложение Cordova в iOS с помощью Visual Studio, выполните шаги в [руководстве по установке iOS](http://taco.visualstudio.com/ru-RU/docs/ios-guide/), чтобы установить и запустить агент удаленной сборки.

Убедитесь, что вы можете создать приложение для iOS. Приведенные в этом руководстве шаги по установке необходимы для сборки приложения для iOS в Visual Studio. Если у вас нет компьютера Mac, можно использовать агент удаленной сборки в такой службе, как MacInCloud. Дополнительные сведения см. в статье [Run your iOS app in the cloud](http://taco.visualstudio.com/ru-RU/docs/build_ios_cloud/) (Запуск приложения iOS в облаке).

> [!NOTE]
> XCode 7 или более поздней версии требуется для использования подключаемого модуля push-уведомлений в iOS.
> 
> 

#### Поиск идентификатора, который будет использоваться как идентификатор приложения
Прежде чем зарегистрировать приложение для работы с push-уведомлениями, откройте файл config.xml в приложении Cordova, найдите значение атрибута `id` в элементе мини-приложения и скопируйте его для последующего использования. В приведенном ниже коде XML идентификатором выступает `io.cordova.myapp7777777`.

        <widget defaultlocale="ru-RU" id="io.cordova.myapp7777777"
          version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Вам потребуется использовать этот идентификатор при создании идентификатора приложения на портале разработчика Apple. (Если вы хотите использовать другой идентификатор приложения, созданный на портале разработчика, позже вам понадобится выполнить некоторые дополнительные действия, чтобы изменить этот идентификатор в файле config.xml. Идентификатор элемента мини-приложения должен соответствовать идентификатору приложения на портале разработчика.)

#### Зарегистрируйте приложение для push-уведомлений на портале разработчика Apple.
[!INCLUDE [Включение push-уведомлений Apple через концентраторы уведомлений Xamarin](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### Настройка Azure для отправки push-уведомлений
1. Войдите на [портал Azure](https://portal.azure.com/). Щелкните **Обзор** > **Мобильные приложения** > ваше мобильное приложение > **Параметры** > **Push-уведомления** > **Apple (APNS)** > **Отправка сертификата**. Отправьте экспортированный ранее P12-файл сертификата push-уведомлений. Если вы создали сертификат push-уведомлений для разработки и тестирования, не забудьте выбрать параметр **Песочница**. В противном случае выберите параметр **Рабочая среда**. Теперь ваша служба настроена для работы с push-уведомлениями в iOS.
   
    ![](./media/app-service-mobile-cordova-get-started-push/mobile-app-upload-apns-cert.png)

#### Проверка, соответствует ли идентификатор приложения указанному в приложении Cordova
Если идентификатор приложения, созданный в вашей учетной записи разработчика Apple, совпадает с идентификатором элемента мини-приложения в файле config.xml, этот шаг можно пропустить. Однако если идентификаторы не совпадают, сделайте следующее:

1. Удалите папку platforms из проекта.
2. Удалите папку plugins из проекта.
3. Удалите папку node\_modules из проекта.
4. В качестве значения атрибута идентификатора для элемента мини-приложения в файле config.xml укажите идентификатор приложения, созданный в вашей учетной записи разработчика Apple.
5. Перестройте свой проект.

##### Тестирование push-уведомлений в приложении iOS
1. В Visual Studio выберите **iOS** в качестве целевого объекта развертывания, а затем выберите **устройство**, которое нужно запустить на подключенном устройстве iOS.
   
    Можно выполнить запуск на устройстве iOS, подключенном к компьютеру с помощью iTunes. Симулятор iOS не поддерживает push-уведомления.
2. Нажмите кнопку **Запустить** или клавишу **F5** в Visual Studio, чтобы выполнить сборку проекта и запустить приложение на устройстве iOS. Затем нажмите кнопку **ОК**, чтобы разрешить прием push-уведомлений.
   
   > [!NOTE]
   > Необходимо явно разрешить прием push-уведомлений от вашего приложения. Этот запрос отображается только при первом запуске приложения.
   > 
   > 
3. В приложении введите задачу, затем щелкните знак "плюс" (+).
4. Убедитесь, что уведомление получено, а затем нажмите кнопку ОК, чтобы его закрыть.

## Настройка и запуск проекта в Windows (необязательно)
В этом разделе описывается запуск проекта приложения Apache Cordova на устройствах Windows 10 (в Windows 10 поддерживается подключаемый модуль push-уведомлений PhoneGap). Пропустите этот раздел, если вы не работаете с устройствами Windows.

#### Регистрация приложения Windows для получения push-уведомлений с помощью WNS
Чтобы использовать возможности Магазина в Visual Studio, выберите версию платформы Windows из списка платформ решения, например **Windows-x64** или **Windows-x86** (не используйте **Windows-AnyCPU** для push-уведомлений).

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Видео, демонстрирующее аналогичные действия](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

#### Настройка концентратора уведомлений для WNS
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### Настройка поддержки push-уведомлений Windows в приложении Cordova
Откройте конструктор конфигурации (щелкните файл config.xml правой кнопкой мыши и выберите **Конструктор представлений**), перейдите на вкладку **Windows** и выберите для параметра **Целевая версия Windows** значение **Windows 10**.

> [!NOTE]
> При использовании версии Cordova старше Cordova 5.1.1 (рекомендуется 6.1.1) укажите для флага Toast Capable (Всплывающие уведомления) значение true в файле config.xml.
> 
> 

Для поддержки push-уведомлений в сборках по умолчанию (отладка) откройте файл build.json. Скопируйте конфигурацию в разделе release в конфигурацию отладки.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

После обновления приведенный выше код должен выглядеть следующим образом:

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

Выполните сборку приложения и убедитесь в отсутствии ошибок. Теперь клиентское приложение должно зарегистрироваться для получения уведомлений из серверной части мобильного приложения. Повторите действия из этого раздела для каждого проекта Windows, входящего в ваше решение.

#### Тестирование push-уведомлений в приложении Windows
В Visual Studio выберите платформу Windows, например **Windows-x64** или **Windows-x86**, в качестве целевого объекта развертывания. Чтобы запустить приложение на компьютере Windows 10, где размещено приложение Visual Studio, выберите **Локальный компьютер**.

Нажмите кнопку Запуск для построения проекта, после чего запустите приложение.

В приложении введите имя нового объекта todoitem и добавьте его, щелкнув значок плюса (+).

После добавления элемента должно отобразиться соответствующее уведомление.

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о push-уведомлениях см. в статье о [центрах уведомлений].
* Продолжите работу с учебником и [добавьте аутентификацию в приложение Apache Cordova], если вы этого еще не сделали.

Подробнее об использовании пакетов SDK.

* [Пакет SDK для Apache Cordova]
* [Серверный пакет SDK для ASP.NET]
* [Серверный пакет SDK для Node.js]

<!-- URLs -->
[добавьте аутентификацию в приложение Apache Cordova]: app-service-mobile-cordova-get-started-users.md
[ознакомительного проекта Apache Cordova]: app-service-mobile-cordova-get-started.md
[ознакомительный проект Apache Cordova]: app-service-mobile-cordova-get-started.md
[проверке подлинности]: app-service-mobile-cordova-get-started-users.md
[Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[учетная запись Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google Developer Console]: https://console.developers.google.com/home/dashboard
[документации по установке подключаемого модуля push-уведомлений PhoneGap]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[набор средств Visual Studio для Apache Cordova]: https://www.visualstudio.com/ru-RU/features/cordova-vs.aspx
[центрах уведомлений]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Пакет SDK для Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[Серверный пакет SDK для ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Серверный пакет SDK для Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0907_2016-->