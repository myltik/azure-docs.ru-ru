---
title: "Авторизация учетных записей разработчиков с помощью Azure Active Directory B2C в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как авторизовать пользователей с помощью Azure Active Directory B2C в службе управления API."
services: api-management
documentationcenter: API Management
author: miaojiang
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: c772f242f36e401c3b9f4b9be2366139c8a871f8
ms.openlocfilehash: 6a8505e52c2f422375063d6a780a6a205d768462
ms.lasthandoff: 02/24/2017

---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-b2c-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory B2C в управлении API Azure
## <a name="overview"></a>Обзор
Azure Active Directory B2C — это облачное решение, позволяющее управлять удостоверениями в веб-приложениях и мобильных приложениях, с которыми взаимодействуют клиенты. Его можно использовать для управления доступом к порталу разработчика. В этом руководстве объясняется, как настроить службу управления API для интеграции с Azure Active Directory B2C. Сведения о предоставлении доступа к порталу разработчика с помощью классической службы Azure Active Directory см. в статье [Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure].

> [!NOTE]
> Чтобы выполнить инструкции из этого руководства, вам понадобятся клиент Azure Active Directory B2C для создания приложения и подготовленные политики регистрации и входа. Дополнительные сведения см. в статье [Azure Active Directory B2C: регистрация и вход пользователей в приложения].
> 
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory-b2c"></a>Авторизация учетных записей разработчиков с помощью Azure Active Directory B2C

Чтобы начать работу, щелкните **Портал издателя** на портале Azure для службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

> [!NOTE]
> Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра управления API][Create an API Management service instance] в руководстве [Начало работы со службой управления Azure API][Get started with Azure API Management].
> 
> 

Выберите **Безопасность** в меню **Управление API**. На вкладке **Удостоверения** выберите **Azure Active Directory B2C**.

![Внешние удостоверения][api-management-howto-aad-b2c-security-tab]

Запишите **URL-адрес перенаправления** и переключитесь на Azure Active Directory B2C на портале Azure.

![Внешние удостоверения][api-management-howto-aad-b2c-security-tab-reply-url]

Нажмите кнопку **Приложения**.

![Регистрация нового приложения][api-management-howto-aad-b2c-portal-menu]

Нажмите кнопку **Добавить**, чтобы создать новое приложение Azure Active Directory B2C.

![Регистрация нового приложения][api-management-howto-aad-b2c-add-button]

В колонке **Новое приложение** введите имя для приложения. Выберите **Да** под параметром **Веб-приложения и веб-API** и **Да** под параметром **Разрешить неявный поток**. Скопируйте **URL-адрес перенаправления** в разделе **Azure Active Directory B2C** на вкладке **Удостоверения** портала издателя и вставьте его в текстовое поле **URL-адрес ответа**. 

![Регистрация нового приложения][api-management-howto-aad-b2c-app-details]

Нажмите кнопку **Создать** . Когда приложение будет создано, оно появится в колонке **Приложения**. Щелкните имя приложения, чтобы просмотреть сведения о нем. 

![Регистрация нового приложения][api-management-howto-aad-b2c-app-created]

В колонке **Свойства** скопируйте в буфер обмена **Идентификатор приложения**.

![Идентификатор приложения API.][api-management-howto-aad-b2c-app-id]

Вернитесь на портал издателя и вставьте скопированный идентификатор в текстовое поле **Идентификатор клиента**.

![Идентификатор приложения API.][api-management-howto-aad-b2c-client-id]

Вернитесь на портал Azure, щелкните раздел **Ключи**, а затем нажмите кнопку **Создать ключ**. Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию и отобразить **Ключ приложения**. Скопируйте этот ключ в буфер обмена. 

![Ключ приложения][api-management-howto-aad-b2c-app-key]

Вернитесь на портал издателя и вставьте скопированный ключ в текстовое поле **Секрет клиента** .

![Ключ приложения][api-management-howto-aad-b2c-client-secret]

Укажите в поле **Разрешенный клиент** имя домена клиента Azure Active Directory B2C.

![Разрешенный клиент][api-management-howto-aad-b2c-allowed-tenant]

Укажите **политику регистрации** и **политику входа**. Дополнительно можно также указать **политику редактирование профиля** и **политику сброса пароля**. 

![Политики][api-management-howto-aad-b2c-policies]

> Дополнительные сведения о политиках см. в статье [Azure Active Directory B2C: расширяемая инфраструктура политик].
>
>

После указания требуемой конфигурации нажмите кнопку **Сохранить**.

После сохранения изменений разработчики смогут создавать новые учетные записи и входить на портал разработчика с помощью AAD B2C. 

## <a name="how-to-sign-up-for-a-developer-account-using-azure-active-directory-b2c"></a>Регистрация учетных записей разработчиков с помощью Azure Active Directory B2C

Чтобы зарегистрировать учетную запись разработчика с помощью Azure Active Directory B2C, откройте новое окно браузера и перейдите на портал разработчика. Нажмите кнопку **Зарегистрироваться**.

![Портал разработчика][api-management-howto-aad-b2c-dev-portal]

Выберите регистрацию с помощью **Azure Active Directory B2C**. 

![Портал разработчика][api-management-howto-aad-b2c-dev-portal-b2c-button]

Вы будете перенаправлены к политике регистрации, настроенной в предыдущем разделе. Там вы сможете выбрать регистрацию с помощью адреса электронной почты или учетной записи социальных сетей. 

> [!NOTE]
> Если Azure Active Directory B2C является единственным активным параметром на вкладке **Удостоверения** на портале издателя, вы будете сразу перенаправлены к политике регистрации.
>
>

![Портал разработчика][api-management-howto-aad-b2c-dev-portal-b2c-options]

После завершения регистрации вы будете перенаправлены обратно на портал разработчика. Теперь вы вошли на портал разработчика вашего экземпляра службы управления API.

![Регистрация завершена][api-management-registration-complete]

## <a name="next-step"></a>Дальнейшие действия

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
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Azure Active Directory B2C: регистрация и вход пользователей в приложения]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Авторизация учетных записей разработчиков с помощью Azure Active Directory в управлении API Azure]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: расширяемая инфраструктура политик]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Azure Active Directory (AD) B2C: организация регистрации и входа для потребителей с учетными записями Microsoft]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Google+]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Azure Active Directory B2C: регистрация и вход пользователей с учетными записями Facebook]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Azure Active Directory B2C: регистрация и вход пользователей с помощью учетных записей LinkedIn]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account


