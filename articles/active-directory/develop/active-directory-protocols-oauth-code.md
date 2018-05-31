---
title: Общие сведения о потоке кода авторизации OAuth 2.0 в Azure AD
description: В этой статье описывается, как использовать HTTP-сообщения для авторизации в клиенте доступа к веб-приложениям и веб-API с использованием Azure Active Directory и OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 93de62a21ca1d3b8c88715fc9207a583920ac33e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158410"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0
В Azure Active Directory (Azure AD) используется протокол OAuth 2.0, чтобы обеспечивать авторизацию доступа к веб-приложениям и веб-API в клиенте Azure AD. Это руководство не зависит от языка и описывает, как отправлять и получать сообщения HTTP, не используя ни одну из наших [библиотек с открытым кодом](active-directory-authentication-libraries.md).

Описание потока кода авторизации OAuth 2.0 см. в [разделе 4.1 спецификации OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Он используется для аутентификации и авторизации в большинстве типов приложений, в том числе веб-приложений и изначально установленных приложений.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Поток авторизации OAuth 2.0
В общих чертах весь поток авторизации для приложений можно представить следующим образом:

![Поток кода проверки подлинности OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Запрос кода авторизации
Поток кода авторизации начинается с того, что клиент направляет пользователя к конечной точке `/authorize` . В этом запросе клиент указывает разрешения, которые ему требуется получить от пользователя. Чтобы получить конечную точку авторизации OAuth 2.0 для клиента, последовательно выберите **Регистрация приложений > Конечные точки** на портале Azure.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A%12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| Параметр |  | ОПИСАНИЕ |
| --- | --- | --- |
| tenant |обязательно |Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения — идентификаторы клиента, например `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` или `common` для маркеров без указания клиента. |
| client_id |обязательно |Идентификатор приложения, назначенный вашему приложению при регистрации в Azure AD. Его значение можно найти на портале Azure. Щелкните **Azure Active Directory** на боковой панели служб, щелкните **Регистрация приложений** и выберите приложение. |
| response_type |обязательно |Должен содержать `code` для потока кода авторизации. |
| redirect_uri |рекомендуется |URI перенаправления приложения, на который можно отправлять ответы проверки подлинности для их получения приложением. Он должен в точности соответствовать одному из URI перенаправления, зарегистрированных на портале, но иметь форму закодированного URL-адреса. Для собственных и мобильных приложений следует использовать значение `urn:ietf:wg:oauth:2.0:oob` по умолчанию. |
| response_mode |рекомендуется |Указывает метод, с помощью которого результирующий маркер будет отправлен приложению. Может иметь значение `query` или `form_post`. `query` предоставляет код в качестве параметра строки запроса для универсального кода ресурса (URI) перенаправления, а `form_post` выполняет запрос POST, содержащий код для универсального кода ресурса (URI) перенаправления. |
| state |рекомендуется |Значение, включенное в запрос, которое также возвращается в ответе маркера. Как правило, для [предотвращения подделки межсайтовых запросов](http://tools.ietf.org/html/rfc6749#section-10.12)используется генерируемое случайным образом уникальное значение. Параметр "state" также используется для кодирования информации о состоянии пользователя в приложении перед созданием запроса на проверку подлинности, например информации об открытой на тот момент странице или представлении. |
| resource | рекомендуется |Универсальный код ресурса (URI) идентификатора приложения целевого веб-API (защищенный ресурс). Чтобы найти универсальный код ресурса (URI) идентификатора приложения, на портале Azure последовательно выберите **Azure Active Directory** и **Регистрация приложений**, откройте страницу **Параметры** приложения, а затем щелкните **Свойства**. Это также может быть внешний ресурс, например `https://graph.microsoft.com`. Он необходим в запросе на авторизацию или запросе маркера. Чтобы сократить число запросов на аутентификацию, поместите его в запрос на авторизацию. Это обеспечит получение согласия от пользователя. |
| scope | **ignored** | Для приложений Azure AD версии 1 области должны быть настроены статически на портале Azure в разделе приложения **Параметры** > **Требуемые разрешения**. |
| prompt |необязательный |Указывает требуемый тип взаимодействия с пользователем.<p> Допустимые значения: <p> *login*: пользователю будет предложено повторно выполнить аутентификацию. <p> *consent*: согласие пользователя предоставлено, но нуждается в обновлении. Пользователю будет предложено предоставить согласие. <p> *admin_consent*: администратору будет предложено предоставить согласие от имени всех пользователей в своей организации. |
| login_hint |необязательный |Можно применять для предварительного заполнения поля имени пользователя или адреса электронной почты на странице входа пользователя (если имя пользователя известно заранее). Зачастую этот параметр используется в приложениях при повторной аутентификации. При этом имя пользователя извлекается во время предыдущего входа с помощью утверждения `preferred_username`. |
| domain_hint |необязательный |Предоставляет подсказку о том, какой клиент или домен пользователю следует использовать для входа. Значение параметра domain_hint представляет зарегистрированный домен клиента. Если для клиента выполняется федерация в локальный каталог, AAD перенаправляет запрос на указанный сервер федерации клиента. |
| code_challenge_method | необязательный    | Метод, используемый для кодирования `code_verifier` в параметре `code_challenge`. Может использоваться значение `plain` или `S256`. Если этот параметр не указан, для `code_challenge` принимается формат открытого текста, если задано значение `code_challenge`. Azure AD версии 1.0 поддерживает `plain` и `S256`. Дополнительные сведения см. в описании [PKCE RFC](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | необязательный    | Используется, чтобы защитить разрешения кода авторизации из собственного или общедоступного клиента при помощи ключа проверки для обмена кодом (PKCE). Является обязательным, если указан параметр `code_challenge_method`. Дополнительные сведения см. в описании [PKCE RFC](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Если пользователь принадлежит к организации, ее администратор может давать согласие или отказывать от имени пользователя или разрешать пользователю давать согласие. Пользователю предоставляется возможность давать согласие, только если это разрешил администратор.
>
>

На этом этапе пользователю предлагается ввести учетные данные и дать согласие на разрешения, запрошенные приложением на портале Azure. Когда пользователь выполнит аутентификацию и предоставит согласие, Azure AD отправит ответ с кодом в приложение по адресу `redirect_uri`, указанному в запросе.

### <a name="successful-response"></a>Успешный ответ
Успешный ответ выглядит следующим образом:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| Параметр | ОПИСАНИЕ |
| --- | --- |
| admin_consent |Задано значение True, если администратор предоставил свое согласие в запросе о запросах на согласие. |
| Код |Запрашиваемый приложением код авторизации. Приложение может использовать код авторизации, чтобы запрашивать маркер доступа для целевого ресурса. |
| session_state |Уникальное значение, указывающее текущий сеанс пользователя. Это значение представляет собой GUID, но его следует расценивать как непрозрачное значение, передаваемое без рассмотрения. |
| state |Если запрос содержит параметр "state", в ответе должно отображаться то же значение. Прежде чем использовать ответ, приложение должно проверить, совпадают ли значения параметра state в запросе и ответе. Это позволяет обнаруживать CSRF-атаки на клиент ( [подделка межсайтовых запросов](https://tools.ietf.org/html/rfc6749#section-10.12) ). |

### <a name="error-response"></a>Сообщение об ошибке
Сообщения об ошибках также можно отправлять на адрес `redirect_uri` , чтобы приложение обрабатывало их.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Параметр | ОПИСАНИЕ |
| --- | --- |
| error |Значение кода ошибки, определенное в разделе 5.2 документации по [OAuth 2.0 Authorization Framework](http://tools.ietf.org/html/rfc6749). В следующей таблице описаны коды ошибок, которые возвращает Azure AD. |
| error_description |Более подробное описание ошибки. Это сообщение не должно быть понятным для пользователей. |
| state |Значение состояния — это случайно сгенерированное значение без возможности повторного использования. Оно отправляется в запросе и возвращается в ответе для предотвращения атак с подделкой межсайтовых запросов. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Коды ошибок конечной точки авторизации
В таблице ниже описаны различные коды ошибок, которые могут возвращаться в параметре `error` ответа с ошибкой.

| Код ошибки | ОПИСАНИЕ | Действие клиента |
| --- | --- | --- |
| invalid_request |Ошибка протокола, например отсутствует обязательный параметр. |Исправьте запрос и отправьте его повторно. Это ошибка разработки, которая, как правило, обнаруживается во время первоначального тестирования. |
| unauthorized_client |Клиентскому приложению не разрешено запрашивать код авторизации. |Как правило, это происходит, если клиентское приложение не зарегистрировано в Azure AD или не добавлено в клиент Azure AD пользователя. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |
| access_denied |Владелец ресурса отказал в использовании |Клиентское приложение может уведомить пользователя, что не может продолжить работу без согласия пользователя. |
| unsupported_response_type |Сервер авторизации не поддерживает тип ответа в запросе. |Исправьте запрос и отправьте его повторно. Это ошибка разработки, которая, как правило, обнаруживается во время первоначального тестирования. |
| server_error |Сервер обнаружил непредвиденную ошибку. |Повторите запрос. Эти ошибки могут возникать в связи с временными условиями. Клиентское приложение может отобразить для пользователя сообщение о том, что его ответ задерживается из-за временной ошибки. |
| temporarily_unavailable |Сервер временно занят и не может обработать запрос. |Повторите запрос. Клиентское приложение может отобразить для пользователя сообщение о том, что его ответ задерживается из-за временных условий. |
| invalid_resource |Целевой ресурс недопустим, так как он не существует. Azure AD не удается найти ресурс, или он настроен неправильно. |Это означает, что ресурс, если он существует, не настроен в клиенте. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Использование кода авторизации для запроса маркера доступа
Получив код авторизации и разрешение от пользователя, вы можете с помощью этого кода получить маркер доступа к требуемому ресурсу, отправив запрос POST к конечной точке `/token` .

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| Параметр |  | ОПИСАНИЕ |
| --- | --- | --- |
| tenant |обязательно |Значение `{tenant}` в пути запроса можно использовать для того, чтобы контролировать, кто может входить в приложение. Допустимые значения — идентификаторы клиента, например `8eaef023-2b34-4da1-9baa-8bc8c9d6a490`, `contoso.onmicrosoft.com` или `common` для маркеров без указания клиента. |
| client_id |обязательно |Идентификатор приложения, назначенный вашему приложению при регистрации в Azure AD. Его значение можно найти на портале Azure. Идентификатор приложения отображается в окне параметров в разделе регистрации приложений. |
| grant_type |обязательно |Должен быть `authorization_code` для потока кода авторизации. |
| Код |обязательно |Код авторизации ( `authorization_code` ), полученный в предыдущем разделе. |
| redirect_uri |обязательно |То же значение `redirect_uri`, что использовалось для получения `authorization_code`. |
| client_secret |обязателен для веб-приложений, не допускается для общедоступных клиентов |Секрет приложения, созданный на портале Azure для приложения в разделе **Ключи**. Его нельзя использовать в собственном приложении (общедоступном клиенте), так как невозможно обеспечить полную безопасность секретов клиентов при их хранении на устройствах. Он обязателен для веб-приложений и веб-API (всех конфиденциальных клиентов) с возможностью безопасного хранения `client_secret` на сервере. |
| resource | рекомендуется |Универсальный код ресурса (URI) идентификатора приложения целевого веб-API (защищенный ресурс). Чтобы найти универсальный код ресурса (URI) идентификатора приложения, на портале Azure последовательно выберите **Azure Active Directory** и **Регистрация приложений**, откройте страницу **Параметры** приложения, а затем щелкните **Свойства**. Это также может быть внешний ресурс, например `https://graph.microsoft.com`. Он необходим в запросе на авторизацию или запросе маркера. Чтобы сократить число запросов на аутентификацию, поместите его в запрос на авторизацию. Это обеспечит получение согласия от пользователя. Если параметр resource указан в запросе на авторизацию и запросе маркера, то его значение должно быть одинаковым. | 
| code_verifier | необязательный | Это тот же параметр code_verifier, который использовался для получения кода авторизации (authorization_code). Является обязательным, если в запросе на код авторизации использовался PKCE. Дополнительные сведения см. в описании [PKCE RFC](https://tools.ietf.org/html/rfc7636).   |

Чтобы найти универсальный код ресурса (URI) идентификатора приложения, на портале Azure последовательно выберите **Azure Active Directory** и **Регистрация приложений**, откройте страницу **Параметры** приложения, а затем щелкните **Свойства**.

### <a name="successful-response"></a>Успешный ответ
Azure AD возвращает маркер доступа после успешного ответа. Чтобы снизить число сетевых вызовов из клиентского приложения и связанные с этим задержки, клиентскому приложению следует кэшировать маркеры доступа на время существования маркера, указанное в ответе OAuth 2.0. Чтобы определить время существования маркера, используйте значения параметра `expires_in` или `expires_on`.

Если ресурс API веб-узла возвращает код ошибки `invalid_token` , это может означать, что ресурс обнаружил истекший срок действия маркера. Если наблюдается расхождение во времени клиента и ресурса, ресурс может счесть маркер истекшим до того, как этот маркер будет удален из кэша клиента. В этом случае следует удалить маркер из кэша, даже если рассчитанное время его существования еще не истекло.

Успешный ответ выглядит следующим образом:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| Параметр | ОПИСАНИЕ |
| --- | --- |
| access_token |Запрашиваемый маркер доступа как подписанный маркер JSON Web Token (JWT). Приложение может использовать этот маркер для проверки подлинности на защищенном ресурсе, таком как веб-API. |
| token_type |Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — носитель. Дополнительные сведения о маркерах носителей см. в спецификации [OAuth 0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (OAuth2.0 Authorization Framework: использование маркера носителя (RFC 6750)). |
| expires_in |Срок действия маркера доступа (в секундах). |
| expires_on |Время истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. Это значение используется для определения времени существования кэшированных маркеров. |
| resource |URI идентификатора приложения веб-API (защищенный ресурс). |
| scope |Разрешения на олицетворение, предоставленные клиентскому приложению. Разрешение по умолчанию — `user_impersonation`. Владелец защищаемого ресурса может регистрировать дополнительные значения в Azure AD. |
| refresh_token |Маркер обновления OAuth 2.0. Приложение может использовать этот маркер, чтобы получать дополнительные маркеры доступа после истечения срока действия текущего маркера. У маркеров обновления длительный срок действия. Их можно использовать, чтобы надолго сохранять доступ к ресурсам. |
| id_token |Неподписанный веб-маркер JSON (JWT). Приложение может base64Url декодировать сегменты этого маркера, чтобы запрашивать сведения о пользователе, выполнившем вход. Приложение может кэшировать и отображать значения, но оно не должно полагаться на них при авторизации или в целях безопасности. |

### <a name="jwt-token-claims"></a>Утверждения JWT
Маркер JWT, указанный в значении параметра `id_token` , содержит следующие утверждения:

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

Дополнительные сведения о JSON Web Token см. в [черновой спецификации JWT IETF](http://go.microsoft.com/fwlink/?LinkId=392344). Дополнительные сведения о типах маркеров и утверждениях см. в статье [Справочник по токенам в Azure AD](active-directory-token-and-claims.md).

Ниже перечислены возможные типы утверждений параметра `id_token`.

| Тип утверждения | ОПИСАНИЕ |
| --- | --- |
| aud |Аудитория маркера. После выдачи маркера клиентскому приложению аудитория будет содержать идентификатор клиента ( `client_id` ). |
| exp |Время окончания срока действия. Это время, когда истекает срок действия маркера. Чтобы маркер был действительным, значение `exp` должно превышать текущее значение даты и времени. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до истечения срока действия маркера.|
| family_name |Фамилия пользователя. Это значение может отображаться в приложении. |
| given_name |Имя пользователя. Это значение может отображаться в приложении. |
| iat |Время выдачи. Это время выдачи JWT. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| iss |Указывает поставщик маркера. |
| nbf |Не ранее этого времени. Время, когда маркер начинает действовать. Чтобы маркер был действителен, текущие дата и время должны быть больше значения nbf или равны ему. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| oid |Идентификатор объекта-пользователя в Azure AD. |
| sub |Идентификатор субъекта маркера. Это постоянный и неизменяемый идентификатор для пользователя, описываемого маркером. Используйте это значение в логике кэширования. |
| tid |Идентификатор клиента Azure AD, выдавшего маркер. |
| unique_name |Уникальный идентификатор, отображаемый для пользователя. Как правило, это имя участника-пользователя (UPN). |
| upn |Имя участника-пользователя. |
| ver |Версия. Версия JWT, обычно 1.0. |

### <a name="error-response"></a>Сообщение об ошибке
Ошибки конечной точки выдачи маркера — это коды ошибок HTTP, так как клиент вызывает конечную точку выдачи маркера напрямую. В дополнение к коду состояния HTTP конечная точка выдачи маркера Azure AD также возвращает документ JSON с объектами, описывающими ошибку.

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
| Параметр | ОПИСАНИЕ |
| --- | --- |
| error |Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| error_description |Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |
| error_codes |Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике. |
| timestamp |Время возникновения ошибки. |
| trace_id |Уникальный идентификатор для запроса, который может помочь при диагностике. |
| correlation_id |Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов. |

#### <a name="http-status-codes"></a>Коды состояния HTTP
В следующей таблице представлены коды состояния HTTP, которые возвращает конечная точка выдачи маркера. В некоторых случаях кода ошибки достаточно для описания ответа, но в случае получения ошибки необходимо проанализировать соответствующий документ JSON и изучить его код ошибки.

| Код HTTP | ОПИСАНИЕ |
| --- | --- |
| 400 |Код HTTP по умолчанию. Используется в большинстве случаев, и, как правило, его получают из-за неправильного формата запроса. Исправьте запрос и отправьте его повторно. |
| 401 |Сбой проверки подлинности. Например, в запросе отсутствует параметр client_secret. |
| 403 |Ошибка авторизации. К примеру, у пользователя нет разрешения на доступ к ресурсу. |
| 500 |Произошла внутренняя ошибка в службе. Повторите запрос. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Коды ошибок конечных точек токенов
| Код ошибки | ОПИСАНИЕ | Действие клиента |
| --- | --- | --- |
| invalid_request |Ошибка протокола, например отсутствует обязательный параметр. |Исправьте запрос и отправьте его повторно. |
| invalid_grant |Код авторизации недопустимый или истек срок его действия. |Попробуйте создать новый запрос к конечной точке `/authorize`. |
| unauthorized_client |У клиента, прошедшего проверку подлинности, нет прав на использование этого типа предоставления авторизации. |Как правило, это происходит, если клиентское приложение не зарегистрировано в Azure AD или не добавлено в клиент Azure AD пользователя. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |
| invalid_client |Сбой проверки подлинности клиента. |Недопустимые учетные данные клиента. Чтобы устранить проблему, администратору приложения необходимо обновить учетные данные. |
| unsupported_grant_type |Сервер авторизации не поддерживает тип предоставления авторизации. |Измените тип предоставления в запросе. Ошибка этого типа должна происходить только во время разработки, и ее должны обнаружить при первоначальном тестировании. |
| invalid_resource |Целевой ресурс недопустим, так как он не существует. Azure AD не удается найти ресурс, или он настроен неправильно. |Это означает, что ресурс, если он существует, не настроен в клиенте. Приложение может отобразить пользователю запрос с инструкцией по установке приложения и его добавлению в Azure AD. |
| interaction_required |Для запроса требуется взаимодействие с пользователем. К примеру, требуется дополнительный шаг проверки подлинности. | Вместо неинтерактивного запроса повторите попытку, выполнив интерактивный запрос авторизации для того же ресурса. |
| temporarily_unavailable |Сервер временно занят и не может обработать запрос. |Повторите запрос. Клиентское приложение может отобразить для пользователя сообщение о том, что его ответ задерживается из-за временных условий. |

## <a name="use-the-access-token-to-access-the-resource"></a>Использование маркера доступа для доступа к ресурсу
Успешно получив `access_token`, вы можете использовать этот маркер в запросах к веб-API, включая его в заголовок `Authorization`. В спецификации [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) описывается, как использовать маркеры носителей в HTTP-запросах на доступ к защищенным ресурсам.

### <a name="sample-request"></a>Пример запроса
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Сообщение об ошибке
Защищенные ресурсы, которые реализуют спецификацию RFC 6750, выдают коды состояния HTTP. Если запрос не содержит учетные данные проверки подлинности или не содержит маркер, ответ будет включать заголовок `WWW-Authenticate` . Если запрос завершился сбоем, то сервер ресурсов отправит ответ с кодом состояния HTTP и кодом ошибки.

Ниже приведен пример неудачного ответа, когда клиентский запрос не содержит токен носителя:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Параметры ошибок
| Параметр | ОПИСАНИЕ |
| --- | --- |
| authorization_uri |Универсальный код ресурса (URI) (физическая конечная точка) сервера авторизации. Это значение также используется в качестве ключа поиска для получения дополнительных сведений о сервере из конечной точки обнаружения. <p><p> Клиент должен проверить, является ли сервер авторизации доверенным. Если ресурс защищен с помощью Azure AD, то достаточно убедиться, что URL-адрес начинается с https://login.microsoftonline.com или другого имени узла, поддерживаемого Azure AD. Клиентский ресурс всегда должен возвращать URI авторизации определенного клиента. |
| error |Значение кода ошибки, определенное в разделе 5.2 документации по [OAuth 2.0 Authorization Framework](http://tools.ietf.org/html/rfc6749). |
| error_description |Более подробное описание ошибки. Это сообщение не должно быть понятным для пользователей. |
| resource_id |Возвращает уникальный идентификатор ресурса. Клиентское приложение может использовать этот идентификатор в качестве значения параметра `resource` в запросе на получение маркера для ресурса. <p><p> Важно, чтобы клиентское приложение проверяло это значение, так как вредоносная служба сможет использовать атаку **повышения привилегий**. <p><p> Для предотвращения атак рекомендуется, чтобы значение `resource_id` соответствовало базовому URL-адресу веб-API, к которому выполняется доступ. Например, при доступе к https://service.contoso.com/data параметр `resource_id` может иметь значение htttps://service.contoso.com/. Клиентское приложение должно отклонять `resource_id` , если это значение не начинается с базового URL-адреса и у вас нет возможности проверить идентификатор другим способом. |

#### <a name="bearer-scheme-error-codes"></a>Коды ошибок схемы носителя
В спецификации RFC 6750 определены следующие ошибки для ресурсов, использующих в ответе заголовок WWW-Authenticate и схему носителя.

| Код состояния HTTP | Код ошибки | ОПИСАНИЕ | Действие клиента |
| --- | --- | --- | --- |
| 400 |invalid_request |У запроса неправильный формат. Например, в нем может отсутствовать параметр или один и тот же параметр может использоваться дважды. |Исправьте ошибку и повторите запрос. Ошибка этого типа должна происходить только во время разработки, и ее должны обнаружить при первоначальном тестировании. |
| 401 |invalid_token |Маркер доступа отсутствует, недопустим или отменен. Значение параметра error_description содержит дополнительные сведения. |Запросите новый маркер из сервера авторизации. Если произойдет сбой нового маркера, это значит, что произошла непредвиденная ошибка. Отправьте пользователю сообщение об ошибке и повторите попытку через произвольные промежутки времени. |
| 403 |insufficient_scope |Маркер доступа не содержит разрешения на олицетворение, необходимые для доступа к ресурсу. |Отправьте новый запрос авторизации в конечную точку авторизации. Если в ответе содержится параметр области, используйте значение области в запросе к ресурсу. |
| 403 |insufficient_access |У субъекта маркера нет разрешений, необходимых для доступа к ресурсу. |Предложите пользователю использовать другую учетную запись или запросите разрешения для указанного ресурса. |

## <a name="refreshing-the-access-tokens"></a>Обновление маркеров доступа
Срок действия маркеров доступа весьма ограничен, поэтому их нужно обновлять после его истечения, чтобы и далее получать доступ к ресурсам. Чтобы обновить `access_token`, следует отправить новый запрос `POST` к конечной точке `/token`, используя `refresh_token` вместо `code`.

Для маркеров обновления не устанавливается определенное время существования. Обычно у маркеров обновления относительно продолжительное время существования. Но в некоторых случаях маркер обновления оказывается устаревшим или отозванным или не имеет достаточных привилегий для требуемого действия. Ваше приложение должно ожидать и правильно обрабатывать ошибки, возвращаемые конечной точкой выдачи маркера.

При получении ответа с ошибкой маркера обновления следует удалить используемый маркер обновления и запросить новый код авторизации или маркер доступа. В частности, если вы используете маркер обновления в потоке предоставления кода авторизации и получаете ответ с кодом ошибки `interaction_required` или `invalid_grant`, то следует удалить такой маркер обновления и запросить новый код авторизации.

Запрос к конечной точке **конкретного клиента** (можно также использовать **общую** конечную точку) для получения нового маркера доступа с использованием маркера обновления выглядит примерно так:

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

### <a name="successful-response"></a>Успешный ответ
Успешный ответ токена выглядит следующим образом:

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
| Параметр | ОПИСАНИЕ |
| --- | --- |
| token_type |Тип маркера. Поддерживается только одно значение — **bearer**. |
| expires_in |Оставшееся время существования маркера в секундах. Стандартное значение — 3600 (1 час). |
| expires_on |Дата и время окончания срока действия маркера. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. |
| resource |Определяет защищенный ресурс, для доступа к которому можно использовать маркер доступа. |
| scope |Разрешения на олицетворение, предоставленные собственному клиентскому приложению. По умолчанию используется разрешение **user_impersonation**. Владелец целевого ресурса может регистрировать дополнительные значения в Azure AD. |
| access_token |Новый запрошенный маркер доступа. |
| refresh_token |Новый маркер обновления OAuth 2.0, который можно использовать, чтобы запрашивать новые маркеры доступа, когда истечет срок действия маркера в этом ответе. |

### <a name="error-response"></a>Сообщение об ошибке
Пример сообщения-ответа об ошибке выглядит следующим образом:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| Параметр | ОПИСАНИЕ |
| --- | --- |
| error |Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| error_description |Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |
| error_codes |Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике. |
| timestamp |Время возникновения ошибки. |
| trace_id |Уникальный идентификатор для запроса, который может помочь при диагностике. |
| correlation_id |Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов. |

Описание кодов ошибок и рекомендуемых действий в клиенте см. в разделе [Коды ошибок конечных точек токенов](#error-codes-for-token-endpoint-errors).
