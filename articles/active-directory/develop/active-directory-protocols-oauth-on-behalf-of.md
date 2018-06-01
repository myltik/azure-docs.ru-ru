---
title: Проверка подлинности между службами в Azure AD с использованием черновой спецификации потока On-Behalf-Of в OAuth 2.0 | Документы Майкрософт
description: В этой статье описывается, как использовать HTTP-сообщения для проверки подлинности между службами с помощью потока On-Behalf-Of в OAuth 2.0.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2f7566bc696d07ad3a8003b3493a382f494c4599
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157220"
---
# <a name="service-to-service-calls-using-delegated-user-identity-in-the-on-behalf-of-flow"></a>Вызовы между службами с использованием делегированного удостоверения пользователя в потоке On-Behalf-Of
Поток On-Behalf-Of в OAuth 2.0 используется в том случае, когда приложение вызывает API службы или веб-API, который, в свою очередь, должен вызывать другой API службы или веб-API. Идея состоит в том, чтобы распространить делегированное удостоверение пользователя и разрешения с помощью цепочки запросов. Для того чтобы служба среднего уровня могла выполнять запросы к службе нижнего уровня с проверкой подлинности, служба среднего уровня должна защитить токен доступа из Azure Active Directory (Azure AD) от имени пользователя.

## <a name="on-behalf-of-flow-diagram"></a>Схема потока On-Behalf-Of
Предположим, что пользователь прошел проверку подлинности для приложения с использованием [потока для предоставления кода проверки подлинности в OAuth 2.0](active-directory-protocols-oauth-code.md). На этом этапе приложение содержит токен доступа (токен A) с утверждениями пользователя и разрешение на доступ к веб-API среднего уровня (API A). Теперь API A должен выполнить запрос к веб-API нижнего уровня (API B) с проверкой подлинности.

Последующие шаги образуют поток On-Behalf-Of. Эти шаги поясняются на следующей схеме.

