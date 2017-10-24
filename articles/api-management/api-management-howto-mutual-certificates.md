---
title: "Защита внутренних служб с помощью проверки подлинности на основе сертификата клиента в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как защитить фоновые службы посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 43453331-39b2-4672-80b8-0a87e4fde3c6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 464e5e2b2d9678bef002497588eac60db074f9bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management
Служба API Management дает возможность защищать доступ к фоновым службам API с помощью сертификатов клиента. В этом руководстве описывается, как управлять сертификатами на портале издателя API и как настроить использование сертификата в  API для доступа к фоновой службе.

Дополнительные сведения об управлении сертификатами с помощью REST API управления API см. в разделе, посвященном [объекту сертификата REST API управления API Azure][Azure API Management REST API Certificate entity].

## <a name="prerequisites"> </a>Предварительные требования
В этом руководстве описано, как настроить для экземпляра службы API Management проверку подлинности с помощью сертификата клиента при доступе из API к фоновой службе. Перед выполнением инструкций, приведенных в этой статье, во внутренней службе необходимо настроить аутентификацию на основе сертификата клиента. [Сведения о том, как настроить аутентификацию на основе сертификата на веб-сайтах Azure, см. в этой статье][to configure certificate authentication in Azure WebSites refer to this article]. Кроме того, нужен доступ к сертификату, который следует отправить на портал издателя управления API, и его паролю.

## <a name="step1"> </a>Отправка сертификата клиента
Чтобы начать работу, щелкните **Publisher portal** (Портал издателя) на портале Azure для службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя API][api-management-management-console]

> Если экземпляр службы управления API еще не создан, см. раздел [Создание экземпляра управления API][Create an API Management service instance] в руководстве [Начало работы со службой управления Azure API][Get started with Azure API Management].
> 
> 

В левой части экрана в меню **Управление API** выберите пункт **Безопасность**, а затем перейдите на вкладку **Сертификаты клиента**.

![Client certificates][api-management-security-client-certificates]

Чтобы добавить новый сертификат, щелкните ссылку **Upload certificate**(Загрузить сертификат).

![Upload certificate][api-management-upload-certificate]

Выберите сертификат и введите пароль к нему.

> Сертификат должен быть в формате **PFX** . Разрешено использовать самозаверяющие сертификаты.
> 
> 

![Upload certificate][api-management-upload-certificate-form]

Нажмите кнопку **Upload** (Загрузить).

> После этого будет проверен пароль к сертификату. Если он неправильный, появится сообщение об ошибке.
> 
> 

![Сертификат добавлен][api-management-certificate-uploaded]

После добавления сертификат появляется на вкладке **Сертификаты клиента**. Если у вас несколько сертификатов, запишите субъект или последние четыре знака отпечатка. Эти данные используются для выбора сертификата при настройке API для использования сертификатов, как описано в разделе [Настройка API для проверки подлинности шлюза с помощью сертификата клиента][Configure an API to use a client certificate for gateway authentication] ниже.

> Чтобы отключить проверку цепочки сертификатов при использовании, например, самозаверяющего сертификата, выполните действия, описанные в [этом разделе](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end) часто задаваемых вопросов.
> 
> 

## <a name="step1a"> </a>Удаление сертификата клиента
Чтобы удалить сертификат, щелкните рядом с ним ссылку **Delete** (Удалить).

![Удаление сертификата][api-management-certificate-delete]

Чтобы подтвердить действие, нажмите кнопку **Yes, delete it** (Да, удалить).

![Подтверждение удаления][api-management-confirm-delete]

Если сертификат используется интерфейсом API, появляется предупреждение. Перед удалением сертификата нужно сначала удалить его из всех API, которые настроены на его использование.

![Подтверждение удаления][api-management-confirm-delete-policy]

## <a name="step2"> </a>Настройка API для проверки подлинности шлюза с помощью сертификата клиента
В левой части экрана в меню **Управление API** выберите пункт **Интерфейсы API**, щелкните имя нужного API и перейдите на вкладку **Безопасность**.

![Безопасность API][api-management-api-security]

В раскрывающемся списке **С учетными данными** выберите пункт **Сертификаты клиента**.

![Client certificates][api-management-mutual-certificates]

В раскрывающемся списке **Client certificate** (Сертификат клиента) выберите нужный сертификат. Если сертификатов несколько, то выбрать нужный можно по субъекту или последним четырем символам отпечатка, которые вы записали ранее.

![Выбор сертификата][api-management-select-certificate]

Чтобы сохранить изменение конфигурации в API, нажмите кнопку **Save** (Сохранить).

> Изменение вступает в силу немедленно, и для проверки подлинности на фоновом сервере при вызове операций этого API теперь будет использоваться сертификат.
> 
> 

![Сохранение изменений в API][api-management-save-api]

> Если сертификат настроен для проверки подлинности шлюза при доступе к серверной службе API, он становится частью политики для этого API, и его можно увидеть в редакторе политики.
> 
> 

![Политика сертификата][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>Самозаверяющие сертификаты

Если вы используете самозаверяющие сертификаты, необходимо отключить проверку цепочки сертификатов для взаимодействия управления API с серверной системой, иначе вернется код ошибки 500. Для этого используйте командлеты PowerShell [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (для новой серверной части) или [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/en-us/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (для существующей серверной части) и задайте для параметра `-SkipCertificateChainValidation` значение `True`.

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о других способах защиты фоновой службы, таких как базовая проверка подлинности HTTP или проверка подлинности с общим секретом, см. в следующем видео.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



