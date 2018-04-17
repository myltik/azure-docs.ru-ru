---
title: Аутентификация с помощью интерфейсов REST API Службы мобильного взаимодействия Azure (настройка вручную)
description: Описание ручной настройки проверки подлинности для REST API Служб мобильного взаимодействия
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0d71908b34ddf8313aa45014420c9e63a00078c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Проверка подлинности с помощью интерфейсов REST API Служб мобильного взаимодействия — настройка вручную
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

Этот документ является приложением к статье [Authenticate with Mobile Engagement REST APIs](mobile-engagement-api-authentication.md) (Аутентификация с помощью интерфейсов REST API Службы мобильного взаимодействия). Обязательно изучите эту статью, чтобы понять контекст. Также в ней описывается альтернативный способ однократной настройки на портале Azure для аутентификации с помощью интерфейсов REST API Службы мобильного взаимодействия.

> [!NOTE]
> Приведенные ниже инструкции основаны на [этом руководстве по Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md). Они переработаны с учетом требований к аутентификации, применимых для API-интерфейсов Службы мобильного взаимодействия. Ознакомьтесь с ними, если хотите хорошо понимать описанные далее действия.

1. Войдите в учетную запись Azure через [портал Azure](https://portal.azure.com/).
2. Выберите **Active Directory** на левой панели.

   ![Выбор Active Directory][1]

3. Чтобы просмотреть приложения в каталоге, щелкните **Регистрация приложений**.

   ![Просмотр приложений][3]

4. Выберите **Регистрация нового приложения**.

   ![Добавить приложение][4]

5. Введите имя приложения. Сохраните тип приложения **Веб-приложение или API** и нажмите кнопку **Далее**. В поле **URL-адрес входа** можно указать любой URL-адрес, даже фиктивный. Они не используются в этом сценарии и их значения никак не проверяются.

   Когда вы завершите этот процесс, вы получите приложение Azure Active Directory (Azure AD) с указанным именем. Это имя потребуется для параметра **AD\_APP\_NAME**, поэтому обязательно запишите его.

   ![Имя приложения.][8]

7. Выберите имя приложения.

8. Найдите **идентификатор приложения** и запишите его. Этот идентификатор клиента будет указываться в параметре **CLIENT\_ID** при вызовах к API.

   ![Поиск идентификатора приложения][10]

9. Найти справа раздел **Ключи**.

   ![Раздел ключей][11]

10. Создайте новый ключ и сразу же скопируйте его. Его невозможно отобразить повторно.

    ![Панель ключей с информацией о ключе][12]

    > [!IMPORTANT]
    > Срок действия этого ключа истечет, когда закончится указанный вами период. Не забудьте обновить его, когда наступит время, иначе аутентификация в вашем API перестанет работать. Если у вас есть основания полагать, что ключ скомпрометирован, удалите его и создайте новый ключ.
    >
    
11. Нажмите кнопку **Конечные точки** в верхней части страницы. Затем скопируйте значение **Конечная точка маркера OAuth 2.0**.

    ![Копирование конечной точки][14]

16. Эта конечная точка будет иметь такой формат: `https://login.microsoftonline.com/<GUID>/oauth2/token`, где GUID в URL-адресе соответствует значению **tenant_id**.

17. Теперь переходите к настройке разрешений для этого приложения. Чтобы начать процесс, перейдите на [портал Azure](https://portal.azure.com).

18. Выберите **Группы ресурсов** и найдите группу **MobileEngagement**.

    ![Выбор группы MobileEngagement][15]

19. Выберите группу ресурсов **MobileEngagement**, а затем выберите **Все параметры**.

    ![Переход к параметрам группы MobileEngagement][16]

20. Выберите **Пользователи** в разделе **Параметры**. Чтобы добавить пользователя, щелкните **Добавить**.

    ![Добавление пользователей][17]

21. Щелкните **Выбор роли**.

    ![Выбор роли][18]

22. Выберите роль **Владелец**.

    ![Выбор роли владельца][19]

23. В поле поиска найдите имя приложения в формате **AD\_APP\_NAME**. Это имя по умолчанию здесь отсутствует. Найдите его и выберите. Теперь щелкните **Изменить** в нижней части раздела.

    ![Выбор имени][20]

24. В разделе **Добавить доступ** появится надпись **1 пользователь, 0 групп**. Нажмите кнопку **OK** для подтверждения изменений.

    ![Подтверждение добавленных пользователей][21]

Итак, вы завершили обязательную часть настройки Azure AD и ваши API-интерфейсы теперь могут принимать вызовы.

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



