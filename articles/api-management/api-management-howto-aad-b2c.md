---
title: Авторизация учетных записей разработчиков с помощью Azure Active Directory B2C в службе управления API Azure | Документация Майкрософт
description: Сведения об авторизации пользователей с помощью Azure Active Directory B2C в службе управления API.
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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: a6e7aad6c3d20a67ecba66c49be4efcdebdf718a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153427"
---
> [!WARNING]
> Интеграция Azure Active Directory B2C доступна в только на уровнях [Developer, Standard и Premium](https://azure.microsoft.com/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Как авторизовать учетные записи разработчиков с помощью Azure Active Directory B2C в службе управления API Azure
## <a name="overview"></a>Обзор
Azure Active Directory B2C — это облачное решение, позволяющее управлять удостоверениями в веб-приложениях и мобильных приложениях, с которыми взаимодействуют клиенты. Его можно использовать для управления доступом к порталу разработчика. В этом руководстве объясняется, как настроить службу управления API для интеграции с Azure Active Directory B2C. Сведения о предоставлении доступа к порталу разработчика с помощью классической службы Azure Active Directory см. в статье [Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure].

> [!NOTE]
> Для выполнения шагов в этом руководстве вам сначала потребуется клиент Azure Active Directory B2C для создания в нем приложения. Кроме того, необходимо настроить политики регистрации и входа. Дополнительные сведения см. в статье [Azure Active Directory B2C: регистрация и вход пользователей в приложения].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory B2C

1. Чтобы начать работу, щелкните **Портал издателя** на портале Azure для службы управления API. Будет открыт портал издателя службы управления API.

   ![Портал издателя][api-management-management-console]

   > [!NOTE]
   > Если вы еще не создали экземпляр службы управления API, см. сведения в разделе [Создание экземпляра управления API][Create an API Management service instance] руководства [Начало работы со службой управления Azure API][Get started with Azure API Management].

2. В меню **Управление API** выберите **Безопасность**. На вкладке **Удостоверения** выберите **Azure Active Directory B2C**.

  ![Внешние удостоверения 1][api-management-howto-aad-b2c-security-tab]

3. Запишите **URL-адрес перенаправления** и переключитесь на Azure Active Directory B2C на портале Azure.

  ![Внешние удостоверения 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Нажмите кнопку **Приложения**.

  ![Регистрация нового приложения 1][api-management-howto-aad-b2c-portal-menu]

5. Нажмите кнопку **Добавить**, чтобы создать новое приложение Azure Active Directory B2C.

  ![Регистрация нового приложения 2][api-management-howto-aad-b2c-add-button]

6. В колонке **Новое приложение** введите имя для приложения. Выберите **Да** под параметром **Веб-приложения и веб-API** и **Да** под параметром **Разрешить неявный поток**. Скопируйте **URL-адрес перенаправления** в разделе **Azure Active Directory B2C** на вкладке **Удостоверения** портала издателя и вставьте его в текстовое поле **URL-адрес ответа**.

  ![Регистрация нового приложения 3][api-management-howto-aad-b2c-app-details]

7. Нажмите кнопку **Создать** . Когда приложение будет создано, оно появится в колонке **Приложения**. Щелкните имя приложения, чтобы просмотреть сведения о нем.

  ![Регистрация нового приложения 4][api-management-howto-aad-b2c-app-created]

8. В колонке **Свойства** скопируйте в буфер обмена **Идентификатор приложения**.

  ![Идентификатор приложения 1][api-management-howto-aad-b2c-app-id]

9. Вернитесь на портал издателя и вставьте скопированный идентификатор в текстовое поле **Идентификатор клиента**.

  ![Идентификатор приложения 2][api-management-howto-aad-b2c-client-id]

10. Вернитесь на портал Azure, щелкните раздел **Ключи**, а затем нажмите кнопку **Создать ключ**. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию и отобразить **Ключ приложения**. Скопируйте этот ключ в буфер обмена.

  ![Ключ приложения 1][api-management-howto-aad-b2c-app-key]

11. Вернитесь на портал издателя и вставьте скопированный ключ в текстовое поле **Секрет клиента** .

  ![Ключ приложения 2][api-management-howto-aad-b2c-client-secret]

12. Укажите имя домена клиента Azure Active Directory B2C в поле **Разрешенный клиент**.

  ![Разрешенный клиент][api-management-howto-aad-b2c-allowed-tenant]

13. Укажите **политику регистрации** и **политику входа**. Дополнительно можно также указать **политику редактирования профиля** и **политику сброса пароля**.

  ![Политики][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Дополнительные сведения о политиках см. в статье [Azure Active Directory B2C: расширяемая инфраструктура политик].

14. После указания требуемой конфигурации нажмите кнопку **Сохранить**.

  После сохранения изменений разработчики смогут создавать новые учетные записи и входить на портал разработчика с помощью Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Регистрация учетных записей разработчиков с помощью Azure Active Directory B2C

1. Чтобы зарегистрировать учетную запись разработчика с помощью Azure Active Directory B2C, откройте новое окно браузера и перейдите на портал разработчика. Нажмите кнопку **Зарегистрироваться**.

   ![Портал разработчика 1][api-management-howto-aad-b2c-dev-portal]

2. Выберите регистрацию с помощью **Azure Active Directory B2C**.

   ![Портал разработчика 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Вы будете перенаправлены к политике регистрации, настроенной в предыдущем разделе. Выберите регистрацию с помощью адреса электронной почты или существующей учетной записи социальных сетей.

   > [!NOTE]
   > Если Azure Active Directory B2C является единственным активным параметром на вкладке **Удостоверения** на портале издателя, вы будете сразу перенаправлены к политике регистрации.

   ![Developer portal][api-management-howto-aad-b2c-dev-portal-b2c-options]

   После завершения регистрации вы будете перенаправлены обратно на портал разработчика. Теперь вы вошли на портал разработчика вашего экземпляра службы управления API.

    ![Регистрация завершена][api-management-registration-complete]

## <a name="next-steps"></a>Дополнительная информация

*  [Azure Active Directory B2C: регистрация и вход пользователей в приложения]
*  [Azure Active Directory B2C: расширяемая инфраструктура политик]
*  [Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Microsoft]
*  [Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Google+]
*  [Azure Active Directory B2C: регистрация и вход пользователей с помощью учетных записей LinkedIn]
*  [Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Facebook]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Azure Active Directory B2C: регистрация и вход пользователей в приложения]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: расширяемая инфраструктура политик]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Microsoft]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Google+]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Facebook]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Azure Active Directory B2C: регистрация и вход пользователей с помощью учетных записей LinkedIn]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
