
<properties
	pageTitle="Поток кода авторизации OAuth 2.0 в Azure AD | Microsoft Azure"
	description="Построение веб-приложений с помощью реализации протокола проверки подлинности OAuth 2.0 в Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Протоколы версии 2.0 — поток кода авторизации OAuth 2.0

Код авторизации OAuth 2.0 может использоваться в приложениях, установленных на устройстве, для получения доступа к защищенным ресурсам, таким как веб-API. С помощью реализации OAuth 2.0 в модели приложений версии 2.0 можно добавить возможности входа и доступа к API в мобильные и классические приложения. Это руководство не зависит от языка и описывает, как отправлять и получать сообщения HTTP, не используя ни одну из наших библиотек с открытым исходным кодом.

<!-- TODO: Need link to libraries -->	

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).

Описание потока кода авторизации OAuth 2.0 можно найти в [разделе 4.1 спецификации OAuth 2.0](http://tools.ietf.org/html/rfc6749). Он используется для проверки подлинности и авторизации большинства типов приложений, включая [веб-приложения](active-directory-v2-flows.md#web-apps) и [изначально установленные приложения](active-directory-v2-flows.md#mobile-and-native-apps). Он позволяет приложениям безопасно получать маркеры доступа, с помощью которых можно получить доступ к ресурсам, защищенным с использованием конечной точки версии 2.0.

## Схема протокола
В общих чертах весь поток проверки подлинности для собственных или мобильных приложений можно представить следующим образом:

![Поток кода проверки подлинности OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Запрос кода авторизации
Поток кода авторизации начинается с того, что клиент направляет пользователя к конечной точке `/authorize`. В этом запросе клиент указывает разрешения, которые ему требуется получить от пользователя:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] Попробуйте вставить запрос, показанный ниже, в браузер.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

| Параметр | | Описание |
| ----------------------- | ------------------------------- | --------------- |
| tenant | обязательно | Значение `{tenant}` в пути запроса можно использовать для контроля над тем, кто может выполнять вход в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в разделе [основных сведений о протоколе](active-directory-v2-protocols.md#endpoints). |
| client\_id | обязательно | Идентификатор приложения, назначенный приложению порталом регистрации ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)). |
| response\_type | обязательно | Должен содержать `code` для потока кода авторизации. |
| redirect\_uri | рекомендуется | URI перенаправления приложения, на который можно отправлять ответы проверки подлинности для их получения приложением. Он должен в точности соответствовать одному из URI перенаправления, зарегистрированных на портале, но иметь форму закодированного URL-адреса. Для собственных и мобильных приложений следует использовать значение `urn:ietf:wg:oauth:2.0:oob` по умолчанию. |
| scope | обязательно | Разделенный пробелами список [областей](active-directory-v2-scopes.md), для которого необходимо согласие пользователя. |
| response\_mode | рекомендуется | Указывает метод, с помощью которого результирующий маркер будет отправлен приложению. Может иметь значение `query` или `form_post`. |
| state | рекомендуется | Значение, включенное в запрос, которое также возвращается в ответе маркера. Это может быть строка любого контента. Как правило, для [предотвращения подделки межсайтовых запросов](http://tools.ietf.org/html/rfc6749#section-10.12) используется генерируемое случайным образом уникальное значение. Параметр "state" также используется для кодирования информации о состоянии пользователя в приложении перед созданием запроса на проверку подлинности, например информации об открытой на тот момент странице или представлении. |
| prompt | необязательный | Указывает требуемый тип взаимодействия с пользователем. На текущий момент единственные допустимые значения — "login", "none" и "consent". При значении `prompt=login` пользователю придется вводить учетные данные по запросу. Единый вход не сработает. Значение `prompt=none` является противоположным — оно гарантирует, что интерактивные запросы не будут выводиться ни при каких обстоятельствах. Если запрос не удастся завершить автоматически через единый вход, конечная точка версии 2.0 вернет ошибку. После входа пользователя `prompt=consent` откроет диалоговое окно согласия OAuth с запросом на предоставление разрешений приложению. |
| login\_hint | необязательный | Можно применять для предварительного заполнения поля имени пользователя или электронного адреса на странице входа пользователя (если имя пользователя известно заранее). Зачастую этот параметр используется в приложениях при повторной аутентификации. При этом имя пользователя извлекается во время предыдущего входа с помощью утверждения `preferred_username`. |
| domain\_hint | необязательный | Может использоваться значение `consumers` или `organizations`. Если используется этот параметр, процесс обнаружения на основе электронной почты, который проходит пользователь на странице входа в приложение версии 2.0, пропускается, что существенно оптимизирует работу. Обычно этот параметр используется в приложениях при повторной аутентификации. Для этого значение утверждения `tid` извлекается из предыдущего сеанса входа. Если значение утверждения `tid` — `9188040d-6c67-4c5b-b112-36a304b66dad`, следует использовать `domain_hint=consumers`. Или используйте `domain_hint=organizations`. |

На текущем этапе пользователю придется ввести учетные данные и завершить проверку подлинности. Конечная точка версии 2.0 также обеспечит согласие пользователя на предоставление разрешений, указанных в параметре запроса `scope`. Если пользователь не предоставил какие-либо из этих разрешений, конечная точка запросит их у пользователя. Подробные сведения о [разрешениях, согласии на предоставление и мультитенантных приложениях можно найти здесь](active-directory-v2-scopes.md).

После того как пользователь пройдет проверку подлинности и предоставит разрешения, конечная точка версии 2.0 вернет приложению ответ на указанный `redirect_uri` с помощью метода, указанного в параметре `response_mode`.

#### Успешный ответ
Успешный ответ с использованием метода `response_mode=query` выглядит следующим образом:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Параметр | Описание |
| ----------------------- | ------------------------------- |
| Код | Запрашиваемый приложением код авторизации. Приложение может использовать код авторизации для запроса маркера доступа для целевого ресурса. Срок действия кодов авторизации крайне мал и обычно истекает по прошествии порядка 10 минут. |
| state | Если запрос содержит параметр "state", в ответе должно отображаться то же значение. Приложение должно проверить, совпадают ли значения параметра "state" в запросе и ответе. |

#### Сообщение об ошибке
Сообщения об ошибках также можно отправлять на `redirect_uri`, чтобы приложение обрабатывало их должным образом:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Параметр | Описание |
| ----------------------- | ------------------------------- |
| error | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| error\_description | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |

## Запрос маркера доступа
После получения кода авторизации и разрешения от пользователя вы можете применить `code` для получения `access_token` к требуемому ресурсу путем отправки запроса `POST` на конечную точку `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] Попробуйте импортировать приведенную ниже команду curl в приложение Postman (для успешного выполнения вам потребуется заменить значение `code` собственным значением).

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```


| Параметр | | Описание |
| ----------------------- | ------------------------------- | --------------------- |
| tenant | обязательно | Значение `{tenant}` в пути запроса можно использовать для контроля над тем, кто может выполнять вход в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в разделе [основных сведений о протоколе](active-directory-v2-protocols.md#endpoints). |
| client\_id | обязательно | Идентификатор приложения, назначенный приложению порталом регистрации ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)). |
| grant\_type | обязательно | Должен быть `authorization_code` для потока кода авторизации. |
| scope | обязательно | Список областей с разделителями-пробелами. Области, запрашиваемые на этом участке, должны быть эквивалентны областям, запрашиваемым на первом участке, или являться их подмножеством. Если области, указанные в этом запросе, охватывают несколько серверов ресурсов, конечная точка версии 2.0 вернет маркер для ресурса, указанного в первой области. Более подробное описание областей можно найти в разделе, посвященном [разрешениям, согласию на их предоставление и областям](active-directory-v2-scopes.md). |
| Код | обязательно | Код авторизации, полученный на первом участке потока. |
| redirect\_uri | обязательно | Значение redirect\_uri, которое использовалось для получения кода authorization\_code. |
| client\_secret | необходим для веб-приложений | Секрет приложения, созданный на портале регистрации для приложения. Его не следует использовать в собственном приложении, поскольку невозможно обеспечить полную безопасность секретов клиентов при их хранении на устройствах. Этот секрет требуется для веб-приложений и веб-API с возможностью безопасного хранения секрета клиента на сервере. |

