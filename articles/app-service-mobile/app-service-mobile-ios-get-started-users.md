---
title: "Добавление проверки подлинности на iOS с помощью мобильных приложений Azure"
description: "Узнайте, как использовать мобильные приложения для проверки подлинности пользователей приложения iOS с помощью разных поставщиков удостоверений, включая AAD, Google, Facebook, Twitter и Майкрософт."
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>Добавление проверки подлинности в приложение iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

В этом учебнике описывается добавление проверки подлинности в проект учебника по [быстрому запуску iOS] с помощью поддерживаемого поставщика удостоверений. Этот учебник использует материал учебника по [быстрому запуску iOS] , который необходимо пройти в первую очередь.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Регистрация приложения для проверки подлинности и настройка службы приложений
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Предоставление разрешений только пользователям, прошедшим проверку подлинности
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

В Xcode нажмите кнопку **Выполнить**, чтобы запустить приложение. Будет создано исключение, так как приложение попытается получить доступ к серверной части как пользователь, не прошедший проверку подлинности, а для таблицы *TodoItem* теперь требуется проверка подлинности.

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>Добавление проверки подлинности в приложение
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[быстрому запуску iOS]: app-service-mobile-ios-get-started.md

[Портал Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


