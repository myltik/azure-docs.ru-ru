---
title: Начало работы со Службами мобильного взаимодействия Azure для Xamarin.Android
description: Узнайте, как использовать Службы мобильного взаимодействия Azure с аналитическими функциями и push-уведомлениями для приложений Xamarin.Android.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: fb68cf98-08a2-41b5-8e59-757469de3fe7
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/16/2016
ms.author: piyushjo
ms.openlocfilehash: 4ac6e85a0143fc083c22a92e99503689f463d813
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinandroid-apps"></a>Начало работы со Службами мобильного взаимодействия Azure для приложений Xamarin.Android
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как применять Службы мобильного взаимодействия Azure для анализа использования приложения и как отправлять push-уведомления сегментированным пользователям приложения Xamarin.Android.
В этом учебнике описывается простой сценарий вещания с использованием Служб мобильного взаимодействия. В рассматриваемом сценарии создается пустое приложение Xamarin.Android, которое собирает основные данные и получает push-уведомления с помощью службы Google Cloud Messaging (GCM).

> [!NOTE]
> Мы прекратим использование Служб мобильного взаимодействия Azure в марте 2018 г. Сейчас они доступны только существующим клиентам. Дополнительные сведения см. на странице [Службы мобильного взаимодействия](https://azure.microsoft.com/en-us/services/mobile-engagement/).

Для работы с данным учебником требуется следующее:

* [Xamarin Studio](http://xamarin.com/studio). Вы также можете использовать Visual Studio с расширением Xamarin, но в этом руководстве используется Xamarin Studio. Инструкции см. в руководстве по [установке и настройке Visual Studio и Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* [пакет Xamarin SDK для Служб мобильного взаимодействия](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started).
> 
> 

## <a id="setup-azme"></a>Настройка Служб мобильного взаимодействия для вашего приложения Android
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. 

Мы создадим базовое приложение в Xamarin Studio, чтобы продемонстрировать интеграцию.

### <a name="create-a-new-xamarinandroid-project"></a>Создание нового проекта Xamarin.Android
1. Запустите **Xamarin Studio**. Щелкните **File** -> **New** -> **Solution** (Файл -> Создать -> Решение). 
   
    ![][1]
2. Выберите **Android App** (Приложение Android), а затем выберите язык **C#** и нажмите кнопку **Next** (Далее).
   
    ![][2]
3. Заполните поля **App Name** (Имя приложения) и **Organization Identifier** (Идентификатор организации). Установите флажок **Google Play Services** (Службы Google Play) и нажмите кнопку **Next** (Далее). 
   
    ![][3]
4. Если требуется, обновите значения в полях **Project Name** (Имя проекта), **Solution Name** (Имя решения) и **Location** (Расположение), а затем нажмите кнопку **Create** (Создать).
   
    ![][4]

Xamarin Studio создаст приложение, в которое мы интегрируем Службы мобильного взаимодействия. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
1. В окне решения правой кнопкой мыши щелкните папку **Packages** (Пакеты) и выберите пункт **Add Packages...** (Добавить пакеты).
   
    ![][5]
2. Найдите пакет **Xamarin SDK для Служб мобильного взаимодействия Microsoft Azure** и добавьте его в свое решение.  
   
    ![][6]
3. Откройте файл **MainActivity.cs** и добавьте следующие инструкции using:
   
        using Microsoft.Azure.Engagement;
        using Microsoft.Azure.Engagement.Activity;
4. В метод `OnCreate` добавьте следующую команду, чтобы инициализировать подключение к внутреннему серверу Служб мобильного взаимодействия. Обязательно добавьте **строку подключения**. 
   
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
        EngagementAgent.Init(engagementConfiguration);

### <a name="add-permissions-and-a-service-declaration"></a>Добавление разрешений и объявления службы
1. Откройте файл **Manifest.xml** в папке Properties (Свойства). Перейдите на вкладку Source (Источник), чтобы напрямую обновить источник XML.
2. Добавьте эти разрешения в файл Manifest.xml (который можно найти в папке **Properties** (Свойства)) проекта непосредственно перед тегом `<application>` или после него:
   
        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
3. Добавьте следующий код между тегами `<application>` и `</application>`, чтобы объявить службу агента:
   
        <service
             android:name="com.microsoft.azure.engagement.service.EngagementService"
             android:exported="false"
             android:label="<Your application name>"
             android:process=":Engagement"/>
4. В только что вставленном коде замените `"<Your application name>"` в метке фактическим значением. Это имя появится в меню **Параметры** , где отображены службы, работающие на устройстве пользователя. Вы можете добавить в метку слово, например Service (служба).

### <a name="send-a-screen-to-mobile-engagement"></a>Отправка экрана в Службы мобильного взаимодействия
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран на внутренний сервер Служб мобильного взаимодействия. Для этого убедитесь, что `MainActivity` наследуется от `EngagementActivity`, а не от `Activity`.

    public class MainActivity : EngagementActivity

Если наследование от `EngagementActivity` невозможно, необходимо добавить методы `.StartActivity` и `.EndActivity` в `OnResume` и `OnPause` соответственно.  

        protected override void OnResume()
            {
                EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
                base.OnResume();             
            }

            protected override void OnPause()
            {
                EngagementAgent.EndActivity();
                base.OnPause();            
            }

## <a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Включение push-уведомлений и обмена сообщениями в приложении
Службы мобильного взаимодействия позволяют взаимодействовать и СВЯЗЫВАТЬСЯ с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Служб мобильного взаимодействия этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

[!INCLUDE [Enable Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[!INCLUDE [Enable in-app messaging](../../includes/mobile-engagement-android-send-push.md)]

[!INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png
