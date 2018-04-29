---
title: Авторизация учетных записей разработчиков с помощью Azure Active Directory в службе управления API Azure | Документация Майкрософт
description: Узнайте, как авторизовать пользователей с помощью Azure Active Directory в службе управления API.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 9f3669d205ab4bd24ccba53ffb532fe1d88131ac
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure

В этой статье показывается, как включить доступ к порталу разработчика для пользователей из каталога Active Directory Azure (Azure AD). В этом руководстве также показано, как управлять группами пользователей Azure AD путем добавления внешних групп, содержащих пользователей.

> [!NOTE]
> Интеграция Azure AD доступна в только на уровнях [Developer, Standard и Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>предварительным требованиям

- Выполните задачи в кратком руководстве по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API Azure. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Авторизация учетных записей разработчиков с помощью Azure AD

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Выберите пункт ![Стрелка](./media/api-management-howto-aad/arrow.png).
3. В поле поиска введите **api**.
4. Выберите **Службы управления API**.
5. Выберите экземпляр службы управления API.
6. В разделе **Безопасность** выберите **Удостоверения**.

7. Выберите **+ Добавить** в верхней части экрана.

    Справа отобразится область **Добавление поставщика удостоверений**.
8. В разделе **Тип поставщика** выберите **Azure Active Directory**.

    В области отобразятся элементы управления, необходимые для ввода других требуемых сведений. Элементы управления включают **идентификатор клиента** и **секрет клиента**. (Эти элементы управления описаны далее в этой статье.)
9. Запишите содержимое **URL-адреса перенаправления**.
    
   ![Шаги для добавления поставщика удостоверений на портале Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. В браузере откройте другую вкладку. 
11. Перейдите на [портал Azure](https://portal.azure.com).
12. Выберите пункт ![Стрелка](./media/api-management-howto-aad/arrow.png).
13. Введите **active**. Появится область **Azure Active Directory**.
14. Выберите **Azure Active Directory**.
15. В разделе **Управление** выберите **Регистрация приложения**.
16. Выберите **Регистрация нового приложения**.

    ![Выбранные элементы для создания регистрации приложения](./media/api-management-howto-aad/api-management-with-aad002.png)

    Справа появится область **Создание**. Здесь вам нужно ввести информацию о приложении Azure AD.
17. Введите имя приложения.
18. Выберите в качестве типа приложения **Веб-приложение или API**.
19. В поле URL-адреса входа введите URL-адрес входа на свой портал разработчика. В данном примере URL-адрес входа — https://apimwithaad.portal.azure-api.net/signin.
20. Выберите **Создать**, чтобы создать приложение.
21. Чтобы найти приложение, выберите **Регистрация приложений** и выполните поиск по имени.

    ![Поле, в котором можно выполнить поиск приложения](./media/api-management-howto-aad/find-your-app.png)
22. После регистрации приложения перейдите к параметру **URL-адрес ответа** и убедитесь, что для **URL-адреса перенаправления** установлено значение, полученное на шаге 9. 
23. Чтобы настроить приложение (например, изменить **URL-адрес идентификатора приложения**) выберите **Свойства**.

    ![Открытие области "Свойства"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Если для этого приложения будет использоваться несколько экземпляров Azure AD, установите значение **Да** для параметра **Мультитенантный**. По умолчанию используется значение **Нет**.
24. Установите разрешения для приложений. Для этого выберите **Требуемые разрешения**.
25. Выберите свое приложение и установите флажки **Чтение данных каталога** и **Вход и чтение профиля пользователя**.

    ![Флажки для разрешений](./media/api-management-howto-aad/api-management-with-aad005.png)

    Дополнительные сведения о разрешениях приложений и делегированных разрешениях см. в разделе, посвященном [доступу к API Graph][Accessing the Graph API].
26. В области слева скопируйте значение **идентификатора приложения**.

    ![Значение идентификатора приложения](./media/api-management-howto-aad/application-id.png)
27. Вернитесь к приложению управления API. 

    В окне **Добавление поставщика удостоверений** вставьте значение **Идентификатор приложения** в текстовое поле **Идентификатор клиента**.
28. Вернитесь к конфигурации Azure AD и выберите **Ключи**.
29. Создайте ключ, указав его имя и срок действия. 
30. Щелкните **Сохранить**. Будет создан ключ.

    Скопируйте этот ключ в буфер обмена.

    ![Выбранные элементы для создания ключа](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Запишите этот ключ. После закрытия области конфигурации Azure AD нельзя будет снова отобразить ключ.
    > 
    > 
31. Вернитесь к приложению управления API. 

    В окне **Добавление поставщика удостоверений** вставьте ключ в текстовое поле **Секрет клиента**.
32. В окне **Добавление поставщика удостоверений** также содержится текстовое поле **Разрешенные клиенты**. Укажите домены экземпляров Azure AD, которым требуется предоставить доступ к API экземпляра службы управления API. Несколько доменов можно разделять символами начала новой строки, пробелами или запятыми.

    В разделе **Разрешенные клиенты** можно указать несколько доменов. Прежде чем какой-либо пользователь сможет выполнить вход из домена, отличного от домена, в котором было зарегистрировано приложение, глобальный администратор этого другого домена должен предоставить разрешение для доступа приложения к данным каталога. Чтобы предоставить разрешение, глобальный администратор должен выполнить следующее:
    
    a. Перейдите на страницу `https://<URL of your developer portal>/aadadminconsent` (например, https://contoso.portal.azure-api.net/aadadminconsent).
    
    Б. Ввести имя домена клиента Azure AD, к которому требуется предоставить доступ.
    
    c. Нажмите кнопку **Submit** (Отправить). 
    
    В следующем примере глобальный администратор miaoaad.onmicrosoft.com пытается предоставить разрешение определенному порталу разработчика. 

33. После указания требуемой конфигурации выберите **Добавить**.

    ![Кнопка "Добавить" в области "Добавление поставщика удостоверений"](./media/api-management-howto-aad/api-management-with-aad007.png)

После сохранения изменений пользователи в указанной службе Azure AD могут входить на портал разработчика, выполняя действия, которые описаны в разделе [Вход на портал разработчика с помощью учетной записи Azure Active Directory](#log_in_to_dev_portal).

![Ввод имени клиента Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

На следующем экране глобальному администратору будет предложено подтвердить предоставление разрешения. 

![Подтверждение назначения разрешений](./media/api-management-howto-aad/api-management-permissions-form.png)

Если администратор, не являющийся глобальным, попытается выполнить вход до того, как глобальный администратор предоставит разрешения, то попытка входа завершится неудачно и появится экран сообщения об ошибке.

## <a name="add-an-external-azure-ad-group"></a>Добавление внешней группы Azure AD

После включения доступа для пользователей в экземпляре Azure AD вы можете добавлять группы Azure AD в службе управления API, чтобы упростить управление связью разработчиков в такой группе с требуемыми продуктами.

Чтобы настроить внешнюю группу Azure AD, необходимо сначала настроить экземпляр Azure AD на вкладке **удостоверений**, выполнив процедуру, описанную в предыдущем разделе. 

Внешние группы Azure AD добавляются с вкладки **Группы** в экземпляре службы управления API.

1. Выберите вкладку **Группы** .
2. Нажмите кнопку **Добавить группу AAD**.
   ![Кнопка "Добавить группу AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Выберите группу, которую нужно добавить.
4. Нажмите кнопку **Выбрать**.

После добавления внешней группы Azure AD можно просмотреть и настроить ее свойства. Выберите имя группы на вкладке **Группы**. Здесь можно изменить **имя** и **описание** группы.
 
Пользователи из настроенного экземпляра Azure AD теперь могут входить на портал разработчика, просматривать любые группы, которые они могут видеть, и подписываться на них.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Вход на портал разработчика с помощью учетной записи Azure AD

Чтобы войти на портал разработчика с помощью учетной записи Azure AD, которая была настроена в предыдущих разделах:

1. Откройте новое окно браузера, используя URL-адрес входа из конфигурации приложения Active Directory, и выберите **Azure Active Directory**.

   ![Страница входа][api-management-dev-portal-signin]

2. Введите учетные данные одного из пользователей в Azure AD и нажмите кнопку **Вход**.

   ![Вход с использованием имени пользователя и пароля][api-management-aad-signin]

3. Может появиться запрос с регистрационной формой, если требуются какие-либо дополнительные сведения. Заполните эту регистрационную форму и выберите **Регистрация**.

   ![Кнопка "Регистрация" в форме регистрации][api-management-complete-registration]

Теперь этот пользователь вошел на портал разработчика вашего экземпляра службы управления API.

![Портал разработчика после завершения регистрации][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
