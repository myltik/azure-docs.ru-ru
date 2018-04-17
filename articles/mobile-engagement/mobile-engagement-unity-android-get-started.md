---
title: Приступая к работе со Службами мобильного взаимодействия Azure для развертывания Unity в Android
description: Узнайте, как использовать Службы мобильного взаимодействия Azure с аналитическими функциями и push-уведомлениями для развертывания приложений Unity на устройствах iOS.
services: mobile-engagement
documentationcenter: unity
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d5f0ef79-be00-4cec-97a5-a0b2fdaa380e
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-unity-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: cd126fc656fc00667c4c09298409f920ab8160fc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-unity-android-deployment"></a>Приступая к работе со Службами мобильного взаимодействия Azure для развертывания Unity в Android
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как с помощью Служб мобильного взаимодействия Azure анализировать использование приложения и как отправлять push-уведомления сегментированным пользователям приложения Unity при его развертывании на устройстве Android.
Это руководство основывается на классическом руководстве по создании игры в мяч в приложении Unity. Вам следует выполнить инструкции, приведенные в этом [руководстве](mobile-engagement-unity-roll-a-ball.md), прежде чем начинать интеграцию Служб мобильного взаимодействия, пример которой приводится в настоящем руководстве. 

Для работы с данным учебником требуется следующее:

* приложение [Unity Editor](http://unity3d.com/get-unity);
* [пакет SDK Unity для Служб мобильного взаимодействия](https://aka.ms/azmeunitysdk)
* пакет SDK для Google Android.

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).
> 
> 

## <a id="setup-azme"></a>Настройка Служб мобильного взаимодействия для вашего приложения Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
### <a name="import-the-unity-package"></a>Импорт пакета Unity
1. Загрузите пакет [Служб мобильного взаимодействия для Unity](https://aka.ms/azmeunitysdk) и сохраните его на локальном компьютере. 
2. Выберите **Ресурсы -> Импорт пакета -> Custom Package** (Пользовательский пакет) и выберите пакет, скачанный на предыдущем этапе. 
   
    ![][70] 
3. Убедитесь, что выбраны все файлы, а затем нажмите кнопку **Импорт** . 
   
    ![][71] 
4. Когда импорт будет завершен, в вашем проекте отобразятся импортированные файлы пакета SDK.  
   
    ![][72] 

### <a name="update-the-engagementconfiguration"></a>Обновление конфигурации EngagementConfiguration
1. В папке пакета SDK откройте файл скрипта **EngagementConfiguration** и обновите **ANDROID\_CONNECTION\_STRING** с помощью строки подключения, полученной ранее на портале Azure.  
   
    ![][73]
2. Сохраните файл. 
3. Щелкните **Файл -> Engagement -> Generate Android Manifest** (Создание манифеста Android). Это подключаемый модуль, добавленный в ваш пакет SDK для Служб мобильного взаимодействия. Если его щелкнуть, параметры проекта будут автоматически обновлены. 
   
    ![][74]

> [!IMPORTANT]
> Его нужно запускать всегда, когда обновляется файл **EngagementConfiguration**. В противном случае изменения не отражаются в приложении. 
> 
> 

### <a name="configure-the-app-for-basic-tracking"></a>Настройка базового отслеживания в приложении
1. Откройте сценарий **PlayerController** , вложенный для редактирования в объект Player. 
2. Добавьте следующую инструкцию using:
   
        using Microsoft.Azure.Engagement.Unity;
3. Добавьте следующую строку в метод `Start()`.
   
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

### <a name="deploy-and-run-the-app"></a>Развертывание и запуск приложения
Прежде чем развертывать это приложение Unity у себя на устройстве, убедитесь, что на компьютере установлен пакет SDK для Android. 

1. Подключите устройство Android к компьютеру. 
2. Откройте **Файл -> Параметры сборки**. 
   
    ![][40]
3. Выберите **Android** и щелкните **Switch Platform** (Переключить платформу).
   
    ![][51]
   
    ![][52]
4. Щелкните **Настройка проигрывателя** и укажите допустимый идентификатор пакета. 
   
    ![][53]
5. Наконец, нажмите кнопку **Сборка и запуск**
   
    ![][54]
6. Возможно, вам потребуется указать имя папки для хранения пакета Android. 
7. Если процесс пройдет успешно и пакет будет развернут на подключенном устройстве, вы увидите игру Unity у себя на телефоне. 

## <a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении
[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

### <a name="update-the-engagementconfiguration"></a>Обновление конфигурации EngagementConfiguration
1. В папке пакета SDK откройте файл скрипта **EngagementConfiguration** и обновите **ANDROID\_GOOGLE\_NUMBER** с помощью **номера проекта Google**, полученного ранее на портале разработчиков облака Google. Это строковое значение, поэтому обязательно заключите его в двойные кавычки. 
   
    ![][75]
2. Сохраните файл. 
3. Щелкните **Файл -> Engagement -> Generate Android Manifest** (Создание манифеста Android). Это подключаемый модуль, добавленный в ваш пакет SDK для Служб мобильного взаимодействия. Если его щелкнуть, параметры проекта будут автоматически обновлены. 
   
    ![][74]

### <a name="configure-the-app-to-receive-notifications"></a>Настройка в приложении получения уведомлений
1. Откройте сценарий **PlayerController** , вложенный для редактирования в объект Player. 
2. Добавьте следующую строку в метод `Start()` .
   
        EngagementReachAgent.Initialize();
3. Теперь, когда приложение обновлено, разверните и запустите его на устройстве согласно приведенным ниже инструкциям. 

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png