![Поток On-Behalf-Of в OAuth 2.0](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Клиентское приложение отправляет запрос к API A с токеном A.
2. API A выполняет проверку подлинности на конечной точке выдачи токена Azure AD и запрашивает токен доступа к API B.
3. Конечная точка выдачи токена Azure AD проверяет учетные данные API A с помощью токена A и выдает токен доступа для API B (токен B).
4. Токен B устанавливается в заголовке проверке подлинности запроса к API B.
5. API B возвращает данные из защищенного ресурса.

## <a name="register-the-application-and-service-in-azure-ad"></a>Регистрация приложения и службы в Azure AD
Зарегистрируйте клиентское приложение и службу среднего уровня в Azure AD.
### <a name="register-the-middle-tier-service"></a>Регистрация службы среднего уровня
1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните учетную запись и в списке **Каталог** выберите клиент Active Directory, в котором хотите зарегистрировать приложение.
3. В левой области навигации щелкните **Другие службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и выберите **Регистрация нового приложения**.
5. Введите понятное имя для приложения и укажите тип приложения. На основе типа приложения установите базовый URL-адрес в качестве URL-адреса входа или URL-адреса перенаправления. Щелкните **Создать**, чтобы создать приложение.
6. На портале Azure выберите свое приложение и щелкните **Параметры**. В меню "Параметры" выберите **Ключи** и добавьте ключ, указав срок действия ключа (1 или 2 года). При сохранении этой страницы будет показано значение ключа. Скопируйте и сохраните это значение в безопасном месте. Это значение потребуется позже для настройки параметров приложения в вашей реализации. Это значение больше не будет отображаться, и его нельзя получить никаким другим способом, поэтому обязательно запишите значение ключа, как только оно будет показано на странице портала Azure.

### <a name="register-the-client-application"></a>Регистрация клиентского приложения
1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели щелкните учетную запись и в списке **Каталог** выберите клиент Active Directory, в котором хотите зарегистрировать приложение.
3. В левой области навигации щелкните **Другие службы** и выберите **Azure Active Directory**.
4. Щелкните **Регистрация приложений** и выберите **Регистрация нового приложения**.
5. Введите понятное имя для приложения и укажите тип приложения. На основе типа приложения установите базовый URL-адрес в качестве URL-адреса входа или URL-адреса перенаправления. Щелкните **Создать**, чтобы создать приложение.
6. Настройте разрешения для приложения. В меню "Параметры" выберите раздел **Необходимые разрешения**, щелкните **Добавить**, затем — **Выбор API** и укажите имя службы среднего уровня в текстовом поле. Затем щелкните **Выбор разрешений** и выберите разрешение "Доступ к *имя_службы*".

### <a name="configure-known-client-applications"></a>Настройка известных клиентских приложений
В этом сценарии служба среднего уровня получает согласие пользователя на доступ к API нижнего уровня без взаимодействия с пользователем. Поэтому согласие на доступ к API нижнего уровня необходимо получить заранее при принятии условий во время проверки подлинности.
Для этого выполните следующие действия, которые позволят явно связать регистрацию клиентского приложения в Azure AD с регистрацией службы среднего уровня. При этом согласие, необходимое для клиента и службы среднего уровня, будет предоставляться в одном диалоговом окне.
1. Перейдите к регистрации службы среднего уровня и щелкните **Манифест**, чтобы открыть редактор манифеста.
2. В манифесте найдите массив свойств `knownClientApplications` и добавьте идентификатор клиента для клиентского приложения в качестве одного из элементов массива.
3. Сохраните манифест, нажав кнопку "Сохранить".

## <a name="service-to-service-access-token-request"></a>Запрос токена доступа между службами
Чтобы запросить токен доступа, отправьте запрос HTTP POST к конечной точке Azure AD конкретного клиента с указанными параметрами.

```
https://login.microsoftonline.com/<tenant>/oauth2/token
```
Существует два сценария. Их выбор зависит от типа защиты клиентского приложения — с помощью общего секрета или с помощью сертификата.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Первый сценарий: запрос маркера доступа с помощью общего секрета
При использовании общего секрета запрос маркера взаимного доступа между службами содержит следующие параметры:

| Параметр |  | ОПИСАНИЕ |
| --- | --- | --- |
| grant_type |обязательно | Тип запроса токена. Для запроса с использованием JWT это значение должно быть равно **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |обязательно | Значение токена, используемого в запросе. |
| client_id |обязательно | Идентификатор приложения, назначенный для вызывающей службы при регистрации в Azure AD. Чтобы узнать идентификатор приложения, щелкните **Active Directory** на портале управления Azure, затем выберите каталог и приложение. |
| client_secret |обязательно | Ключ, зарегистрированный для вызывающей службы в Azure AD. Значение ключа должно было быть записано при регистрации. |
| resource |обязательно | URI идентификатора приложения принимающей службы (защищенный ресурс). Чтобы узнать URI идентификатора приложения, щелкните **Active Directory** на портале управления Azure, выберите каталог и приложение, щелкните **Все параметры** и **Свойства**. |
| requested_token_use |обязательно | Указывает, как должен быть обработан запрос. Для потока On-Behalf-Of это значение должно быть равно **on_behalf_of**. |
| scope |обязательно | Список областей для запроса токена, разделенный пробелами. Для OpenID Connect необходимо указать область **openid**.|

#### <a name="example"></a>Пример
Ниже приведен HTTP-запрос POST маркера доступа для веб-API https://graph.windows.net. Параметр `client_id` определяет службу, которая запрашивает токен доступа.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_secret=0Y1W%2BY3yYb3d9N8vSjvm8WrGzVZaAaHbHHcGbcgG%2BoI%3D
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Второй сценарий: запрос маркера доступа с помощью сертификата
Запрос маркера взаимного доступа между службами с помощью сертификата содержит следующие параметры:

| Параметр |  | ОПИСАНИЕ |
| --- | --- | --- |
| grant_type |обязательно | Тип запроса токена. Для запроса с использованием JWT это значение должно быть равно **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| assertion |обязательно | Значение токена, используемого в запросе. |
| client_id |обязательно | Идентификатор приложения, назначенный для вызывающей службы при регистрации в Azure AD. Чтобы узнать идентификатор приложения, щелкните **Active Directory** на портале управления Azure, затем выберите каталог и приложение. |
| client_assertion_type |обязательно |Значение должно быть `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |обязательно | Утверждение (JSON Web Token), которое необходимо создать и подписать с помощью сертификата, зарегистрированного как учетные данные для приложения. Ознакомьтесь с [учетными данными сертификата](active-directory-certificate-credentials.md), чтобы узнать, как зарегистрировать сертификат и отформатировать утверждение.|
| resource |обязательно | URI идентификатора приложения принимающей службы (защищенный ресурс). Чтобы узнать URI идентификатора приложения, щелкните **Active Directory** на портале управления Azure, выберите каталог и приложение, щелкните **Все параметры** и **Свойства**. |
| requested_token_use |обязательно | Указывает, как должен быть обработан запрос. Для потока On-Behalf-Of это значение должно быть равно **on_behalf_of**. |
| scope |обязательно | Список областей для запроса токена, разделенный пробелами. Для OpenID Connect необходимо указать область **openid**.|

Обратите внимание на то, что параметры являются почти такими же, как и при использовании запроса с помощью общего секрета, за исключением параметра client_secret, который заменяется двумя параметрами: client_assertion_type и client_assertion.

#### <a name="example"></a>Пример
Ниже приведен HTTP-запрос POST маркера доступа для веб-API https://graph.windows.net с сертификатом. Параметр `client_id` определяет службу, которая запрашивает токен доступа.

```
// line breaks for legibility only

POST /oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&client_id=625391af-c675-43e5-8e44-edd3e30ceb15
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&resource=https%3A%2F%2Fgraph.windows.net
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2Rkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tLzE5MjNmODYyLWU2ZGMtNDFhMy04MWRhLTgwMmJhZTAwYWY2ZCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzI2MDM5Y2NlLTQ4OWQtNDAwMi04MjkzLTViMGM1MTM0ZWFjYi8iLCJpYXQiOjE0OTM0MjMxNTIsIm5iZiI6MTQ5MzQyMzE1MiwiZXhwIjoxNDkzNDY2NjUyLCJhY3IiOiIxIiwiYWlvIjoiWTJaZ1lCRFF2aTlVZEc0LzM0L3dpQndqbjhYeVp4YmR1TFhmVE1QeG8yYlN2elgreHBVQSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiJiMzE1MDA3OS03YmViLTQxN2YtYTA2YS0zZmRjNzhjMzI1NDUiLCJhcHBpZGFjciI6IjAiLCJlX2V4cCI6MzAyNDAwLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidmVyIjoiMS4wIn0.R-Ke-XO7lK0r5uLwxB8g5CrcPAwRln5SccJCfEjU6IUqpqcjWcDzeDdNOySiVPDU_ZU5knJmzRCF8fcjFtPsaA4R7vdIEbDuOur15FXSvE8FvVSjP_49OH6hBYqoSUAslN3FMfbO6Z8YfCIY4tSOB2I6ahQ_x4ZWFWglC3w5mK-_4iX81bqi95eV4RUKefUuHhQDXtWhrSgIEC0YiluMvA4TnaJdLq_tWXIc4_Tq_KfpkvI004ONKgU7EAMEr1wZ4aDcJV2yf22gQ1sCSig6EGSTmmzDuEPsYiyd4NhidRZJP4HiiQh-hePBQsgcSgYGvz9wC6n57ufYKh2wm_Ti3Q
&requested_token_use=on_behalf_of
&scope=openid
```

## <a name="service-to-service-access-token-response"></a>Ответ для токена доступа между службами
Если доступ предоставлен, ответ будет содержать JSON-файл OAuth 2.0 со следующими параметрами.

| Параметр | ОПИСАНИЕ |
| --- | --- |
| token_type |Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — **носитель**. Дополнительные сведения о токенах носителей см. в разделе [OAuth 2.0 Authorization Framework: использование токена носителя (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Область доступа, предоставляемая токеном. |
| expires_in |Срок действия доступа для токена (в секундах). |
| expires_on |Время истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. Это значение используется для определения времени существования кэшированных маркеров. |
| resource |URI идентификатора приложения принимающей службы (защищенный ресурс). |
| access_token |Запрашиваемый маркер доступа. Вызывающая служба может использовать этот токен для проверки подлинности принимающей службы. |
| id_token |Запрашиваемый идентификатор токена. Вызывающая служба может использовать этот токен для проверки удостоверения пользователя и запуска сеанса пользователя. |
| refresh_token |Токен обновления для запрошенного токена доступа. Вызывающая служба может использовать этот токен для запроса другого токена доступа после того, как срок действия текущего токена доступа истек. |

### <a name="success-response-example"></a>Пример ответа с успешным предоставлением доступа
В следующем примере показано сообщение о предоставлении доступа в ответ на запрос маркера доступа к веб-API https://graph.windows.net.

```
{
    "token_type":"Bearer",
    "scope":"User.Read",
    "expires_in":"43482",
    "ext_expires_in":"302683",
    "expires_on":"1493466951",
    "not_before":"1493423168",
    "resource":"https://graph.windows.net",
    "access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ",
    "refresh_token":"AQABAAAAAABnfiG-mA6NTae7CdWW7QfdjKGu9-t1scy_TDEmLi4eLQMjJGt_nAoVu6A4oSu1KsRiz8XyQIPKQxSGfbf2FoSK-hm2K8TYzbJuswYusQpJaHUQnSqEvdaCeFuqXHBv84wjFhuanzF9dQZB_Ng5za9xKlUENrNtlq9XuLNVKzxEyeUM7JyxzdY7JiEphWImwgOYf6II316d0Z6-H3oYsFezf4Xsjz-MOBYEov0P64UaB5nJMvDyApV-NWpgklLASfNoSPGb67Bc02aFRZrm4kLk-xTl6eKE6hSo0XU2z2t70stFJDxvNQobnvNHrAmBaHWPAcC3FGwFnBOojpZB2tzG1gLEbmdROVDp8kHEYAwnRK947Py12fJNKExUdN0njmXrKxNZ_fEM33LHW1Tf4kMX_GvNmbWHtBnIyG0w5emb-b54ef5AwV5_tGUeivTCCysgucEc-S7G8Cz0xNJ_BOiM_4bAv9iFmrm9STkltpz0-Tftg8WKmaJiC0xXj6uTf4ZkX79mJJIuuM7XP4ARIcLpkktyg2Iym9jcZqymRkGH2Rm9sxBwC4eeZXM7M5a7TJ-5CqOdfuE3sBPq40RdEWMFLcrAzFvP0VDR8NKHIrPR1AcUruat9DETmTNJukdlJN3O41nWdZOVoJM-uKN3uz2wQ2Ld1z0Mb9_6YfMox9KTJNzRzcL52r4V_y3kB6ekaOZ9wQ3HxGBQ4zFt-2U0mSszIAA",
    "id_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC8yNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IvIiwiaWF0IjoxNDkzNDIzMTY4LCJuYmYiOjE0OTM0MjMxNjgsImV4cCI6MTQ5MzQ2Njk1MSwiYW1yIjpbInB3ZCJdLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJzdWIiOiJEVXpYbkdKMDJIUk0zRW5pbDFxdjZCakxTNUllQy0tQ2ZpbzRxS1MzNEc4IiwidGlkIjoiMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiIiwidW5pcXVlX25hbWUiOiJuYXZ5YUBkZG9iYWxpYW5vdXRsb29rLm9ubWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXRpIjoieEN3ZnpoYS1QMFdKUU9MeENHZ0tBQSIsInZlciI6IjEuMCJ9."
}
```

### <a name="error-response-example"></a>Пример ответа с сообщением об ошибке
Сообщение об ошибке возвращается конечной точкой токена Azure AD при попытке получить токен доступа для API нижнего уровня в том случае, если для API нижнего уровня настроена политика условного доступа, например многофакторная проверка подлинности. Служба среднего уровня должна передать эту ошибку в клиентское приложение, чтобы пользователь мог выполнить необходимые действия для соблюдения политики условного доступа.

```
{
    "error":"interaction_required",
    "error_description":"AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access 'bf8d80f9-9098-4972-b203-500f535113b1'.\r\nTrace ID: b72a68c3-0926-4b8e-bc35-3150069c2800\r\nCorrelation ID: 73d656cf-54b1-4eb2-b429-26d8165a52d7\r\nTimestamp: 2017-05-01 22:43:20Z",
    "error_codes":[50079],
    "timestamp":"2017-05-01 22:43:20Z",
    "trace_id":"b72a68c3-0926-4b8e-bc35-3150069c2800",
    "correlation_id":"73d656cf-54b1-4eb2-b429-26d8165a52d7",
    "claims":"{\"access_token\":{\"polids\":{\"essential\":true,\"values\":[\"9ab03e19-ed42-4168-b6b7-7001fb3e933a\"]}}}"
}
```

## <a name="use-the-access-token-to-access-the-secured-resource"></a>Использование токена доступа для доступа к защищенному ресурсу
Теперь служба среднего уровня может использовать полученный выше токен для запросов к веб-API нижнего уровня с проверкой подлинности путем установки токена в заголовке `Authorization`.

### <a name="example"></a>Пример
```
GET /me?api-version=2013-11-08 HTTP/1.1
Host: graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLndpbmRvd3MubmV0IiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvMjYwMzljY2UtNDg5ZC00MDAyLTgyOTMtNWIwYzUxMzRlYWNiLyIsImlhdCI6MTQ5MzQyMzE2OCwibmJmIjoxNDkzNDIzMTY4LCJleHAiOjE0OTM0NjY5NTEsImFjciI6IjEiLCJhaW8iOiJBU1FBMi84REFBQUE1NnZGVmp0WlNjNWdBVWwrY1Z0VFpyM0VvV2NvZEoveWV1S2ZqcTZRdC9NPSIsImFtciI6WyJwd2QiXSwiYXBwaWQiOiI2MjUzOTFhZi1jNjc1LTQzZTUtOGU0NC1lZGQzZTMwY2ViMTUiLCJhcHBpZGFjciI6IjEiLCJlX2V4cCI6MzAyNjgzLCJmYW1pbHlfbmFtZSI6IlRlc3QiLCJnaXZlbl9uYW1lIjoiTmF2eWEiLCJpcGFkZHIiOiIxNjcuMjIwLjEuMTc3IiwibmFtZSI6Ik5hdnlhIFRlc3QiLCJvaWQiOiIxY2Q0YmNhYy1iODA4LTQyM2EtOWUyZi04MjdmYmIxYmI3MzkiLCJwbGF0ZiI6IjMiLCJwdWlkIjoiMTAwMzNGRkZBMTJFRDdGRSIsInNjcCI6IlVzZXIuUmVhZCIsInN1YiI6IjNKTUlaSWJlYTc1R2hfWHdDN2ZzX0JDc3kxa1l1ekZKLTUyVm1Zd0JuM3ciLCJ0aWQiOiIyNjAzOWNjZS00ODlkLTQwMDItODI5My01YjBjNTEzNGVhY2IiLCJ1bmlxdWVfbmFtZSI6Im5hdnlhQGRkb2JhbGlhbm91dGxvb2sub25taWNyb3NvZnQuY29tIiwidXBuIjoibmF2eWFAZGRvYmFsaWFub3V0bG9vay5vbm1pY3Jvc29mdC5jb20iLCJ1dGkiOiJ4Q3dmemhhLVAwV0pRT0x4Q0dnS0FBIiwidmVyIjoiMS4wIn0.cqmUVjfVbqWsxJLUI1Z4FRx1mNQAHP-L0F4EMN09r8FY9bIKeO-0q1eTdP11Nkj_k4BmtaZsTcK_mUygdMqEp9AfyVyA1HYvokcgGCW_Z6DMlVGqlIU4ssEkL9abgl1REHElPhpwBFFBBenOk9iHddD1GddTn6vJbKC3qAaNM5VarjSPu50bVvCrqKNvFixTb5bbdnSz-Qr6n6ACiEimiI1aNOPR2DeKUyWBPaQcU5EAK0ef5IsVJC1yaYDlAcUYIILMDLCD9ebjsy0t9pj_7lvjzUSrbMdSCCdzCqez_MSNxrk1Nu9AecugkBYp3UVUZOIyythVrj6-sVvLZKUutQ
```

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о протоколе OAuth 2.0 и другом способе проверки подлинности между службами с использованием учетных данных клиента.
* [Проверка подлинности между службами с использованием учетных данных клиента OAuth 2.0 в Azure AD](active-directory-protocols-oauth-service-to-service.md)
* [OAuth 2.0 в Azure AD](active-directory-protocols-oauth-code.md)
