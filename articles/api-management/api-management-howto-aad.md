---
title: "Авторизация учетных записей разработчиков с помощью Azure Active Directory в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как авторизовать пользователей с помощью Azure Active Directory в управлении API"
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure

В этом руководстве показывается, как включить доступ к порталу разработчика для пользователей из каталога Active Directory Azure. В этом руководстве также показано, как управлять группами пользователей Azure Active Directory путем добавления внешних групп, содержащих пользователей Azure Active Directory.

> [!WARNING]
> Интеграция Azure Active Directory доступна в только на уровнях [Developer, Standard и Premium](https://azure.microsoft.com/pricing/details/api-management/).

## <a name="prerequisites"></a>предварительным требованиям

- Выполните задачи в кратком руководстве по [созданию экземпляра службы управления API Azure](get-started-create-service-instance.md).
- Импортируйте и опубликуйте экземпляр службы управления API. Дополнительные сведения см. в статье об [импорте и публикации](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Выберите пункт ![Стрелка](./media/api-management-howto-aad/arrow.png).
3. В поле поиска введите "api".
4. Щелкните **Службы управления API**.
5. Выберите свой экземпляр службы управления API.
6. В разделе **Безопасность** выберите **Удостоверения**.

    ![Внешние удостоверения](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Щелкните **+ Добавить** в верхней части экрана.

    Справа отобразится окно **Добавление поставщика удостоверений**.
8. В разделе **Тип поставщика** выберите **Azure Active Directory**.

    В окне отобразятся элементы управления, необходимые для ввода других требуемых сведений. Элементы управления включают **идентификатор клиента** и **секрет клиента** (подробнее они рассматриваются далее в этом руководстве).
9. Запишите **URL-адрес перенаправления**.  
10. В браузере откройте другую вкладку. 
11. Откройте [портал Azure](https://portal.azure.com).
12. Выберите пункт ![Стрелка](./media/api-management-howto-aad/arrow.png).
13. Введите "active". Отобразится **Azure Active Directory**.
14. Выберите **Azure Active Directory**.
15. В разделе **Управление** выберите **Регистрация приложения**.

    ![Регистрация приложения](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Щелкните **Регистрация нового приложения**.

    Справа появится окно **Создание**. Здесь вам нужно ввести информацию о приложении AAD.
17. Введите имя приложения.
18. Выберите в качестве типа приложения **Веб-приложение или API**.
19. В поле URL-адреса входа введите URL-адрес входа на свой портал разработчика. В этом примере URL-адрес входа: https://apimwithaad.portal.azure-api.net/signin.
20. Щелкните **Создать**, чтобы создать приложение.
21. Чтобы найти приложение, выберите **Регистрация приложений** и выполните поиск по имени.

    ![Регистрация приложения](./media/api-management-howto-aad/find-your-app.png)
22. После регистрации приложения перейдите к параметру **URL-адрес ответа** и убедитесь, что для URL-адреса перенаправления установлено значение, полученное на шаге 9. 
23. Чтобы настроить приложение (например, изменить **URL-адрес идентификатора приложения**) выберите **Свойства**.

    ![Регистрация приложения](./media/api-management-howto-aad/api-management-with-aad004.png)

    Если для этого приложения будут использоваться несколько служб каталогов Azure Active Directory, нажмите кнопку **Да** для параметра **Приложение является мультитенантным**. По умолчанию используется значение **Нет**.
24. Установите разрешения для приложений. Для этого выберите **Требуемые разрешения**.
25. Выберите свое приложение и установите флажки **Чтение данных каталога** и **Вход и чтение профиля пользователя**.

    ![Регистрация приложения](./media/api-management-howto-aad/api-management-with-aad005.png)

    Дополнительные сведения о приложении и делегированных разрешениях см. в разделе, посвященном [доступу к API Graph][Accessing the Graph API].
26. В окне слева скопируйте значение **идентификатора приложения**.

    ![Регистрация приложения](./media/api-management-howto-aad/application-id.png)
27. Вернитесь к приложению управления API. Должно отобразиться окно **Добавление поставщика удостоверений**. <br/>Вставьте значение **идентификатора приложения** в поле **Идентификатор клиента**.
28. Вернитесь к конфигурации Azure Active Directory и щелкните **Ключи**.
29. Укажите имя и срок действия ключа. 
30. Выберите команду **Сохранить**. Будет создан ключ.

    Скопируйте этот ключ в буфер обмена.

    ![Регистрация приложения](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Запишите этот ключ. После закрытия окна конфигурации Azure Active Directory нельзя будет снова отобразить ключ.
    > 
    > 
31. Вернитесь к приложению управления API. <br/>В окне **Добавление поставщика удостоверений** вставьте ключ в текстовое поле **Секрет клиента**.
32. В окне **Добавление поставщика удостоверений** есть текстовое поле **Разрешенные клиенты**. Укажите в нем, какие каталоги имеют доступ к интерфейсам API экземпляра службы управления API. <br/>Укажите домены экземпляров Azure Active Directory, к которым требуется предоставить доступ. Несколько доменов можно разделять символами начала новой строки, пробелами или запятыми.

    В разделе **Разрешенные клиенты** можно указывать несколько доменов. Прежде чем какой-либо пользователь сможет выполнить вход из домена, отличного от домена, в котором было зарегистрировано приложение, глобальный администратор этого другого домена должен предоставить разрешение для доступа приложения к данным каталога. Чтобы предоставить разрешение, глобальный администратор должен перейти по адресу `https://<URL of your developer portal>/aadadminconsent` (например, https://contoso.portal.azure-api.net/aadadminconsent), ввести имя домена клиента Active Directory, доступ к которому требуется предоставить, и щелкнуть "Отправить". В следующем примере глобальный администратор `miaoaad.onmicrosoft.com` пытается предоставить разрешение определенному порталу разработчика. 

33. Указав требуемую конфигурацию, нажмите кнопку **Добавить**.

    ![Регистрация приложения](./media/api-management-howto-aad/api-management-with-aad007.png)

После сохранения изменений пользователи в указанной службе Azure Active Directory могут входить на портал разработчика, выполняя действия, которые описаны в разделе [Вход на портал разработчика с помощью учетной записи Azure Active Directory](#log_in_to_dev_portal).

![Разрешения](./media/api-management-howto-aad/api-management-aad-consent.png)

На следующем экране глобальному администратору будет предложено подтвердить предоставление разрешения. 

![Разрешения](./media/api-management-howto-aad/api-management-permissions-form.png)

Если администратор, не являющийся глобальным, попытается выполнить вход до того, как глобальный администратор предоставит разрешения, то попытка входа завершится неудачно и появится экран сообщения об ошибке.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Добавление внешней группы Active Directory Azure

После включения доступа для пользователей в Azure Active Directory вы можете добавлять группы Azure Active Directory в управление API, чтобы упростить управление связью разработчиков в такой группе с требуемыми продуктами.

Чтобы настроить внешнюю группу Azure Active Directory, необходимо сначала настроить Azure Active Directory на вкладке удостоверений, выполнив процедуру, описанную в предыдущем разделе. 

Внешние группы Azure Active Directory добавляются с вкладки **Группы** в экземпляре службы управления API.

![Группы](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Выберите вкладку **Группы** .
2. Нажмите кнопку **Добавить группу AAD**.
3. Выберите группу, которую нужно добавить.
4. Нажмите кнопку **Выбор**.

Как только будет создана группа Azure Active Directory, вы сможете просмотреть и настроить свойства внешних групп после их добавления, щелкнув имя группы на вкладке **Группы**.

Здесь можно изменить **имя** и **описание** группы.
 
Пользователи из настроенных служб каталогов Azure Active Directory могут входить на портал разработчика, а затем просматривать и подписываться на любые группы, которые они могут видеть, выполняя инструкции из следующего раздела.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Вход на портал разработчика с помощью учетной записи Azure Active Directory

Чтобы войти на портал разработчика с помощью учетной записи Azure Active Directory, настроенной в предыдущих разделах, откройте новое окно браузера с помощью **URL-адреса входа** из конфигурации приложения Active Directory и нажмите кнопку **Azure Active Directory**.

![Портал разработчика][api-management-dev-portal-signin]

Введите учетные данные одного из пользователей в Azure Active Directory и нажмите кнопку **Вход**.

![Вход][api-management-aad-signin]

Может появиться запрос с регистрационной формой, если требуются какие-либо дополнительные сведения. Заполните эту регистрационную форму и нажмите кнопку **Регистрация**.

![Регистрация][api-management-complete-registration]

Теперь этот пользователь вошел на портал разработчика для вашего экземпляра службы управления API.

![Регистрация завершена][api-management-registration-complete]

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

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
