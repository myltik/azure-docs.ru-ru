---
title: Настройка проверки подлинности и авторизации в службе приложений Azure | Документация Майкрософт
description: В этой статье показано, как настроить проверку подлинности и авторизацию в службе приложений и получить утверждения пользователей, а также различные токены.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: c41cb3ef2939fe7271b1f8738fcf0cb95c4b1111
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763148"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Настройка проверки подлинности и авторизации в службе приложений Azure

В этой статье показано, как настроить [проверку подлинности и авторизацию в службе приложений](app-service-authentication-overview.md), а также управлять удостоверениями, используя приложение. 

Чтобы быстро приступить к работе, ознакомьтесь с одним из следующих руководств:

* [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Windows)](app-service-web-tutorial-auth-aad.md)
* [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Linux)](containers/tutorial-auth-aad.md)
* [Настройка приложения для использования имени входа Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Настройка приложения для использования имени входа Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Настройка приложения для использования имени входа Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Настройка приложения для использования входа по учетной записи Майкрософт](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Настройка приложения для использования имени входа Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Настройка нескольких вариантов входа

На портале нельзя напрямую настроить несколько вариантов входа для пользователей (например, через Facebook и Twitter). Однако эту функцию можно легко добавить к функциональным возможностям вашего веб-приложения. Для этого необходимо сделать следующее:

Во-первых, на странице **Authentication / Authorization** (Проверка подлинности и авторизация) на портале Azure настройте все поставщики удостоверений, которые нужно включить.

В раскрывающемся списке **Action to take when request is not authenticated** (Предпринимаемое действие, если проверка подлинности для запроса не выполнена) выберите **Разрешить анонимные запросы (нет действия)**.

На странице входа, на панели навигации или в любом другом расположении веб-приложения добавьте ссылку входа для каждого включенного поставщика (`/.auth/login/<provider>`). Например: 

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Когда пользователь щелкнет одну из этих ссылок, откроется соответствующая страница для входа.

## <a name="access-user-claims"></a>Доступ к утверждениям пользователей

Служба приложений передает утверждения пользователей в приложение с помощью специальных заголовков. Эти заголовки нельзя настроить с помощью внешних запросов. Они присутствуют, только если это установлено службой приложений. Некоторые примеры заголовков включают:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Сведения из этих заголовков можно получить с помощью кода, написанного на любом языке или в любой платформе. Для приложений ASP.NET 4.6 автоматически настраивается класс **ClaimsPrincipal** с соответствующими значениями.

В приложении можно также получить дополнительные сведения о пользователе, прошедшем проверку подлинности, путем вызова `/.auth/me`. Пакеты SDK для серверной части мобильных приложений предоставляют вспомогательные методы для работы с этими данными  Дополнительные сведения см. Дополнительные сведения см. в разделах [Практическое руководство. Использование утверждений аутентификации для таблиц](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity) и [Практическое руководство. Получение сведений о пользователе, прошедшем проверку подлинности](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Извлечение токенов в коде приложения

Токены, предоставляющиеся поставщиком, вводятся в заголовке запроса из кода сервера, что позволяет легко получить к ним доступ. В следующей таблице показаны возможные заголовки токена.

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Токен Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Учетная запись Майкрософт | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

В коде клиента (например, мобильного приложения или JavaScript в браузере) отправьте HTTP-запрос `GET` к `/.auth/me`. В возвращаемом JSON-файле будут содержаться предоставляемые поставщиком токены.

> [!NOTE]
> Токены доступа предназначены для получения доступа к ресурсам поставщика, поэтому они заданы, только если настройка поставщика осуществляется с помощью секрета клиента. Дополнительные сведения о том, как получить токены обновления, см. в разделе об [обновлении токенов доступа](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Обновление токенов доступа

Когда срок действия токена доступа поставщика истечет, необходимо выполнить повторную проверку подлинности пользователя. Истечения срока действия токена можно избежать, выполнив вызов `GET` к конечной точке приложения `/.auth/refresh`. В этом случае служба приложений автоматически обновляет токены доступа в хранилище токенов для пользователя, прошедшего проверку подлинности. В результате последующих запросов на токены с помощью кода приложения будут возвращаться обновленные токены. Однако для правильного обновления токена в хранилище токенов должны содержаться [токены обновления](https://auth0.com/learn/refresh-tokens/) для поставщика. Способ получения токенов обновления документируется каждым поставщиком, но ниже приведено краткое описание.

- **Google**. Добавьте параметр строки запроса `access_type=offline` к вызову API `/.auth/login/google`. Если используется пакет SDK для мобильных служб, можно добавить параметр к одной из перегрузок `LogicAsync` (см. в разделе о [токенах обновления Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**. Не предоставляет токены обновления. Срок действия токенов с долгим временем существования истекает через 60 дней (см. раздел об [истечении и продлении срока действия токенов доступа Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**. Срок действия токенов доступа не истекает (см. раздел о [часто задаваемых вопросах о Twitter OAuth](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq)).
- **Учетная запись Майкрософт**. [Настраивая параметры проверки подлинности учетной записи Майкрософт](app-service-mobile-how-to-configure-microsoft-authentication.md), выберите область `wl.offline_access`.
- **Azure Active Directory**. В [https://resources.azure.com](https://resources.azure.com) сделайте следующее:
    1. В верхней части страницы выберите **Read/Write** (Чтение и запись).
    1. В левой части браузера перейдите к **subscriptions** > **_\<имя\_подписки_** > **resourceGroups** > _**\<имя\_группы\_ресурсов>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<имя\_приложения>**_ > **config** > **authsettings**. 
    1. Нажмите кнопку **Изменить**.
    1. Измените следующее свойство. Замените _\<app\_id>_ идентификатором приложения Azure Active Directory службы, к которой требуется получить доступ.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Щелкните **Put**. 

После настройки поставщика можно проверить наличие токенов обновления в хранилище, вызвав `/.auth/me`. 

Чтобы обновить токен доступа в любое время, необходимо просто вызвать `/.auth/refresh` на любом языке. В следующем фрагменте кода jQuery используется для обновления токенов доступа из клиента JavaScript.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Если пользователь отменяет разрешения, предоставленные приложению, вызов `/.auth/me` может завершиться ошибкой `403 Forbidden`. Чтобы выполнить диагностику ошибок, проверьте дополнительные сведения в журналах приложений.

## <a name="extend-session-expiration-grace-period"></a>Продление льготного периода срока действия сеанса

По истечении срока действия авторизованного сеанса по умолчанию предоставляется 72-часовой льготный период. В течение этого льготного периода файл cookie или токен сеанса можно обновить с помощью службы приложений без повторной проверки подлинности пользователя. Если файл cookie или токен сеанса становятся недействительными, можно просто вызвать `/.auth/refresh`, и вам не нужно самостоятельно отслеживать истечение срока действия токена. После завершения 72-часового льготного периода пользователь должен выполнить вход в систему, чтобы получить действительные файл cookie или токен сеанса.

Если 72 часов не достаточно, можно продлить этот льготный период. Значительное продление этого периода может повлиять на уровень безопасности (например, токен проверки подлинности может быть утерян или украден). В связи с этим следует оставить значение по умолчанию (72 часа) или установить для периода продления наименьшее значение.

Чтобы продлить льготный период истечения срока действия по умолчанию, выполните следующую команду в [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Льготный период применяется только к сеансу, проверка подлинности для которого осуществлялась с помощью службы приложений, а не токенов, предоставляемых поставщиками удостоверений. Льготный период на предоставленные поставщиком токены, срок действия которых истек, не распространяется. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Ограничение домена учетных записей для входа

Учетные записи Майкрософт и Azure Active Directory позволяют выполнять вход из нескольких доменов. Например, учетная запись Майкрософт позволяет выполнять вход с помощью учетных записей _outlook.com_, _live.com_ и _hotmail.com_. Azure Active Directory разрешает использовать любое количество личных доменов для учетных записей для входа. Это может быть нежелательно для внутренних приложений, к которым вы не хотите предоставлять доступ всем, у кого имеется учетная запись _outlook.com_. Чтобы ограничить доменное имя учетных записей для входа, сделайте следующее.

В разделе [https://resources.azure.com](https://resources.azure.com) перейдите к **subscriptions** > **_\<имя\_подписки_** > **resourceGroups** > _**\<имя\_группы\_ресурсов>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<имя\_приложения>**_ > **config** > **authsettings**. 

Щелкните **Edit** (Изменить), измените следующее свойство и выберите **Put**. Обязательно замените _\<domain\_имя>_ необходимым доменом.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Windows)](app-service-web-tutorial-auth-aad.md)
> [Руководство по сквозной проверке подлинности и авторизации в службе приложений Azure (Linux)](containers/tutorial-auth-aad.md)