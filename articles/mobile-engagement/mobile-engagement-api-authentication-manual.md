---
title: "Проверка подлинности с помощью интерфейсов REST API Служб мобильного взаимодействия — настройка вручную"
description: "Описание ручной настройки проверки подлинности для REST API Служб мобильного взаимодействия"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Проверка подлинности с помощью интерфейсов REST API Служб мобильного взаимодействия — настройка вручную
Данная документация является приложение документации [аутентификация с помощью API-интерфейсов REST Mobile Engagement](mobile-engagement-api-authentication.md). Сначала следует ознакомиться с данными в статье.
Он описывает способ сделать однократно для настройки проверки подлинности для API REST Mobile Engagement, с помощью портала Azure.

> [!NOTE]
> Приведенные ниже инструкции основаны на этой [руководство по Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) и настраивать для требования проверки подлинности для API-интерфейсов Mobile Engagement. Ознакомьтесь с этим руководством, если хотите получить более подобные сведения о выполняемых действиях.

1. Войдите в учетную запись Azure на [портале Azure](https://portal.azure.com/).
2. Выберите **Active Directory** на левой панели.

     ![выбор Active Directory][1]

3. Для просмотра приложения в каталоге, нажмите кнопку **регистрации приложения**.

     ![просмотр приложений][3]

4. Щелкните **Регистрация нового приложения**.

     ![Добавить приложение][4]

5. Введите имя приложения и оставить тип приложения как **веб-приложения и API** и нажмите кнопку "Далее". Можно указать любой фиктивный URL-адреса для **URL-адрес входа**: они не используются в этом сценарии и сами URL-адреса, не проверяются.
6. После этого вы сможете приложения Azure AD с помощью указанного имени. Это ваш **AD\_приложения\_имя**, пожалуйста, запишите его.

     ![имя приложения][8]

7. Щелкните имя приложения.
8. Найти **идентификатор приложения**, запишите его, это будет идентификатор КЛИЕНТА, который должен использоваться в качестве **КЛИЕНТА\_идентификатор** API вызывает.

     ![настройка приложения][10]

9. Найти **ключей** раздел в правой части.

     ![настройка приложения][11]

10. Создание нового ключа и немедленно скопируйте его и сохранить его для использования. Он никогда не будет отображаться еще раз.

     ![настройка приложения][12]

    > [!IMPORTANT]
    > Этот ключ, срок действия истекает в конце времени, на которое указана, поэтому убедитесь, что обновлять его при необходимости в противном случае проверки подлинности API больше не будет работать. Если вам кажется, что ключ был скомпрометирован, удалите его и создайте заново.
    >
    >
11. Щелкните **конечные точки** кнопку в верхней части страницы и скопируйте **конечная точка МАРКЕРА OAUTH 2.0**.

    ![][14]

16. Эта конечная точка будет в следующей форме, где GUID в URL-адрес вашего **TENANT_ID** поэтому запомните или запишите его:`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. Перейдем к настройке разрешений для этого приложения. Для этого необходимо открыть [портал Azure](https://portal.azure.com). 
18. Щелкните **Группы ресурсов** и найдите группу **Службы мобильного взаимодействия**.

    ![][15]

19. Нажмите кнопку **Mobile Engagement** ресурсов группы и перейдите к **параметры** здесь статьи.

    ![][16]

20. Щелкните **пользователей** в параметрах раздела и выберите команду на **добавить** для добавления пользователя.

    ![][17]

21. Щелкните **выберите роль**.

    ![][18]

22. Щелкните **владельца**.

    ![][19]

23. В поле поиска найдите имя приложения в формате **AD\_APP\_NAME**. Здесь оно не отображается по умолчанию. После обнаружения, выберите его и щелкните **выберите** в нижней части раздела.

    ![][20]

24. На **Добавление доступа к** раздела, он будет отображаться как **1 пользователь, 0 групп**. Нажмите кнопку **ОК** на этот раздел, чтобы подтвердить изменение.

    ![][21]

Завершена необходимая конфигурация Azure AD и имеют значение для вызова API.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



