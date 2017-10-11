---
title: "Добавление push-уведомлений в приложение Xamarin.Android | Документация Майкрософт"
description: "Узнайте, как использовать службу приложений и центры уведомлений Azure для отправки push-уведомлений в приложение Xamarin.Android."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: syntaxc4
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
ms.openlocfilehash: c3757d56fb1792092710740dc5ffbd64f18730cf
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2017
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

## <a name="configure-hub"></a>Настройка концентратора уведомлений
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Включение Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Настройка Azure для отправки push-запросов
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Обновление серверного проекта для отправки push-уведомлений
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Настройка клиентского проекта для использования push-уведомлений
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Добавление кода push-уведомлений в приложение
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Тестирование push-уведомлений в приложении
Приложение можно проверить, используя виртуальное устройство в эмуляторе. Для запуска в эмуляторе необходимо выполнить дополнительную настройку.

1. Убедитесь, что для развертывания или отладки используется виртуальное устройство, на котором в качестве назначения заданы интерфейсы Google API, как показано ниже в диспетчере виртуальных устройств Android (AVD).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Добавить учетную запись Google на устройстве Android, поочередно щелкнув **Apps** (Приложения) > **Settings** (Параметры) > **Add account** (Добавить учетную запись). Затем следуйте указаниям на экране.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Запустите приложение todolist, как ранее, и вставьте новый элемент списка дел. На этот раз в области уведомлений отображается значок уведомления. Вы можете открыть панель уведомлений, чтобы просмотреть полный текст уведомления.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
