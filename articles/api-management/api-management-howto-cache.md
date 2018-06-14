---
title: Добавление кэширования для повышения производительности в службе управления API Azure | Документация Майкрософт
description: Сведения об уменьшении задержки, использования пропускной способности и загрузки веб-службы для вызовов службы управления API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7458ad6e0a864d742f74ce743ce3179594113c00
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
ms.locfileid: "26127783"
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Добавление кэширования для повышения производительности в службе управления API Azure
Операции в управлении API можно настроить для кэширования ответов. Кэширование ответов может значительно уменьшить время задержки API, потребляемую пропускную способность, и нагрузку на веб-службу применительно к данным, которые изменяются редко.
 
Дополнительные сведения о кэшировании см. в статьях [Политики кэширования в службе управления API](api-management-caching-policies.md) и [Пользовательское кэширование в службе управления API Azure](api-management-sample-cache-by-key.md).

![Политики кэширования](media/api-management-howto-cache/cache-policies.png)

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * добавление кэширования ответов для API;
> * проверка кэширования в действии.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством:

+ [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md)
+ [импортируйте и опубликуйте API.](import-and-publish.md)

## <a name="caching-policies"> </a>Добавление политик кэширования

При использовании политик кэширования в этом примере на первый запрос операции **GetSpeakers** возвращается ответ из серверной службы. Этот ответ кэшируется, а также отмечается ключом указанных заголовков и параметров строки запроса. Для последующих вызовов операции с совпадающими параметрами будет возвращаться кэшированный ответ до тех пор, пока не истечет срок действия кэша.

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).
2. Перейдите к экземпляру службы управления API Azure.
3. Выберите вкладку **API**.
4. В списке API выберите **Demo Conference API**.
5. Выберите **GetSpeakers**.
6. В верхней части экрана выберите вкладку **Конструктор**.
7. В окне **Обработка входящих запросов** щелкните треугольник (рядом со значком карандаша).

    ![Редактор кода](media/api-management-howto-cache/code-editor.png)
8. Выберите **Редактор кода**.
9. В элемент **inbound** добавьте следующую политику:

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

10. В элемент **outbound** добавьте такую политику:

        <cache-store caching-mode="cache-on" duration="20" />

    **Длительность** указывает интервал срока действия кэшированных ответов. В этом примере интервал составляет **20** секунд.

## <a name="test-operation"> </a>Вызов операции и проверка кэширования
Чтобы увидеть кэширование в действии, вызовите операцию на портале разработчика.

1. На портале Azure перейдите к экземпляру службы управления API Azure.
2. Выберите вкладку **Интерфейсы API**.
3. Выберите API, в который вы добавили политики кэширования.
4. Щелкните операцию **GetSpeakers**.
5. Перейдите на вкладку **Тестирование** в правом верхнем меню.
6. Нажмите кнопку **Отправить**.

## <a name="next-steps"> </a>Дальнейшие действия
* Дополнительные сведения о политиках кэширования см. в разделе [Политики кэширования][Caching policies] [справочника по политикам управления API][API Management policy reference].
* Сведения о кэшировании элементов по ключу с помощью выражений политики см. в статье [Пользовательское кэширование в службе управления API Azure](api-management-sample-cache-by-key.md).

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
