---
title: Авторизация учетных записей разработчиков с помощью протокола OAuth 2.0 в службе управления Azure API | Документация Майкрософт
description: Сведения об авторизации пользователей с помощью OAuth 2.0 в службе управления API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: f3611fa4da571dd74d844c7fad45788ece372be4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937238"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Авторизация учетных записей разработчиков с помощью протокола OAuth 2.0 в службе управления Azure API
Многие интерфейсы API поддерживают протокол [OAuth 2.0](http://oauth.net/2/) , позволяющий защитить API, а также и предоставлять доступ только действительным пользователям и только к ресурсам, на которые эти пользователи имеют право. Чтобы использовать интерактивную  консоль разработчика управления API Azure с такими API, служба позволяет настроить экземпляр службы для работы с API с поддержкой OAuth 2.0.

## <a name="prerequisites"> </a>Предварительные требования
В этом руководстве описано, как настроить экземпляр службы API Management для авторизации учетных записей разработчиков по протоколу OAuth 2.0. В нем не рассматривается настройка поставщика OAuth 2.0. Настройка каждого поставщика OAuth 2.0 имеет свои особенности, хотя инструкции в целом схожи, а данные, необходимые для настройки этого протокола в экземпляре службы API Management, одинаковы. В примерах в этом разделе в качестве поставщика OAuth 2.0 используется служба Azure Active Directory.

> [!NOTE]
> Дополнительные сведения о настройке OAuth 2.0 с использованием Azure Active Directory см. в примере [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].
> 
> 

## <a name="step1"> </a>Настройка сервера авторизации OAuth 2.0 в управлении API
Чтобы начать работу, щелкните **Publisher portal** (Портал издателя) на портале Azure для службы управления API.

![Портал издателя][api-management-management-console]

> [!NOTE]
> Если экземпляр службы управления API еще не создан, выполните инструкции в статье [Создание экземпляра службы управления API Azure][Create an API Management service instance].
> 
> 

В меню **Управление API** слева выберите пункт **Безопасность**, перейдите на вкладку **OAuth 2.0** и щелкните ссылку **Add authorization server** (Добавить сервер авторизации).

![OAuth 2.0][api-management-oauth2]

После перехода по ссылке **Add authorization server**открывается форма нового сервера авторизации.

![Новый сервер][api-management-oauth2-server-1]

В полях **Имя** и **Описание** введите имя и (при желании) описание. 

> [!NOTE]
> Эти поля служат для идентификации сервера авторизации OAuth 2.0 в текущем экземпляре службы API Management. Их значения не извлекаются с сервера OAuth 2.0.
> 
> 

Заполните поле **Client registration page URL** (URL-адрес страницы регистрации клиента). Это страница, на которой пользователи могут создавать свои учетные записи и управлять ими. Ее URL-адрес зависит от используемого поставщика OAuth 2.0. **URL-адрес страницы регистрации клиента** указывает на страницу, на которой пользователи могут создавать и настраивать собственные учетные записи для поставщиков OAuth 2.0, поддерживающих пользовательское управление учетными записями. В некоторых организациях эта функциональность не настраивается и не используется, даже если поставщик OAuth 2.0 ее поддерживает. Если у вашего поставщика OAuth 2.0 не настроено пользовательское управление учетными записями, введите здесь URL-адрес-заполнитель, такой как URL-адрес вашей компании или URL-адрес `https://placeholder.contoso.com`.

В следующем разделе формы содержатся параметры **Authorization code grant types** (Типы предоставления кода авторизации), **Authorization endpoint URL** (URL-адрес конечной точки авторизации) и **Authorization request method** (Метод запроса авторизации).

![Новый сервер][api-management-oauth2-server-2]

Установите нужные флажки в разделе **Authorization code grant types** . **Authorization code** (Код авторизации).

Введите URL-адрес в поле **Authorization endpoint URL**. Для Azure Active Directory этот URL-адрес будет аналогичен следующему URL-адресу, где `<client_id>` заменяется идентификатором клиента, по которому сервер OAuth 2.0 опознает ваше приложение.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

Параметр **Authorization request method** указывает на то, каким образом запрос авторизации отправляется на сервер OAuth 2.0. По умолчанию выбран вариант **GET** .

В следующем разделе можно настроить параметры **Token endpoint URL** (URL-адрес конечной точки маркера), **Client authentication methods** (Способы проверки подлинности клиента), **Access token sending method** (Способ отправки маркера доступа) и **Default scope** (Область по умолчанию).

![Новый сервер][api-management-oauth2-server-3]

Для сервера OAuth 2.0 Azure Active Directory параметр**Token endpoint URL** будет иметь следующий формат, где `<APPID>` имеет вид `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

Параметр **Client authentication methods** по умолчанию имеет значение **Basic** (Обычная), а параметр **Access token sending method** — значение **Authorization header** (Заголовок авторизации). Эти значения можно настроить в данном разделе формы вместе с параметром **Default scope**.

В разделе **Client credentials** (Учетные данные клиента) содержатся поля **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента), значения которых получены в ходе создания и настройки сервера OAuth 2.0. После определения значений **Client ID** и **Client secret** генерируется параметр **redirect_uri** (URI перенаправления) для **кода авторизации**. Этот универсальный код ресурса (URI) используется для настройки URL-адреса ответа в конфигурации сервера OAuth 2.0.

![Новый сервер][api-management-oauth2-server-4]

Если параметр **Authorization code grant types** имеет значение **Resource owner password** (Пароль владельца ресурса), то в разделе **Resource owner password credentials** (Учетные данные владельца ресурса) нужно указать соответствующие учетные данные. В противном случае эти поля можно оставить пустыми.

![Новый сервер][api-management-oauth2-server-5]

После заполнения формы нажмите кнопку **Save** (Сохранить), чтобы сохранить конфигурацию сервера авторизации OAuth 2.0 для службы API Management. Сохранив конфигурацию сервера, вы можете настроить интерфейсы API для ее использования, как описано в следующем разделе.

## <a name="step2"> </a>Настройка API для авторизации пользователей по протоколу OAuth 2.0
В меню **API Management** (Управление API) слева выберите пункт **APIs** (Интерфейсы API), щелкните имя нужного API, перейдите на вкладку **Security** (Безопасность) и установите флажок **OAuth 2.0**.

![Авторизация пользователя][api-management-user-authorization]

В раскрывающемся списке **Authorization server** (Сервер авторизации) выберите нужный пункт и нажмите кнопку **Save** (Сохранить).

![Авторизация пользователя][api-management-user-authorization-save]

## <a name="step3"> </a>Тестирование авторизации пользователей по протоколу OAuth 2.0 на портале разработчика
Настроив сервер авторизации OAuth 2.0 и его использование интерфейсом API, вы можете протестировать сервер, перейдя на портал разработчика и вызвав интерфейс API.  В правом верхнем меню выберите пункт **Developer portal** (Портал разработчика).

![Developer portal][api-management-developer-portal-menu]

Щелкните **APIs** в меню вверху и выберите **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Если есть только один настроенный или видимый API для данной учетной записи, тогда щелчок по API сразу приведет к операциям для этого API.
> 
> 

Выберите операцию **GET Resource**, щелкните **Open Console** (Открыть консоль) и выберите в раскрывающемся списке пункт **Authorization code** (Код авторизации).

![Открытие консоли][api-management-open-console]

После выбора пункта **Authorization code** появляется всплывающее окно с формой входа поставщика OAuth 2.0. В этом примере форма входа предоставляется службой Azure Active Directory.

> [!NOTE]
> Если всплывающие окна отключены, то в браузере появится запрос на их включение. Включив всплывающие окна, еще раз выберите пункт **Authorization code** , чтобы открыть форму входа.
> 
> 

![Вход][api-management-oauth2-signin]

После входа поле **Request headers** (Заголовки запроса) заполняется заголовком `Authorization : Bearer`, используемым для авторизации запроса.

![Маркер заголовка запроса][api-management-request-header-token]

Теперь вы можете настроить остальные параметры и отправить запрос. 

## <a name="next-steps"></a>Дополнительная информация
Для получения дополнительных сведений об использовании OAuth 2.0 и службы управления API см. следующий видеоролик и эту [статью](api-management-howto-protect-backend-with-aad.md).


[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