#### Успешный ответ
Успешный ответ маркера выглядит следующим образом:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Параметр | Описание |
| ----------------------- | ------------------------------- |
| access\_token | Запрашиваемый маркер доступа. Приложение может использовать этот маркер для проверки подлинности на защищенном ресурсе, таком как веб-API. |
| token\_type | Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD, — носитель |
| expires\_in | Срок действия маркера доступа (в секундах). |
| scope | Области, для которых действителен маркер доступа. |
| refresh\_token | Маркер обновления OAuth 2.0. Приложение может использовать этот маркер для получения дополнительных маркеров доступа после истечения срока действия текущего маркера доступа. Маркеры обновления действуют в течение долгого времени. Их можно использовать для длительного сохранения доступа к ресурсам. Дополнительные сведения можно найти в [справочном материале по маркерам версии 2.0](active-directory-v2-tokens.md). |
| id\_token | Неподписанный веб-маркер JSON (JWT). Приложение может base64Url декодировать сегменты этого маркера, чтобы запрашивать сведения о пользователе, выполнившем вход. Приложение может кэшировать и отображать значения, но оно не должно полагаться на них при авторизации или в целях безопасности. Дополнительные сведения о маркерах id\_token можно найти в [справочнике по маркерам конечной точки версии 2.0](active-directory-v2-tokens.md). |

