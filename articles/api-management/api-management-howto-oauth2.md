<properties 
	pageTitle="Авторизация учетных записей разработчиков с помощью протокола OAuth 2.0 в Azure API Management" 
	description="Сведения об авторизации пользователей с помощью OAuth 2.0 в службе управления API." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Авторизация учетных записей разработчиков с помощью протокола OAuth 2.0 в Azure API Management

Служба API Management дает пользователям возможность получать доступ к порталу разработчика с помощью авторизации по протоколу OAuth 2.0. В этом руководстве описано, как настроить экземпляр службы API Management для авторизации по протоколу OAuth 2.0.

>Дополнительные сведения о протоколе OAuth 2.0 см. в спецификации на сайте [http://oauth.net/2/][http://oauth.net/2/].

## Содержание раздела

- [Предварительные требования][Предварительные требования]
- [Настройка сервера авторизации OAuth 2.0 в службе API Management][Настройка сервера авторизации OAuth 2.0 в службе API Management]
- [Настройка API для авторизации пользователей по протоколу OAuth 2.0][Настройка API для авторизации пользователей по протоколу OAuth 2.0]
- [Тестирование авторизации пользователей по протоколу OAuth 2.0 на портале разработчика][Тестирование авторизации пользователей по протоколу OAuth 2.0 на портале разработчика]

## <a name="prerequisites"></a> Предварительные требования

В этом руководстве описано, как настроить экземпляр службы API Management для авторизации учетных записей разработчиков по протоколу OAuth 2.0. В нем не рассматривается настройка поставщика OAuth 2.0. Настройка каждого поставщика OAuth 2.0 имеет свои особенности, хотя инструкции в целом схожи, а данные, необходимые для настройки этого протокола в экземпляре службы API Management, одинаковы. В примерах в этом разделе в качестве поставщика OAuth 2.0 используется служба Azure Active Directory.

> Дополнительные сведения о настройке OAuth 2.0 с использованием Azure Active Directory см. в примере [WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet].

## <a name="step1"></a> Настройка сервера авторизации OAuth 2.0 в службе API Management

Для начала щелкните **Консоль управления** на портале Azure службы API Management. Открывается административный портал API Management.

![Консоль API Management][api-management-management-console]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][Создание экземпляра службы API Management] в руководстве [Начинаем работу с API Management][Начинаем работу с API Management].

В меню **API Management** (Управление API) слева выберите пункт **Security** (Безопасность), перейдите на вкладку **OAuth 2.0** и щелкните ссылку **Add authorization server** (Добавить сервер авторизации).

![OAuth 2.0][api-management-oauth2]

После перехода по ссылке **Add authorization server** открывается форма нового сервера авторизации.

![Новый сервер][api-management-oauth2-server-1]

В полях **Name** (Имя) и **Description** (Описание) введите имя и (при желании) описание.

>Эти поля служат для идентификации сервера авторизации OAuth 2.0 в текущем экземпляре службы API Management. Их значения не извлекаются с сервера OAuth 2.0.

Заполните поле **Client registration page URL** (URL-адрес страницы регистрации клиента). Это страница, на которой пользователи могут создавать свои учетные записи и управлять ими. Ее URL-адрес зависит от используемого поставщика OAuth 2.0.

В следующем разделе формы содержатся параметры **Authorization code grant types** (Типы предоставления кода авторизации), **Authorization endpoint URL** (URL-адрес конечной точки авторизации) и **Authorization request method** (Метод запроса авторизации).

![Новый сервер][api-management-oauth2-server-2]

Установите нужные флажки в разделе **Authorization code grant types**. По умолчанию выбран вариант **Authorization code** (Код авторизации).

Введите URL-адрес в поле **Authorization endpoint URL**. Для Azure Active Directory этот URL-адрес выглядит, как показано ниже. Вместо `<client_id>` нужно подставить идентификатор клиента, по которому сервер OAuth 2.0 опознает ваше приложение.

    https://login.windows.net/<client_id>/oauth2/authorize

Параметр **Authorization request method** указывает на то, каким образом запрос авторизации отправляется на сервер OAuth 2.0. По умолчанию выбран вариант **GET**.

В следующем разделе можно настроить параметры **Token endpoint URL** (URL-адрес конечной точки маркера), **Client authentication methods** (Способы проверки подлинности клиента), **Access token sending method** (Способ отправки маркера доступа) и **Default scope** (Область по умолчанию).

