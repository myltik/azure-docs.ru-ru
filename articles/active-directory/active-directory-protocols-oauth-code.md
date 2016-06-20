<properties
	pageTitle="Обзор протокола .NET Azure AD | Microsoft Azure"
	description="В этой статье описывается, как использовать HTTP-сообщения для авторизации в клиенте доступа к веб-приложениям и веб-API с использованием Azure Active Directory и OAuth 2.0."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>


# Авторизация доступа к веб-приложениям с помощью OAuth 2.0 и Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

В Azure Active Directory (Azure AD) используется протокол OAuth 2.0, чтобы обеспечивать авторизацию доступа к веб-приложениям и веб-API в клиенте Azure AD. Это руководство не зависит от языка и описывает, как отправлять и получать сообщения HTTP, не используя ни одну из наших библиотек с открытым кодом.

Описание потока кода авторизации OAuth 2.0 можно найти в [разделе 4.1 спецификации OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Он используется для проверки подлинности и авторизации большинства типов приложений, в том числе веб-приложений и изначально установленных приложений.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## Поток авторизации OAuth 2.0

В общих чертах весь поток авторизации для приложений можно представить следующим образом:

![Поток кода проверки подлинности OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## Запрос кода авторизации

Поток кода авторизации начинается с того, что клиент направляет пользователя к конечной точке `/authorize`. В этом запросе клиент указывает разрешения, которые ему требуется получить от пользователя. Конечные точки OAuth 2.0 можно получить на классическом портале Azure на странице приложения, нажав кнопку **Просмотр конечных точек** в нижней панели.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| Параметр | | Описание |
| ----------------------- | ------------------------------- | --------------- |
| tenant | обязательно | С помощью значения `{tenant}` в пути запроса можно управлять доступом к приложению. Допустимые значения — идентификаторы клиента, например `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` или `common` для маркеров без указания клиента. |
| client\_id | обязательно | Идентификатор приложения, назначенный вашему приложению при регистрации в Azure AD. Это значение можно найти на портале управления Azure. Щелкните **Active Directory**, выберите каталог, затем щелкните приложение и **Настройка**. |
| response\_type | обязательно | Должен содержать `code` для потока кода авторизации. |
| redirect\_uri | рекомендуется | URI перенаправления приложения, на который можно отправлять ответы проверки подлинности для их получения приложением. Он должен в точности соответствовать одному из URI перенаправления, зарегистрированных на портале, но иметь форму закодированного URL-адреса. Для собственных и мобильных приложений следует использовать значение `urn:ietf:wg:oauth:2.0:oob` по умолчанию. |
| scope | обязательно | Разделенный пробелами список [областей](active-directory-v2-scopes.md), для которого необходимо согласие пользователя. |
| response\_mode | рекомендуется | Указывает метод, с помощью которого результирующий маркер будет отправлен приложению. У него может быть значение `query` или `form_post`. |
| state | рекомендуется | Значение, включенное в запрос, которое также возвращается в ответе маркера. Это может быть строка любого контента. Как правило, с целью [предотвращения подделки межсайтовых запросов](http://tools.ietf.org/html/rfc6749#section-10.12) используется генерируемое случайным образом уникальное значение. Параметр "state" также используется для кодирования информации о состоянии пользователя в приложении перед созданием запроса на проверку подлинности, например информации об открытой на тот момент странице или представлении. |
| prompt | необязательный | Указывает требуемый тип взаимодействия с пользователем. На текущий момент единственные допустимые значения — "login", "none" и "consent". При значении `prompt=login` пользователю придется вводить учетные данные по запросу. Единый вход не сработает. Значение `prompt=none` является противоположным — оно гарантирует, что интерактивные запросы не будут выводиться ни при каких обстоятельствах. Если запрос не удастся завершить автоматически через единый вход, конечная точка версии 2.0 вернет ошибку. После входа пользователя `prompt=consent` откроет диалоговое окно согласия OAuth с запросом на предоставление разрешений приложению. |
| login\_hint | необязательный | Можно применять для предварительного заполнения поля имени пользователя или электронного адреса на странице входа пользователя (если имя пользователя известно заранее). Зачастую этот параметр используется в приложениях при повторной проверке подлинности. При этом имя пользователя извлекается во время предыдущего входа с помощью утверждения `preferred_username`. |
| domain\_hint | необязательный | У него может быть одно значение: `consumers` или `organizations`. Если используется этот параметр, процесс обнаружения на основе электронной почты, который проходит пользователь на странице входа в приложение версии 2.0, пропускается, что существенно оптимизирует работу. Обычно этот параметр применяется в приложениях при повторной аутентификации. Для этого `tid` извлекается во время предыдущего входа. Если значение утверждения `tid` — `9188040d-6c67-4c5b-b112-36a304b66dad`, следует использовать `domain_hint=consumers`. Или используйте `domain_hint=organizations`. |

> [AZURE.NOTE] Если пользователь принадлежит к организации, ее администратор может давать согласие или отказывать от имени пользователя или разрешать пользователю давать согласие. Пользователю предоставляется возможность давать согласие, только если это разрешил администратор.

На этом этапе пользователю будет предложено ввести учетные данные и принять разрешения, указанные в параметре запроса `scope`. Когда пользователь выполнит проверку подлинности и даст свое согласие, Azure AD отправит ответ в приложение по адресу `redirect_uri` в запросе.

### Успешный ответ

Успешный ответ выглядит следующим образом:

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| Параметр | Описание |
| -----------------------| --------------- |
| admin\_consent | Задано значение True, если администратор предоставил свое согласие в запросе о запросах на согласие.|
| Код | Запрашиваемый приложением код авторизации. Приложение может использовать код авторизации, чтобы запрашивать маркер доступа для целевого ресурса. |
| session\_state | Уникальное значение, указывающее текущий сеанс пользователя. Это значение представляет собой GUID, но его следует расценивать как непрозрачное значение, передаваемое без рассмотрения. |
| state | Если запрос содержит параметр "state", в ответе должно отображаться то же значение. Приложение должно проверить значения параметра state в запросе.
Если в запросе содержится параметр state, в ответе должно отображаться то же его значение. Приложение должно проверить, совпадают ли значения параметра state в запросе и ответе.

### Сообщение об ошибке

Сообщения-ответы об ошибках также можно отправлять по `redirect_uri`, чтобы приложение обрабатывало их должным образом.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## Использование кода авторизации для запроса маркера доступа

После получения кода авторизации и разрешения от пользователя вы можете применить код для получения маркера доступа к требуемому ресурсу путем отправки запроса POST на конечную точку `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| Параметр | | Описание |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | обязательно | С помощью значения `{tenant}` в пути запроса можно управлять доступом к приложению. Допустимые значения — идентификаторы клиента, например `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` или `common` для маркеров без указания клиента. |
| client\_id | обязательно | Идентификатор приложения, назначенный вашему приложению при регистрации в Azure AD. Это значение можно найти на классическом портале Azure. Щелкните **Active Directory**, выберите каталог, затем щелкните приложение и **Настройка**. |
| grant\_type | обязательно | Должен быть `authorization_code` для потока кода авторизации. |
| Код | обязательно | `authorization_code` полученный в предыдущем разделе. |
| redirect\_uri | обязательно | То же значение `redirect_uri`, что использовалось для получения `authorization_code`. |
| client\_secret | необходим для веб-приложений | Секрет приложения, созданный на портале регистрации для приложения. Его не следует использовать в собственном приложении, поскольку невозможно обеспечить полную безопасность секретов клиентов при их хранении на устройствах. Он требуется для веб-приложений и веб-API с возможностью безопасного хранения `client_secret` на сервере. |


### Успешный ответ

Успешный ответ выглядит следующим образом:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Параметр | Описание |
| ----------------------- | ------------------------------- |
| access\_token | Запрашиваемый маркер доступа. Приложение может использовать этот маркер для проверки подлинности на защищенном ресурсе, таком как веб-API. |
| token\_type | Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — носитель. Дополнительные сведения о токенах носителей см. в спецификации [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (OAuth2.0 Authorization Framework: использование токена носителя (RFC 6750)) |
| expires\_in | Срок действия маркера доступа (в секундах). |
| scope | Разрешения на олицетворение, предоставленные клиентскому приложению. Разрешение по умолчанию — `user_impersonation`. Владелец защищаемого ресурса может регистрировать дополнительные значения в Azure AD.|
| refresh\_token | Маркер обновления OAuth 2.0. Приложение может использовать этот маркер, чтобы получать дополнительные маркеры доступа после истечения срока действия текущего маркера. У маркеров обновления длительный срок действия. Их можно использовать, чтобы надолго сохранять доступ к ресурсам. |
| id\_token | Неподписанный веб-маркер JSON (JWT). Приложение может base64Url декодировать сегменты этого маркера, чтобы запрашивать сведения о пользователе, выполнившем вход. Приложение может кэшировать и отображать значения, но оно не должно полагаться на них при авторизации или в целях безопасности. |

### Утверждения JWT
JWT в значении параметра `id_token` можно декодировать на следующие утверждения:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

Параметр `id_token` включает в себя такие типы утверждений. Дополнительные сведения о веб-маркерах в формате JSON см. в [черновой спецификации JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Дополнительные сведения о типах маркеров и утверждениях см. в статье [Поддерживаемые токены и типы утверждений](active-directory-token-and-claims.md)

| Тип утверждения | Описание |
|------------|-------------|
| aud | Аудитория маркера. После выдачи маркера клиентскому приложению аудитория представляет собой `client_id` клиента.
| exp | Время окончания срока действия. Это время, когда истекает срок действия маркера. Чтобы маркер был действителен, текущие дата и время должны быть меньше значения `exp` или равны ему. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| family\_name | Фамилия пользователя. Это значение может отображаться в приложении. |
| given\_name | Имя пользователя. Это значение может отображаться в приложении. |
| iat | Время выдачи. Это время выдачи JWT. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| iss | Указывает поставщик маркера. |
| nbf | Не ранее этого времени. Время, когда маркер начинает действовать. Чтобы маркер был действителен, текущие дата и время должны быть больше значения nbf или равны ему. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| oid | Идентификатор объекта-пользователя в Azure AD. |
| sub | Идентификатор субъекта маркера. Это постоянный и неизменяемый идентификатор для пользователя, описываемого маркером. Используйте это значение в логике кэширования. |
| tid | Идентификатор клиента Azure AD, выдавшего маркер. |
| unique\_name | Уникальный идентификатор, отображаемый для пользователя. Как правило, это имя участника-пользователя (UPN). |
| upn | Имя участника-пользователя. |
| ver | Версия. Версия JWT, обычно 1.0. |

### Сообщение об ошибке

Пример сообщения-ответа об ошибке выглядит следующим образом:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Параметр | Описание |
| ----------------------- | ------------------------------- |
| error | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| error\_description | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |
| error\_codes | Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике. |
| Timestamp | Время возникновения ошибки. |
| trace\_id | Уникальный идентификатор для запроса, который может помочь при диагностике. |
| correlation\_id | Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов.|

## Использование маркера доступа для доступа к ресурсу

Успешно получив `access_token`, вы можете использовать маркер в запросах к веб-API путем включения его в заголовок `Authorization`: В спецификации [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) описывается, как использовать токены носителей в HTTP-запросах на доступ к защищенным ресурсам.

### Пример запроса

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## Обновление маркеров доступа

Срок действия маркеров доступа весьма ограничен, поэтому их нужно обновлять после его истечения, чтобы и далее получать доступ к ресурсам. Чтобы обновить `access_token`, можно отправить еще один запрос `POST` на конечную точку `/token`, прибегнув к `refresh_token` вместо `code`:

Время существования маркера обновления не указывается и зависит от параметров политики и времени, когда предоставление кода авторизации отменено Azure AD. Приложение должно предусматривать и обрабатывать случаи, когда запрос на новый маркер доступа не выполняется. Тогда оно должно возвращаться к коду, который запрашивает новый маркер доступа.

Пример запроса к конечной точке **конкретного клиента** (можно также использовать **общую** конечную точку) для получения нового маркера доступа с использованием маркера обновления выглядит следующим образом:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Параметр | Описание |
|-----------|-------------|
| access\_token | Новый запрошенный маркер доступа.|
| expires\_in | Оставшееся время существования маркера в секундах. Стандартное значение — 3600 (1 час). |
| expires\_on | Дата и время окончания срока действия маркера. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. |
| refresh\_token | Новый маркер обновления OAuth 2.0, который можно использовать, чтобы запрашивать новые маркеры доступа, когда истечет срок действия маркера в этом ответе. |
| resource | Определяет защищенный ресурс, для доступа к которому можно использовать маркер доступа. |
| scope | Разрешения на олицетворение, предоставленные собственному клиентскому приложению. По умолчанию используется разрешение **user\_impersonation**. Владелец целевого ресурса может регистрировать дополнительные значения в Azure AD. |
| token\_type | Тип маркера. Единственное поддерживаемое значение — **bearer**. |

### Успешный ответ

Успешный ответ маркера выглядит следующим образом:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### Сообщение об ошибке

Пример сообщения-ответа об ошибке выглядит следующим образом:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0608_2016-->