#### Сообщение об ошибке
Сообщения об ошибках выглядят следующим образом:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Параметр | Описание |
| ----------------------- | ------------------------------- |
| error | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| error\_description | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |
| error\_codes | Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике. |
| Timestamp | Время возникновения ошибки. |
| trace\_id | Уникальный идентификатор для запроса, который может помочь при диагностике. |
| correlation\_id | Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов. |

## Использование маркера доступа
Успешно получив `access_token`, вы можете использовать маркер в запросах в веб-API путем включения его в заголовок `Authorization`:

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] Попробуйте использовать следующую команду (заменив маркер собственным значением).

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## Обновление маркера доступа
Срок действия маркеров доступа весьма ограничен, поэтому их нужно обновлять после его истечения, чтобы продолжить пользоваться запросами. Для этого можно отправить еще один запрос `POST` на конечную точку `/token`, прибегнув к `refresh_token` вместо `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] Попробуйте импортировать приведенную ниже команду curl в приложение Postman (для успешного выполнения вам потребуется заменить маркер refresh\_token собственным значением).

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Параметр | | Описание |
| ----------------------- | ------------------------------- | -------- |
| tenant | обязательно | Значение `{tenant}` в пути запроса можно использовать для контроля над тем, кто может выполнять вход в приложение. Допустимые значения: `common`, `organizations`, `consumers`, а также идентификаторы клиента. Дополнительные сведения см. в разделе [основных сведений о протоколе](active-directory-v2-protocols.md#endpoints). |
| client\_id | обязательно | Идентификатор приложения, назначенный приложению порталом регистрации ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)). |
| grant\_type | обязательно | Должен быть `refresh_token` для этого участка потока кода авторизации. |
| scope | обязательно | Список областей с разделителями-пробелами. Области, запрашиваемые на этом участке, должны быть эквивалентны областям, запрашиваемым на исходном участке запроса кода авторизации, или являться их подмножеством. Если области, указанные в этом запросе, охватывают несколько серверов ресурсов, конечная точка версии 2.0 вернет маркер для ресурса, указанного в первой области. Более подробное описание областей можно найти в разделе, посвященном [разрешениям, согласию на их предоставление и областям](active-directory-v2-scopes.md). |
| refresh\_token | обязательно | Маркер обновления, полученный на втором участке потока. |
| redirect\_uri | обязательно | Значение redirect\_uri, которое использовалось для получения кода authorization\_code. |
| client\_secret | необходим для веб-приложений | Секрет приложения, созданный на портале регистрации для приложения. Его не следует использовать в собственном приложении, поскольку невозможно обеспечить полную безопасность секретов клиентов при их хранении на устройствах. Этот секрет требуется для веб-приложений и веб-API с возможностью безопасного хранения секрета клиента на сервере. |

#### Успешный ответ
Успешный ответ маркера выглядит следующим образом:

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Параметр | Описание |
| ----------------------- | ------------------------------- |
| access\_token | Запрашиваемый маркер доступа. Приложение может использовать этот маркер для проверки подлинности на защищенном ресурсе, таком как веб-API. |
| token\_type | Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD, — носитель |
| expires\_in | Срок действия маркера доступа (в секундах). |
| scope | Области, для которых действителен маркер доступа. |
| refresh\_token | Новый маркер обновления OAuth 2.0. Вам следует заменить старый маркер обновления вновь полученным, чтобы обеспечить максимальный срок действия маркеров обновления. |
| id\_token | Неподписанный веб-маркер JSON (JWT). Приложение может base64Url декодировать сегменты этого маркера, чтобы запрашивать сведения о пользователе, выполнившем вход. Приложение может кэшировать и отображать значения, но оно не должно полагаться на них при авторизации или в целях безопасности. Дополнительные сведения о маркерах id\_token можно найти в [справочнике по маркерам конечной точки версии 2.0](active-directory-v2-tokens.md). |

#### Сообщение об ошибке
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Параметр | Описание |
| ----------------------- | ------------------------------- |
| error | Строка кода ошибки, которую можно использовать для классификации типов возникающих ошибок и реагирования на них. |
| error\_description | Конкретное сообщение об ошибке, с помощью которого разработчик может определить причину возникновения ошибки проверки подлинности. |
| error\_codes | Список кодов ошибок, характерных для службы маркеров безопасности, которые могут помочь при диагностике. |
| Timestamp | Время возникновения ошибки. |
| trace\_id | Уникальный идентификатор для запроса, который может помочь при диагностике. |
| correlation\_id | Уникальный идентификатор для запроса, который может помочь при диагностике нескольких компонентов. |

<!---HONumber=AcomDC_0302_2016-->