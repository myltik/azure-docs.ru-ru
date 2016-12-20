---
title: "Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management"
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
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad14ed8b36d6d0a2121c32fd9a54de97e8b02342


---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management
Служба API Management дает возможность защищать доступ к фоновым службам API с помощью сертификатов клиента. В этом руководстве описывается, как управлять сертификатами на портале издателя API и как настроить использование сертификата в  API для доступа к фоновой службе.

Дополнительные сведения об управлении сертификатами с помощью REST API управления API см. в разделе, посвященном [объекту сертификата REST API управления API Azure][Сущность сертификата REST API службы управления API Azure].

## <a name="prerequisites"> </a>Предварительные требования
В этом руководстве описано, как настроить для экземпляра службы API Management проверку подлинности с помощью сертификата клиента при доступе из API к фоновой службе. Перед выполнением инструкций, приведенных в этой статье, в серверной службе необходимо настроить проверку подлинности на основе сертификата клиента. [Настройка проверки подлинности сертификата в службе веб-сайтов Azure описана в этой статье.][Настройка проверки подлинности сертификата в службе веб-сайтов Azure описана в этой статье.]. Кроме того, нужен доступ к сертификату, который следует отправить на портал издателя управления API, и его паролю.

## <a name="step1"> </a>Отправка сертификата клиента
Чтобы начать работу, щелкните **Publisher portal** (Портал издателя) на портале Azure для службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя API][api-management-management-console]

> Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Создание экземпляра управления API] в статье [Начало работы со службой управления Azure API][Приступая к работе со службой управления API].
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

После добавления сертификат появляется на вкладке **Сертификаты клиента**. Если у вас несколько сертификатов, запишите субъект или последние четыре символа отпечатка. Эти данные используются для выбора сертификата при настройке API для применения сертификатов, как описано в разделе [Настройка API для проверки подлинности шлюза с помощью сертификата клиента][Настройка API для проверки подлинности шлюза с помощью сертификата клиента] ниже.

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



[Как добавлять операции в API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Мониторинг и аналитика]: ../api-management-monitoring.md
[Добавление интерфейсов API к продукту]: api-management-howto-add-products.md#add-apis
[Публикация продукта]: api-management-howto-add-products.md#publish-product
[Приступая к работе со службой управления API]: api-management-get-started.md
[Справочник по политикам службы управления API]: api-management-policy-reference.md
[Политики кэширования]: api-management-policy-reference.md#caching-policies
[Создание экземпляра управления API]: api-management-get-started.md#create-service-instance

[Сущность сертификата REST API службы управления API Azure]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Настройка проверки подлинности сертификата в службе веб-сайтов Azure описана в этой статье.]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Предварительные требования]: #prerequisites
[Отправка сертификата клиента]: #step1
[Удаление сертификата клиента]: #step1a
[Настройка API для проверки подлинности шлюза с помощью сертификата клиента]: #step2
[Проверка конфигурации с помощью вызова операции на портале разработчика]: #step3
[Дальнейшие действия]: #next-steps






<!--HONumber=Nov16_HO3-->


