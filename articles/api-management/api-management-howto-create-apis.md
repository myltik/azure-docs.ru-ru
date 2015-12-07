<properties 
	pageTitle="Как создавать интерфейсы API в Azure API Management" 
	description="Сведения о создании и настройке API в службе управления API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="sdanie"/>

# Как создавать интерфейсы API в Azure API Management

API в API Management представляет набор операций, которые могут быть вызваны клиентскими приложениями. Новые интерфейсы API создаются на портале издателя, после чего добавляются требуемые операции. После добавления операций интерфейс API добавляется к продукту и может быть опубликован. После публикации API на него могут подписаться разработчики для дальнейшего использования.

В этом руководстве описывается первый этап в процессе создания и настройки нового интерфейса API в управлении API. Дополнительные сведения о добавлении операций и публикации продукта см. в разделе [Как добавлять операции к API][] и [Как создать и публиковать продукт][].

## <a name="create-new-api"></a>Создание нового API

API создаются и настраиваются на портале издателя. Чтобы перейти на портал издателя, щелкните **Управление** на портале Azure для службы управления API.

![Портал издателя][api-management-management-console]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][] в руководстве [Начинаем работу с API Management][].

Щелкните **API** в расположенном слева меню **API Management**, а затем **Добавить API**.

![Создание API][api-management-create-api]

Для настройки нового API используйте окно **Добавление нового API**.

![Добавление нового API][api-management-add-new-api]

Следующие поля используются для настройки нового API.

-	**Название веб-API** представляет собой уникальное описательное имя для API. Оно выводится на порталах разработчика и управления.
-	**URL-адрес веб-службы** ссылается на реализующую API HTTP-службу. Портал управления API направит запросы по этому адресу.
-	**Суффикс URL-адреса веб-API** добавляется к основному URL-адресу для службы управления API. Основной URL-адрес является общим для всех интерфейсов API, размещенных в экземпляре службы API Management. API Management отличает интерфейсы API по их суффиксу. Следовательно, суффикс должен быть уникальным для каждого API для заданного издателя.
-	**Схема URL-адреса веб-API** определяет, какой протокол будет использоваться для доступа к API. По умолчанию указан протокол HTTPs.
-	Чтобы при необходимости добавить этот новый API в продукт, в раскрывающемся списке **Продукты (необязательно)** выберите продукт. Этот шаг можно повторить несколько раз, чтобы добавить API в несколько продуктов.

После настройки нужных значений нажмите кнопку **Сохранить**. После создания нового API на портале издателя выводится страница сводных данных для API.

![Сводные данные API][api-management-api-summary]

## <a name="configure-api-settings"> </a>Настройка параметров API

Для проверки и изменения конфигурации для API можно использовать вкладку **Параметры**. **Название веб-API**, **URL веб-службы** и **Суффикс URL-адреса веб-API** изначально настраиваются при создании интерфейса API и могут быть здесь изменены. Параметр **Описание** предоставляет необязательное описание, а параметр **Схема URL-адреса веб-API** определяет, какие протоколы будут использоваться для доступа к API.

![Параметры API][api-management-api-settings]

Чтобы настроить проверку подлинности шлюза для фоновой службы, реализующей API, перейдите на вкладку **Безопасность**. Для настройки **Обычной проверки подлинности HTTP** или проверки подлинности с помощью **Сертификатов клиента** можно использовать раскрывающийся список **С учетными данными**. Чтобы использовать обычную проверку подлинности HTTP, просто введите нужные учетные данные. Сведения об использовании проверки подлинности с помощью сертификатов клиентов см. в статье [Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management][].

На вкладке **Безопасность** также можно настроить **авторизацию пользователя** с помощью OAuth 2.0. Дополнительные сведения см. в разделе [Авторизация учетных записей разработчиков с помощью протокола OAuth 2.0 в управлении API Azure][]

![Параметры базовой проверки подлинности][api-management-api-settings-credentials]

Щелкните **Сохранить** для сохранения любых изменений, внесенных в параметры API.

## <a name="next-steps"> </a>Дальнейшие действия

После того, как API создан, и параметры настроены, следует добавить операции в API, добавить API к продукту и опубликовать его, чтобы данный интерфейс стал доступен для разработчиков. Дополнительные сведения см. в следующих руководствах.

-	[Как добавлять операции в API][]
-	[Как создать и опубликовать продукт][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Как добавлять операции в API]: api-management-howto-add-operations.md
[Как добавлять операции к API]: api-management-howto-add-operations.md
[Как создать и опубликовать продукт]: api-management-howto-add-products.md
[Как создать и публиковать продукт]: api-management-howto-add-products.md

[Начинаем работу с API Management]: api-management-get-started.md
[Создание экземпляра службы API Management]: api-management-get-started.md#create-service-instance
[Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management]: api-management-howto-mutual-certificates.md
[Авторизация учетных записей разработчиков с помощью протокола OAuth 2.0 в управлении API Azure]: api-management-howto-oauth2.md

<!---HONumber=AcomDC_1125_2015-->