![Новый сервер][api-management-oauth2-server-3]

Для сервера OAuth 2.0 Azure Active Directory параметр **Token endpoint URL** имеет вид, показанный ниже, где `<APPID>` имеет формат `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

Параметр **Client authentication methods** по умолчанию имеет значение **Basic** (Обычная), а параметр **Access token sending method** — значение **Authorization header** (Заголовок авторизации). Эти значения можно настроить в данном разделе формы вместе с параметром **Default scope**.

В разделе **Client credentials** (Учетные данные клиента) содержатся поля **Client ID** (Идентификатор клиента) и **Client secret** (Секрет клиента), значения которых получены в ходе создания и настройки сервера OAuth 2.0. После определения значений **Client ID** и **Client secret** генерируется параметр **redirect_uri** (URI перенаправления) для **кода авторизации**. Этот универсальный код ресурса (URI) используется для настройки URL-адреса ответа в конфигурации сервера OAuth 2.0.

![Новый сервер][api-management-oauth2-server-4]

Если параметр **Authorization code grant types** имеет значение **Resource owner password** (Пароль владельца ресурса), то в разделе **Resource owner password credentials** (Учетные данные владельца ресурса) нужно указать соответствующие учетные данные. В противном случае эти поля можно оставить пустыми.

![Новый сервер][api-management-oauth2-server-5]

После заполнения формы нажмите кнопку **Save** (Сохранить), чтобы сохранить конфигурацию сервера авторизации OAuth 2.0 для службы API Management. Сохранив конфигурацию сервера, вы можете настроить интерфейсы API для ее использования, как описано в следующем разделе.

## <a name="step2"></a> Настройка API для авторизации пользователей по протоколу OAuth 2.0

В меню **API Management** (Управление API) слева выберите пункт **APIs** (Интерфейсы API), щелкните имя нужного API, перейдите на вкладку **Security** (Безопасность) и установите флажок **OAuth 2.0**.

![Авторизация пользователя][api-management-user-authorization]

В раскрывающемся списке **Authorization server** (Сервер авторизации) выберите нужный пункт и нажмите кнопку **Save** (Сохранить).

![Авторизация пользователя][api-management-user-authorization-save]

## <a name="step3"></a> Тестирование авторизации пользователей по протоколу OAuth 2.0 на портале разработчика

Настроив сервер авторизации OAuth 2.0 и его использование интерфейсом API, вы можете протестировать сервер, перейдя на портал разработчика и вызвав интерфейс API. В правом верхнем меню выберите пункт **Developer portal** (Портал разработчика).

![Портал разработчика][api-management-developer-portal-menu]

Щелкните **API** в меню вверху и выберите **Echo API**.

![Echo API][api-management-apis-echo-api]

> Если есть только один настроенный или видимый API для данной учетной записи, тогда щелчок по API сразу приведет к операциям для этого API.

Выберите операцию **GET Resource**, щелкните **Open Console** (Открыть консоль) и выберите в раскрывающемся списке пункт **Authorization code** (Код авторизации).

![Открытие консоли][api-management-open-console]

После выбора пункта **Authorization code** появляется всплывающее окно с формой входа поставщика OAuth 2.0. В этом примере форма входа предоставляется службой Azure Active Directory.

>Если всплывающие окна отключены, то в браузере появится запрос на их включение. Включив всплывающие окна, еще раз выберите пункт **Authorization code**, чтобы открыть форму входа.

![Вход][api-management-oauth2-signin]

После входа в систему поле **Request headers** (Заголовки запроса) заполняется заголовком `Authorization : Bearer`, используемым для авторизации запроса.

![Маркер заголовка запроса][api-management-request-header-token]

Теперь вы можете настроить остальные параметры и отправить запрос.

[http://oauth.net/2/]: http://oauth.net/2/
[Предварительные требования]: #prerequisites
[Настройка сервера авторизации OAuth 2.0 в службе API Management]: #step1
[Настройка API для авторизации пользователей по протоколу OAuth 2.0]: #step2
[Тестирование авторизации пользователей по протоколу OAuth 2.0 на портале разработчика]: #step3
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Создание экземпляра службы API Management]: ../api-management-get-started/#create-service-instance
[Начинаем работу с API Management]: ../api-management-get-started
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

<!--HONumber=46--> 
 