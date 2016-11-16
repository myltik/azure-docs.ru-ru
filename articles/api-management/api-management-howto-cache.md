---
title: "Добавление кэширования для повышения производительности в службе управления API Azure | Документация Майкрософт"
description: "Сведения об уменьшении задержки, использования пропускной способности и загрузки веб-службы для вызовов службы управления API."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8cdb37fceb7b598c92b7b3cd41655c87c74e639


---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Добавление кэширования для повышения производительности в службе управления API Azure
Операции в управлении API можно настроить для кэширования ответов. Кэширование ответов может значительно уменьшить время задержки API, потребляемую пропускную способность, и нагрузку на веб-службу применительно к данным, которые изменяются редко.

В этом руководстве показано, как добавить кэширование ответов для API и настроить политики для примеров операций Echo API. Затем можно вызвать операцию из портала разработчика, чтобы проверить кэширование в действии.

> [!NOTE]
> Сведения о кэшировании элементов по ключу с помощью выражений политики см. в статье [Пользовательское кэширование в службе управления API Azure](api-management-sample-cache-by-key.md).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем выполнять действия из этого руководства, необходимо настроить экземпляр службы управления API с API и продуктом. Если экземпляр службы управления API еще не создан, выполните инструкции из раздела [Создание экземпляра управления API][Create an API Management service instance] в статье [Начало работы со службой управления Azure API][Get started with Azure API Management].

## <a name="configure-caching"> </a>Настройка операции для кэширования
На этом этапе необходимо проверить параметры кэширования операции **GET Resource (cached)** примера Echo API.

> [!NOTE]
> В каждом экземпляре службы управления API предварительно настроен Echo API, с которым можно экспериментировать при изучении службы управления API. Дополнительные сведения см. в статье [Начало работы со службой управления Azure API][Get started with Azure API Management].
> 
> 

Для начала работы щелкните **Publisher portal** (Портал издателя) на портале Azure для службы управления API. Будет открыт портал издателя службы управления API.

![Портал издателя][api-management-management-console]

Щелкните **API** в меню **Управление API** слева, а затем выберите **Echo API**.

![Echo API][api-management-echo-api]

Перейдите на вкладку **Операции** и в списке **Операции** выберите операцию **GET Resource (cached)**.

![Операции интерфейса Echo API][api-management-echo-api-operations]

Перейдите на вкладку **Кэширование** , чтобы просмотреть параметры кэширования для этой операции.

![Вкладка "Кэширование"][api-management-caching-tab]

Чтобы включить кэширование для операции, установите флажок **Включить** . В этом примере кэширование включено.

Каждый ответ операции содержит ключ на основе значений в полях **В зависимости от параметров строки запроса** и **В зависимости от заголовков**. Если вы хотите кэшировать несколько ответов на основании параметров строки запроса или заголовков, то такой режим можно настроить с помощью этих полей.

**Длительность** указывает интервал срока действия кэшированных ответов. В этом примере интервал составляет **3600** секунд, что эквивалентно одному часу.

При использовании конфигурации кэширования в этом примере на первый запрос операции **GET Resource (cached)** возвращается ответ из серверной службы. Этот ответ будет кэшироваться, а также отмечаться (ключом) указанных заголовков и параметров строки запроса. Для последующих вызовов операции с совпадающими параметрами будет возвращаться кэшированный ответ до тех пор, пока не истечет срок действия кэша.

## <a name="caching-policies"> </a>Анализ политик кэширования
На этом шаге необходимо проверить параметры кэширования для операции **GET Resource (cached)** образца Echo API.

После настройки параметров кэширования на вкладке **Кэширование** для операции добавляются политики кэширования. Эти политики можно просматривать и изменять в редакторе политик.

Щелкните **Политики** в меню **Управление API** слева и выберите **Echo API / GET Resource (cached)** из раскрывающегося списка **Операция**.

![Операция с диапазоном политик][api-management-operation-dropdown]

При этом в редакторе политик отображаются политики для данной операции.

![Редактор политик API Management][api-management-policy-editor]

Определение политики для этой операции содержит политики, определяющие конфигурацию кэширования, которые были проанализированы с помощью вкладки **Кэширование** на предыдущем этапе.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

> [!NOTE]
> Изменения, внесенные в политики кэширования в редакторе политик, будут отражаться на вкладке **Кэширование** операции (и наоборот).
> 
> 

## <a name="test-operation"> </a>Вызов операции и проверка кэширования
Чтобы увидеть процесс кэширования в действии, можно вызвать операцию из портала разработчика. В правом верхнем меню выберите пункт **Developer portal** (Портал разработчика).

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **API** в меню вверху и выберите **Echo API**.

![Echo API][api-management-apis-echo-api]

> Если есть только один настроенный или видимый API для данной учетной записи, тогда щелчок по API сразу приведет к операциям для этого API.
> 
> 

Выберите операцию **GET Resource (cached)** и щелкните **Открыть консоль**.

![Открытие консоли][api-management-open-console]

Консоль позволяет вызывать операции прямо на портале разработчика.

![Консоль][api-management-console]

Сохраните значения по умолчанию для **param1** и **param2**.

Выберите в раскрывающемся списке **subscription-key** требуемый ключ. Если ваша учетная запись содержит только одну подписку, он уже будет выбран.

Введите **sampleheader:value1** в текстовом поле **Заголовки запроса**.

Щелкните **HTTP Get** и запишите значение заголовков ответа.

Введите в текстовое поле **Заголовки запроса** значение **sampleheader:value2** и нажмите **HTTP Get**.

Следует отметить, что значение **sampleheader** по-прежнему совпадает со значение **value1** в ответе. Попробуйте ввести другие значения и убедитесь, что возвращается кэшированный ответ из первого вызова.

Введите в поле **param2** значение **25** и нажмите **HTTP Get**.

Следует отметить, что значением **sampleheader** в ответе теперь является **value2**. Так как результаты операции определяются строкой запроса, то предыдущий кэшированный ответ не возвращается.

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


[Как добавлять операции в API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Мониторинг и аналитика]: api-management-monitoring.md
[Добавление интерфейсов API к продукту]: api-management-howto-add-products.md#add-apis
[Публикация продукта]: api-management-howto-add-products.md#publish-product
[Приступая к работе со службой управления API]: api-management-get-started.md

[Справочник по политикам службы управления API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Политики кэширования]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Создание экземпляра службы управления API]: api-management-get-started.md#create-service-instance

[Настройка операции для кэширования]: #configure-caching
[Анализ политик кэширования]: #caching-policies
[Вызов операции и проверка кэширования]: #test-operation
[Дальнейшие действия]: #next-steps



<!--HONumber=Nov16_HO2-->


