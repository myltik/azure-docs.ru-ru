---
title: Приступая к работе со Службами мобильного взаимодействия Azure для универсальных приложений для Windows
description: Узнайте, как использовать Службы мобильного взаимодействия Azure с аналитическими функциями и push-уведомлениями для универсальных приложений для Windows.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: ed6f3e0a369157bbf4c89982f8e1acbc1ed51340
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Приступая к работе со Службами мобильного взаимодействия Azure для универсальных приложений для Windows
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

В этой статье показано, как применять Службы мобильного взаимодействия Azure для анализа использования приложений и отправки push-уведомлений сегментированным пользователям универсального приложения для Windows.
В этом учебнике описывается простой сценарий вещания с использованием Служб мобильного взаимодействия. Вы создадите пустое универсальное приложение для Windows, которое будет собирать основные данные об использовании приложений и получать push-уведомления, используя службу push-уведомлений Windows (WNS).

> [!NOTE]
> Мы прекратим использование Служб мобильного взаимодействия Azure в марте 2018 г. Сейчас они доступны только существующим клиентам. Дополнительные сведения см. на странице [Службы мобильного взаимодействия](https://azure.microsoft.com/services/mobile-engagement/).

## <a name="prerequisites"></a>предварительным требованиям
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Настройка Служб мобильного взаимодействия для универсального приложения для Windows
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
В этом учебнике описаны действия по базовой интеграции, т. е. минимум, требуемый для сбора данных и отправки push-уведомлений. Полную документацию по интеграции см. в статье [Интеграция пакета SDK универсальных приложений для Windows для Служб мобильного взаимодействия Azure](mobile-engagement-windows-store-sdk-overview.md)

Вы создадите базовое приложение в Visual Studio, чтобы продемонстрировать интеграцию.

### <a name="create-a-windows-universal-app-project"></a>Создание проекта универсального приложения для Windows
Указанные ниже действия предполагают использование Visual Studio 2015, хотя они и аналогичны действиям с использованием более ранних версий Visual Studio.

1. Запустите Visual Studio и на **начальном** экране выберите пункт **Создать проект**.
2. Во всплывающем окне выберите **Windows** -> **Универсальные** -> **Пустое приложение (универсальное приложение Windows)**. Введите необходимые значения в поля **Имя** и **Имя решения**, а затем нажмите кнопку **ОК**.

    ![][1]

Вы создали универсальное приложение для Windows, в которое интегрируете пакет SDK для Служб мобильного взаимодействия Azure.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Подключение приложения к серверной части Служб мобильного взаимодействия
1. Установите пакет NuGet [MicrosoftAzure.MobileEngagement] в проект. Если приложение предназначено как для платформы Windows, так и для Windows Phone, это понадобится сделать для обоих проектов. Для Windows 8.x и Windows Phone 8.1 один и тот же пакет NuGet добавляет нужные двоичные файлы для определенной платформы в каждый проект.
2. Откройте файл **Package.appxmanifest** и добавьте в него указанную ниже возможность.

        Internet (Client)

    ![][2]
3. Теперь вставьте строку подключения (которую вы скопировали ранее для приложения Служб мобильного взаимодействия) в файл `Resources\EngagementConfiguration.xml` между тегами `<connectionString>` и `</connectionString>`.

    ![][3]

    > [!TIP]
    > Если приложение рассчитано как на платформу Windows, так и на Windows Phone, то следует создать два приложения Служб мобильного взаимодействия (по одному для каждой из поддерживаемых платформ). Это позволит правильно сегментировать аудиторию и отправлять целевые уведомления для каждой платформы.

    > [!IMPORTANT]
    > Автоматическое развертывание ресурсов пакета SDK в приложениях Windows 10 UWP не поддерживается. Действия из файла readme.txt, которые появятся после установки пакета Nuget, необходимо выполнить вручную.  

1. В файле `App.xaml.cs`:

    a. Добавьте оператор `using`:

            using Microsoft.Azure.Engagement;

    Б. Добавьте метод для инициализации Engagement.

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Инициализируйте пакет SDK в методе **OnLaunched**:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Вставьте указанный ниже код в метод **OnActivated** и добавьте метод, если его еще нет.

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Включение мониторинга в режиме реального времени
Чтобы начать отправку данных и убедиться, что пользователи активны, отправьте по крайней мере один экран (действие) в серверную часть Служб мобильного взаимодействия.

1. В файле **MainPage.xaml.cs** добавьте следующий оператор `using`:

    с помощью Microsoft.Azure.Engagement.Overlay.
2. Измените базовый класс **MainPage** с **Page** на **EngagementPage**.

        class MainPage : EngagementPageOverlay
3. В файле `MainPage.xaml`:

    a. Добавьте в объявления пространств имен:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    Б. В имени XML-тега измените **Page** на **engagement:EngagementPageOverlay**.

> [!IMPORTANT]
> Если страница переопределяет метод `OnNavigatedTo`, необходимо обязательно вызвать `base.OnNavigatedTo(e)`. В противном случае о действии не будет сообщено (`EngagementPage` вызывает `StartActivity` внутри метода `OnNavigatedTo`). Это особенно важно в проекте Windows Phone, где шаблон по умолчанию включает метод `OnNavigatedTo` .
>
> Для **универсальных приложений Windows 10** используйте метод, рекомендованный в разделе "Рекомендуемый метод: перегрузка классов" статьи [Создание расширенных отчетов с помощью пакета SDK службы Engagement для универсальных приложений для Windows](mobile-engagement-windows-store-advanced-reporting.md) вместо приведенного выше.

## <a id="monitor"></a>Подключение приложения с мониторингом в режиме реального времени
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Включение функции отправки и приема push-уведомлений и обмена сообщениями в приложении
Службы мобильного взаимодействия позволяют взаимодействовать и связываться с пользователями при помощи push-уведомлений и сообщений в приложении в контексте кампаний. На портале Служб мобильного взаимодействия этот модуль называется МОДУЛЕМ ОБРАБОТКИ РЕКЛАМНЫХ КАМПАНИЙ.
В следующих разделах показано, как настроить приложение для приема уведомлений и сообщений.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Настройка приложения для приема push-уведомлений WNS
1. В файле `Package.appxmanifest` откройте вкладку **Приложение** и в разделе **Уведомления** для параметра **Всплывающие уведомления** укажите значение **Да**.

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Запуск пакета SDK для REACH
В файле `App.xaml.cs` вызовите **EngagementReach.Instance.Init(e);** в функции **InitEngagement** сразу после инициализации агента.

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Все готово к отправке всплывающего уведомления. Теперь убедимся, что базовая интеграция выполнена правильно.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Предоставление доступа к Службам мобильного взаимодействия для отправки уведомлений
1. Откройте в веб-браузере [Центр разработки Магазина Windows] , войдите и при необходимости создайте учетную запись.
2. Щелкните **Панель мониторинга** в верхнем правом углу, а затем нажмите кнопку **Создать новое приложение** в меню на панели слева.

    ![][9]
3. Создайте приложение, резервируя его имя.

    ![][10]
4. Создав приложение, выберите в меню слева пункты **Службы -> Push-уведомления**.

    ![][11]
5. В разделе рush-уведомлений щелкните ссылку на **сайт служб Live** .

    ![][12]
6. Вы перейдете в раздел учетных данных для отправки push-уведомлений. В разделе **Параметры приложения** скопируйте **SID пакета** и **cекрет клиента**.

    ![][13]
7. Перейдите в область **Параметры** портала Служб мобильного взаимодействия и щелкните раздел **Системные push-уведомления** слева. Нажмите кнопку **Изменить**, чтобы ввести данные в поля **Идентификатор безопасности пакета (SID)** и **Секретный ключ**, как показано ниже.

    ![][6]
8. Наконец вам нужно связать свое приложение Visual Studio и созданное приложение в магазине приложений. В Visual Studio щелкните **Associate App with Store** (Связать приложение с магазином).

    ![][7]

## <a id="send"></a>Отправка уведомления в приложение
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Если приложение запущено, отобразится уведомление в приложении, а если закрыто — всплывающее уведомление. В противном случае, если приложение закрыто, отобразится всплывающее уведомление.
Если отображается уведомление в приложении, а не всплывающее уведомление, а само приложение выполняется в Visual Studio в режиме отладки, на панели инструментов щелкните **События жизненного цикла -> Приостановить**. Так приложение точно будет приостановлено. Просто нажатие кнопки домашней страницы при отладке приложения в Visual Studio не всегда означает приостановку, и пока уведомление выводится как уведомление в приложении, оно не отображается как всплывающее уведомление.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Центр разработки Магазина Windows]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
