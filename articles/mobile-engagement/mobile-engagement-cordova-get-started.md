---
title: Начало работы со Службами мобильного взаимодействия Azure для Cordova (Phonegap)
description: Узнайте, как использовать Служб мобильного взаимодействия Azure для аналитики и отправки push-уведомлений в приложения Cordova (Phonegap).
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: abe0ad6117cc2feb72ebe788d07b50e8f145174c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Начало работы со Службами мобильного взаимодействия Azure для Cordova (Phonegap)
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как использовать Службы мобильного взаимодействия Azure для анализа использования приложений и отправки push-уведомлений сегментированным пользователям мобильного приложения, разработанного на платформе Cordova.

В этом учебнике вы создадим пустое приложение Cordova на компьютере Mac и интегрируем пакет SDK для Служб мобильного взаимодействия. Приложение будет собирать базовые данные аналитики и получать push-уведомления с помощью системы push-уведомлений Apple (APNS) для iOS и Google Cloud Messaging (GCM) для Android. Для тестирования мы развернем его на устройствах под управлением iOS или Android. 

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Для работы с данным учебником требуется следующее:

* Xcode, который можно установить из Mac App Store (для развертывания на iOS)
* [Android SDK и эмулятор](http://developer.android.com/sdk/installing/index.html) (для развертывания на Android).
* Сертификат push-уведомлений (P12), который можно получить в центре разработки Apple (для APNS)
* Номер проекта GCM, который можно найти в консоли разработчиков Google (для GCM)
* [Подключаемый модуль Cordova для Служб мобильного взаимодействия](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> Исходный код и файл сведений о подключаемом модуле Cordova см. на сайте [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova).
> 
> 

## <a id="setup-azme"></a>Настройка Служб мобильного взаимодействия для приложения Cordova
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений. 

Чтобы продемонстрировать интеграцию, мы создадим простое приложение при помощи Сordova.

### <a name="create-a-new-cordova-project"></a>Создание нового проекта Cordova
1. Чтобы создать проект Cordova на основе шаблона по умолчанию, откройте окно *терминала* на компьютере Mac и введите указанную ниже команду. Убедитесь, что в профиле публикации, который вы впоследствии будете использовать для развертывания приложения iOS, используется идентификатор приложения "com.mycompany.myapp". 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Выполните указанные ниже команды, чтобы настроить проект для **iOS** и запустить его в iOS Simulator.
   
        $ cordova platform add ios 
        $ cordova run ios
3. Выполните указанные ниже команды, чтобы настроить проект для **Android** и запустить его в эмуляторе Android. Убедитесь, что для параметров эмулятора Android SDK в качестве цели установлены API-интерфейсы Google (Google Inc.), а в качестве ЦП/ABI используется ARM API-интерфейсов Google.  
   
        $ cordova platform add android
        $ cordova run android
4. Добавьте консольный подключаемый модуль Cordova. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
1. Установите подключаемый модуль Cordova для Служб мобильного взаимодействия Azure и укажите значения переменных для настройки подключаемого модуля:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Значок Android Reach* — это должно быть имя ресурса без расширения или префикса (например, mynotificationicon), а значок файла необходимо скопировать в приложение Android (platforms/android/res/drawable).

*Значок iOS Reach* — это должно быть имя ресурса с расширением (например, mynotificationicon.png), а файл значка должен быть добавлен в проект iOS с XCode (с помощью меню "Добавить файлы").

## <a id="monitor"></a>Включение мониторинга в реальном времени
1. В проекте Cordova добавьте в файл **www/js/index.js** вызов Служб мобильного взаимодействия, чтобы объявить новое действие после получения события *deviceReady*.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Запустите приложение:
   
   * **Для iOS:**
     
       В окне `Terminal` запустите ваше приложение в новом экземпляре iOS Simulator, выполнив указанную ниже команду.
     
           cordova run ios
   * **Для Android:**
     
       В окне `Terminal` запустите ваше приложение в новом экземпляре эмулятора, выполнив указанную ниже команду.
     
           cordova run android
3. В журнале консоли можно увидеть следующее:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении
Службы мобильного взаимодействия позволяют взаимодействовать с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Служб мобильного взаимодействия этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах описаны действия по настройке приложения для приема уведомлений и сообщений.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Настройка учетных данных для отправки push-уведомлений из Служб мобильного взаимодействия
Чтобы разрешить Службам мобильного взаимодействия отправлять push-уведомления от вашего имени, необходимо предоставить им доступ к сертификату Apple iOS или ключу API сервера GCM. 

1. Перейдите на портал Служб мобильного взаимодействия. Убедитесь, что у вас открыто приложение, используемое для этого проекта, и нажмите кнопку **Использовать** внизу.
   
    ![][1]
2. Вы перейдете на страницу "Параметры" на портале Mobile Engagement. Здесь выберите раздел **Собственные push-уведомления** .
   
    ![][2]
3. Настройте сертификат Apple iOS или ключ API сервера GCM.
   
    **[iOS]**
   
    a. Выберите сертификат P12, загрузите его и введите пароль:
   
    ![][3]
   
    **[Android]**
   
    a. Выберите значок редактирования перед **ключом API** в разделе параметров GCM, а затем в открывшемся всплывающем окне вставьте ключ сервера GCM и нажмите кнопку **ОК**. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Включение push-уведомлений в приложении Cordova
Добавьте в файл **www/js/index.js** вызов Служб мобильного взаимодействия для запроса push-уведомлений и объявления обработчика.

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Запуск приложения
**[iOS]**

1. При тестировании push-уведомлений для сборки и развертывания приложения на устройстве мы будем использовать XCode, поскольку iOS разрешает вывод push-уведомлений только на реальных устройствах. Перейдите в расположение, где создан проект Cordova, и откройте папку **...\platforms\ios**. Откройте XCODEPROJ-файл в XCode. 
2. Скомпилируйте и разверните приложение Cordova на устройстве iOS, используя учетную запись с профилем подготовки, содержащим сертификат, который вы только что загрузили на портал Служб мобильного взаимодействия, и идентификатором приложения, введенным при создании приложения Cordova. Вы можете проверить значение *идентификатора набора* в файле **Resources\*-info.plist** в XCode. 
3. На устройстве iOS появится стандартное всплывающее окно с уведомлением о том, что приложение запрашивает разрешение на отправку уведомлений. Предоставьте это разрешение. 

**[Android]**

Для запуска приложения на Android можно просто использовать эмулятор Android, так как он поддерживает уведомления GCM. 

    cordova run android

## <a id="send"></a>Отправка уведомления в приложение
Теперь мы создадим простую кампанию push-уведомлений, которая будет отправлять push-уведомления в приложение, запущенное на устройстве.

1. Перейдите на вкладку **Reach** на портале Служб мобильного взаимодействия.
2. Щелкните **Создать объявление** , чтобы создать кампанию push-уведомлений.
   
    ![][6]
3. Введите данные для новой кампании **[Android]**
   
   * Задайте **имя** кампании. 
   * Для параметра **Тип доставки** выберите значение *Системное уведомление* *Простое*.
   * Для параметра **Время доставки** выберите значение *Любое время*
   * Укажите **название** уведомления, которое будет отображаться в первой строке push-уведомления.
   * Укажите **сообщение** для уведомления, которое будет использоваться в качестве текста сообщения. 
     
     ![][11]
4. Введите данные для новой кампании **[iOS]**
   
   * Задайте **имя** кампании. 
   * Для параметра **Время доставки** выберите значение *Только вне приложения*
   * Укажите **название** уведомления, которое будет отображаться в первой строке push-уведомления.
   * Укажите **сообщение** для уведомления, которое будет использоваться в качестве текста сообщения. 
     
     ![][12]
5. Прокрутите окно вниз и в разделе содержимого выберите пункт **Только уведомления**
   
    ![][8]
6. [Необязательно] Можно также указать URL-адрес действия. Нужно использовать ту же схему URL-адресов, что и для переменной **AZME\_REDIRECT\_URL** подключаемого модуля, например *myapp://test*.  
7. Вы настроили простейшую базовую кампанию. Еще раз прокрутите окно вниз и нажмите кнопку **Создать** , чтобы сохранить кампанию.
8. И, наконец, **активируйте** кампанию.
   
    ![][10]
9. Теперь push-уведомления в рамках этой кампании будут отображаться на устройстве или в эмуляторе. 

## <a id="next-steps"></a>Дальнейшие действия
[Обзор методов, доступных в пакете Cordova SDK для Служб мобильного взаимодействия](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png

