---
title: "Добавление проверки подлинности в Android с помощью мобильных приложений | Документация Майкрософт"
description: "Узнайте, как использовать функцию мобильных приложений службы приложений Azure для аутентификации пользователей приложения Android с помощью разнообразных поставщиков удостоверений, включая Google, Facebook, Twitter и корпорацию Майкрософт."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: adrianha
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 817626dd3fc87db61280075b80cedf8b9ed77684
ms.openlocfilehash: e638495c10742388804e75f3277c50cf1e20c6a6
ms.lasthandoff: 12/13/2016


---
# <a name="add-authentication-to-your-android-app"></a>Добавление проверки подлинности в приложение Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Сводка
В этом руководстве вы добавите аутентификацию в проект быстрого запуска ToDoList для Android с помощью поддерживаемого поставщика удостоверений. Этот учебник создан на основе учебника [Начало работы с мобильными службами] , который необходимо изучить в первую очередь.

## <a name="register"></a>Регистрация приложения для аутентификации и настройка службы приложений Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* В Android Studio откройте проект, при выполнении заданий руководства [Начало работы с мобильными службами]. В меню **Run** (Запуск) щелкните **Run app** (Запустить приложение). Убедитесь, что после запуска приложения возникает необработанное исключение с кодом состояния "401 (Не авторизовано)".

     Это происходит, потому что приложение пытается получить доступ к серверной части как неаутентифицированный пользователь, а таблица *TodoItem* теперь требует проходить аутентификацию.

Далее мы изменим приложение таким образом, что оно станет аутентифицировать пользователей, прежде чем запрашивать ресурсы из серверной части мобильных приложений. 

## <a name="add-authentication-to-the-app"></a>Добавление проверки подлинности в приложение
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Кэширование маркеров проверки подлинности на клиенте
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Дальнейшие действия
Вы прошли этот учебник по обычной проверке подлинности и теперь можете перейти к одному из следующих учебников:

* [Добавление push-уведомлений в приложение Android](app-service-mobile-android-get-started-push.md).
  Узнайте, как добавить поддержку push-уведомлений в мобильное приложение и настроить в его серверной части использование центров уведомлений Azure для отправки push-уведомлений.
* [Включение автономной синхронизации для приложения Android](app-service-mobile-android-get-started-offline-data.md).
  Узнайте, как добавить в приложение поддержку автономной работы с помощью серверной части мобильных приложений. Автономная синхронизация позволяет пользователям взаимодействовать с мобильным приложением (&mdash;просматривать, добавлять или изменять данные&mdash;) даже при отсутствии подключения к сети.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Начало работы с мобильными службами]: app-service-mobile-android-get-started.md

