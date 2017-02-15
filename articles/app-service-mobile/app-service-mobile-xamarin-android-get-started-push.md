---
title: "Добавление push-уведомлений в приложение Xamarin.Android | Документация Майкрософт"
description: "Узнайте, как использовать службу приложений и центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c9f8c46fa5fba1e69382a383ff94700de07db4ab


---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Добавление push-уведомлений в приложение Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Обзор
В этом руководстве мы добавим push-уведомления в [проект быстрого запуска Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md), чтобы при каждом добавлении новой записи на устройство отправлялось push-уведомление.

Если вы не используете скачанный проект сервера, необходимо добавить пакет расширений для push-уведомлений. Дополнительные сведения о пакетах расширений для сервера см. в статье [Работа с пакетом SDK для внутреннего сервера .NET для мобильных приложений Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Предварительные требования
Для работы с данным учебником требуется следующее:

* Активная учетная запись Google. Вы можете зарегистрировать учетную запись Google на сайте [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Компонент клиента Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/).

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraenable-firebase-cloud-messaging"></a><a id="register"></a>Включение Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Настройка Azure для отправки push-запросов
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="a-idupdate-serveraupdate-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Обновление серверного проекта для отправки push-уведомлений
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="a-idconfigure-appaconfigure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Настройка клиентского проекта для использования push-уведомлений
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="a-idadd-pushaadd-push-notifications-code-to-your-app"></a><a id="add-push"></a>Добавление кода push-уведомлений в приложение
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="a-nametestatest-push-notifications-in-your-app"></a><a name="test"></a>Тестирование push-уведомлений в приложении
Приложение можно проверить, используя виртуальное устройство в эмуляторе. Для запуска в эмуляторе необходимо выполнить дополнительную настройку.

1. Убедитесь, что для развертывания или отладки используется виртуальное устройство, на котором в качестве назначения заданы интерфейсы Google API, как показано ниже в диспетчере виртуальных устройств Android (AVD).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Добавить учетную запись Google на устройстве Android, поочередно щелкнув **Apps** (Приложения) > **Settings** (Параметры) > **Add account** (Добавить учетную запись). Затем следуйте указаниям на экране.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Запустите приложение todolist, как ранее, и вставьте новый элемент списка дел. На этот раз в области уведомлений отображается значок уведомления. Вы можете открыть панель уведомлений, чтобы просмотреть полный текст уведомления.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Приложение быстрого запуска Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Компонент клиента Google Cloud Messaging]: http://components.xamarin.com/view/GCMClient/
[Компонент мобильных служб Azure]: http://components.xamarin.com/view/azure-mobile-services/



<!--HONumber=Nov16_HO3-->


