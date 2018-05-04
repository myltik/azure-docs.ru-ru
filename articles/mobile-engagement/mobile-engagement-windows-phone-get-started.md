---
title: Приступая к работе со Службами мобильного взаимодействия Azure для приложений Windows Phone Silverlight
description: Узнайте, как использовать Службы мобильного взаимодействия Azure для аналитики и отправки push-уведомлений для приложений Windows Phone Silverlight.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9bf51bf6a4e2b84771edb9841e1b0ae18953826c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Приступая к работе со Службами мобильного взаимодействия Azure для приложений Windows Phone Silverlight
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как использовать Службы мобильного взаимодействия Azure, чтобы понять направление использования приложения и отправлять push-уведомления сегментированным пользователям приложения Windows Phone Silverlight.
В этом учебнике описывается простой сценарий вещания с использованием Служб мобильного взаимодействия. Здесь вы создадите пустое приложение Windows Phone Silverlight, которое будет собирать основные данные и получать push-уведомления, используя службу push-уведомлений (Майкрософт) (MPNS).

> [!NOTE]
> Мы прекратим использование Служб мобильного взаимодействия Azure в марте 2018 г. Сейчас они доступны только существующим клиентам. Дополнительные сведения см. на странице [Службы мобильного взаимодействия](https://azure.microsoft.com/services/mobile-engagement/).

> [!NOTE]
> Проекты для версии Windows Phone 8.1 и более ранних версий не поддерживаются в Visual Studio 2017.  Дополнительные сведения см. в статье [Целевая платформа и совместимость для Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Если вы ориентируетесь на Windows Phone 8.1 (без Silverlight), см. [руководство по универсальным приложениям для Windows](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

Для работы с данным учебником требуется следующее:

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] 

> [!NOTE]
> Для работы с этим учебником необходима активная учетная запись Azure. Если ее нет, можно создать бесплатную пробную учетную запись всего за несколько минут. Дополнительные сведения см. в разделе [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a id="setup-azme"></a>Настройка Служб мобильного взаимодействия для приложения Windows Phone
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
В этом руководстве описаны действия по базовой интеграции, т. е. минимум, необходимый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции см. в статье [Общие сведения о пакете SDK для Windows Phone Silverlight для Служб мобильного взаимодействия Azure](mobile-engagement-windows-phone-sdk-overview.md).

Мы создадим базовое приложение в Visual Studio, чтобы продемонстрировать интеграцию.

### <a name="create-a-new-windows-phone-silverlight-project"></a>Создание проекта для Windows Phone Silverlight
Указанные ниже действия предполагают использование Visual Studio 2015, хотя они и аналогичны действиям с использованием более ранних версий Visual Studio. 

1. Запустите Visual Studio и на **начальном экране** выберите пункт **Создать проект**.
2. Во всплывающем окне выберите **Windows 8** -> **Windows Phone** -> **Пустое приложение (Windows Phone Silverlight)**. Введите необходимые значения в поля **Имя** и **Имя решения**, а затем нажмите кнопку **ОК**.
   
    ![][1]
3. Вы можете выбрать версию **Windows Phone 8.0** или **Windows Phone 8.1**.

Вы создали приложение Windows Phone Silverlight, в которое нам предстоит интегрировать пакет SDK для Служб мобильного взаимодействия Azure.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
1. Установите пакет NuGet [MicrosoftAzure.MobileEngagement] в проект.
2. Откройте `WMAppManifest.xml` (в папке "Свойства") и убедитесь, что в теге `<Capabilities />` есть указанные ниже объявления (в противном случае добавьте их).
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Теперь вставьте строку подключения, которую вы скопировали ранее для приложения Служб мобильного взаимодействия, в файл `Resources\EngagementConfiguration.xml` между тегами `<connectionString>` и `</connectionString>`.
   
    ![][3]
4. В файле `App.xaml.cs`:
   
    a. Добавьте оператор `using`:
   
            using Microsoft.Azure.Engagement;
   
    Б. Инициализируйте пакет SDK в методе `Application_Launching`:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Вставьте указанный ниже код в `Application_Activated`.
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>Включение мониторинга в режиме реального времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Служб мобильного взаимодействия.

1. В файле MainPage.xaml.cs добавьте инструкцию `using`.
   
        using Microsoft.Azure.Engagement;
2. Замените базовый класс **MainPage**, который расположен перед **PhoneApplicationPage**, на **EngagementPage**.
   
        class MainPage : EngagementPage 
3. В файле `MainPage.xml`:
   
    a. Добавьте в объявления пространств имен:
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    Б. В имени XML-тега замените `phone:PhoneApplicationPage` на `engagement:EngagementPage`.

## <a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении
Службы мобильного взаимодействия позволяют взаимодействовать и связываться с пользователями с помощью push-уведомлений и сообщений в приложении в контексте кампаний. На портале Служб мобильного взаимодействия этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>Настройка приложения для приема push-уведомлений MPNS
Добавьте новые возможности в файл `WMAppManifest.xml` :

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>Запуск пакета SDK для REACH
1. В `App.xaml.cs` вызовите метод `EngagementReach.Instance.Init();` в функции **Application_Launching** сразу после инициализации агента.
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. В `App.xaml.cs` вызовите `EngagementReach.Instance.OnActivated(e);` в функции **Application_Activated** сразу после инициализации агента.
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Все готово. Теперь убедимся, что базовая интеграция выполнена правильно.

## <a id="send"></a>Отправка уведомления в приложение
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Если приложение открыто, на устройстве должно отобразиться уведомление в приложении. В противном случае оно будет отображено в виде всплывающего уведомления, как показано ниже. 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
