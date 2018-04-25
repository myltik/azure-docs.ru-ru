---
title: Сведения о различных типах маркеров и утверждений, поддерживаемых Azure AD | Документация Майкрософт
description: Руководство с общими данными и анализом утверждений в токенах SAML 2.0 и веб-токенах JSON (JWT), выдаваемых службой Azure Active Directory (AAD)
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: a28811437668488c2207535cef3aa4640f17aa54
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
---
# <a name="azure-ad-token-reference"></a>Справочник по токенам в Azure AD
При обработке каждого потока проверки подлинности Azure Active Directory (Azure AD) создает токены безопасности различных типов. В этом документе описывается формат, характеристики безопасности и содержимое каждого типа маркера.

## <a name="types-of-tokens"></a>Типы маркеров
Служба Azure AD поддерживает [протокол авторизации OAuth 2.0](active-directory-protocols-oauth-code.md), использующий как маркеры доступа, так и маркеры обновления.  Она также поддерживает аутентификацию и вход с помощью [OpenID Connect](active-directory-protocols-openid-connect-code.md), вводя третий тип маркера — маркер идентификации.  Каждый из этих маркеров представляется как "токен носителя".

Токен носителя — это упрощенный маркер безопасности, предоставляющий его носителя (предъявителю) доступ к защищенному ресурсу. В этом смысле предъявителем является любая сторона, которая может предъявить маркер. Хотя аутентификация с помощью Azure AD необходима для получения токена носителя, нужно выполнить действия по защите токена, чтобы предотвратить перехват несанкционированной стороной. В токенах носителя нет встроенного механизма для предотвращения несанкционированного использования, и они должны передаваться по защищенному каналу, например с использованием стандарта безопасности транспортного уровня (HTTPS). Если токен носителя передается в незашифрованном виде, может быть использована атака "злоумышленник в середине", чтобы получить токен и использовать его для несанкционированного доступа к защищенному ресурсу. Те же принципы безопасности применяются при сохранении или кэшировании маркеров носителя для последующего использования. Необходимо всегда проверять, что приложение передает и сохраняет токены носителя безопасным образом. Дополнительные сведения о безопасности в случае применения токенов носителя см. в [RFC 6750, раздел 5](http://tools.ietf.org/html/rfc6750).

Многие токены, формируемые Azure AD, реализуются в виде веб-токенов JSON Web Token (JWT).  Маркер JWT — это компактное и безопасное для URL-адреса средство передачи информации между двумя сторонами.  Сведения, содержащиеся в маркерах JWT, называются "утверждениями" информации о носителе и субъекте токена.  Утверждения в маркерах JWT — это объекты JSON, закодированные и сериализованные для передачи.  Так как формируемые Azure AD токены JWT подписываются, но не зашифровываются, их содержимое можно без труда изучить с целью отладки.  Для этого существует несколько инструментов, таких как [jwt.net](https://jwt.ms/). Дополнительные сведения о маркерах JWT можно найти в [спецификации JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Маркеры id_token
Маркеры id_token — это разновидность маркера безопасности входа, который приложение получается при аутентификации с использованием [OpenID Connect](active-directory-protocols-openid-connect-code.md).  Они представлены в виде [маркеров JWT](#types-of-tokens) и содержат утверждения, которые можно использовать для входа пользователя в приложение.  Вы можете использовать утверждения в маркере "id_token" на свое усмотрение. Как правило, их используют для отображения сведений об учетной записи или управления доступом в приложении.

На текущий момент маркеры "id_token" подписываются, но не зашифровываются.  Когда приложение получает маркер id_token, оно должно [проверить подпись](#validating-tokens), чтобы подтвердить подлинность маркера, и проверить несколько содержащихся в нем утверждений для подтверждения его действительности.  Утверждения, проверяемые приложением, зависят от требований сценария, но некоторые [стандартные проверки утверждений](#validating-tokens) приложение должно выполнять в каждом сценарии.

В следующем разделе приведены сведения об утверждениях маркеров id_token, а также пример маркера id_token.  Обратите внимание, что утверждения в маркерах "id_token" не возвращаются в определенном порядке.  Кроме того, в любой момент в маркеры "id_token" можно добавлять новые утверждения. При этом сбоев в работе приложения возникать не должно.  В следующем списке приведены утверждения, которые приложение гарантированно интерпретирует на момент написания этой статьи.  При необходимости дополнительные сведения можно найти в [спецификации OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Пример маркера id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Для тренировки попробуйте изучить утверждения в примере id_token, вставив его в [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Утверждения в маркерах id_token
> [!div class="mx-codeBreakAll"]
| Утверждение JWT | ИМЯ | ОПИСАНИЕ |
| --- | --- | --- |
| `appid` |Идентификатор приложения |Идентифицирует приложение, использующее токен для доступа к ресурсу. Приложение может действовать самостоятельно или от имени пользователя. Идентификатор приложения, как правило, представляет объект приложения, но может также представлять и объект субъекта-службы в Azure AD. <br><br> **Пример значения JWT**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |Аудитория |Целевой получатель маркера. Приложение, которое получает токен, должно проверять, верно ли значение, обозначающее аудиторию, и отклонять любые токены, предназначенные для другой аудитории. <br><br> **Пример значения SAML**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Пример значения JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Указывает на способ проверки подлинности клиента. Для общедоступного клиента значение равно 0. При использовании идентификатора и секрета клиента значение равно 1. <br><br> **Пример значения JWT**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Указывает, как проведена проверка подлинности субъекта, в отличие от клиента в утверждении Application Authentication Context Class Reference. Значение 0 означает, что аутентификация конечного пользователя не соответствовала требованиям ISO/IEC 29115. <br><br> **Пример значения JWT**: <br> `"acr": "0"` |
| Время выполнения проверки подлинности |Фиксирует дату и время выполнения сеанса проверки подлинности. <br><br> **Пример значения SAML**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Метод проверки подлинности |Указывает способ проверки подлинности субъекта токена. <br><br> **Пример значения SAML**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Пример значения JWT**: `“amr”: ["pwd"]` |
| `given_name` |Имя |Указывает на имя или заданное имя пользователя, которое задали в объекте пользователя Azure AD. <br><br> **Пример значения SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"given_name": "Frank"` |
| `groups` |Группы |Предоставляет идентификаторы объектов, представляющие членство субъекта в группах. Эти значения уникальны (см. раздел «Object ID»), их можно безопасно использовать для управления доступом, например для принудительной авторизации для доступа к ресурсу. Группы, входящие в утверждение Groups, настраиваются для конкретного приложения с помощью свойства groupMembershipClaims манифеста приложения. Если установлено значение null, исключаются все группы, если значение SecurityGroup — включается только членство в группах безопасности Active Directory, а значение All подразумевает включение как групп безопасности, так и списков рассылки Office 365. <br><br> **Примечания** <br> См. описание утверждения `hasgroups` ниже, чтобы получить сведения об использовании утверждения `groups` с неявным разрешением.  <br> Для других потоков: если число групп, в которые входит пользователь, превышает лимит (150 для SAML и 200 для JWT), тогда избыточное утверждение будет добавлено к источникам утверждений, указывая на конечную точку Graph, содержащую список групп для пользователя . <br><br> **Пример значения SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Пример значения JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | Индикатор превышения групп неявного потока JWT| Если он присутствует, всегда имеет значение `true`. Это обозначает, что пользователь входит как минимум в одну группу.  Используется вместо утверждения `groups` для JWT в неявных потоках предоставления, если утверждение полной группы расширяет фрагмент URI за пределы ограничения длины URL-адреса (в настоящее время 6 или более групп).  Указывает на то, что клиент должен использовать Graph для определения групп пользователя (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Индикатор превышения групп | Для запросов маркеров, которые не ограничены по длине (см. `hasgroups` выше), но все еще слишком большие для маркеров, будет добавлена ссылка на полный список групп, в которые входит пользователь.  Используется для JWT в качестве распределенного утверждения и для SAML в качестве нового утверждения вместо `groups`. <br><br> **Пример значения SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Поставщик удостоверений |Фиксирует поставщика удостоверений, который проверил подлинность субъекта маркера. Это значение идентично значению утверждения Issuer за исключением случаев, когда учетная запись пользователя и издатель принадлежат разным клиентам. <br><br> **Пример значения SAML**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Указывает время выдачи токена. Оно часто используется для определения времени существования токена. <br><br> **Пример значения SAML**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Пример значения JWT**: <br> `"iat": 1390234181` |
| `iss` |Издатель |Обозначает службу токенов безопасности (STS), которая создает и возвращает токен. В возвращаемых службой Azure AD токенах указан следующий издатель: sts.windows.net. Идентификатор GUID в значении утверждения Issuer — это идентификатор клиента каталога Azure AD. Идентификатор клиента представляет собой неизменяемый и надежный идентификатор каталога. <br><br> **Пример значения SAML**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Пример значения JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Фамилия |Указывает фамилию пользователя в соответствии с определением в объекте пользователя Azure AD. <br><br> **Пример значения SAML**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |ИМЯ |Предоставляет удобное для восприятия значение, которое идентифицирует субъект маркера. Это значение не обязательно должно быть уникальным в пределах клиента. Оно предназначено только для отображения. <br><br> **Пример значения SAML**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Object ID |Содержит уникальный идентификатор объекта в Azure AD. Это значение является неизменяемым и не может быть переназначено или повторно использовано. Используйте идентификатор объекта для идентификации объекта в запросах, адресованных Azure AD. <br><br> **Пример значения SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Роли |Представляет все роли приложения, предоставленные субъекту напрямую или косвенно через членство в группе, и может использоваться для реализации управления доступом на основе ролей. Роли приложения определяются на уровне приложения с использованием свойства `appRoles` манифеста приложения. Свойство `value` каждой роли приложения представляет значение, которое отображается в утверждении Roles. <br><br> **Пример значения SAML**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Пример значения JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Область |Обозначает предоставленные клиентскому приложению разрешения на олицетворение. Разрешение по умолчанию — `user_impersonation`. Владелец защищаемого ресурса может регистрировать дополнительные значения в Azure AD. <br><br> **Пример значения JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Субъект |Указывает субъекта, сведения о котором утверждает токен, например данные о пользователе приложения. Это значение является неизменным, его невозможно назначить другому объекту или использовать повторно. Следовательно, это значение можно использовать для безопасного проведения проверок авторизации. Так как субъект всегда присутствует в выдаваемых Azure AD токенах, мы советуем использовать это значение в системе авторизации общего назначения. <br> `SubjectConfirmation` не является утверждением. Он описывает, как проверяется субъект токена. `Bearer` указывает, что субъект подтвержден благодаря владению токеном. <br><br> **Пример значения SAML**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Пример значения JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Tenant ID |Tenant ID — неизменяемый идентификатор без возможности повторного использования, который идентифицирует клиент каталога, выдавший токен. Это значение можно использовать для доступа к ресурсам каталога конкретного клиента в мультитенантном приложении. Например, это значение можно использовать для идентификации клиента при вызове API Graph. <br><br> **Пример значения SAML**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Пример значения JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Token Lifetime |Определяет интервал времени, в течение которого маркер является действительным. Служба, проверяющая токен, должна проверить, не выходит ли текущая дата за пределы периода действия токена. Если выходит, токен должен быть отклонен. Служба может разрешить использовать токен в течение пяти минут после окончания срока его действия, тем самым учитывая возможные различия во времени ("разница во времени") между Azure AD и службой. <br><br> **Пример значения SAML**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Пример значения JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |User Principal Name |Обеспечивает хранение имени субъекта-пользователя.<br><br> **Пример значения JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Version (версия) |Обеспечивает хранение номера версии токена. <br><br> **Пример значения JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Маркеры доступа
После успешной аутентификации Azure AD возвращает маркер доступа, который может использоваться для доступа к защищенным ресурсам. Маркер доступа представляет собой JSON Web Token (JWT) в кодировке Base-64, и его содержимое можно изучить с помощью декодера.

Если приложение *использует* маркеры доступа только для получения доступа к интерфейсам API, то можно (и нужно) считать маркеры доступа полностью непрозрачными. Это просто строки, которые ваше приложение может передавать ресурсам в HTTP-запросах.

При запросе токена доступа Azure AD также возвращает некоторые метаданные о токене доступа для приложения.  Эти сведения включают время окончания срока действия маркера доступа и области, для которых он действителен.  Благодаря этому приложение может выполнять интеллектуальное кэширование маркеров доступа без необходимости анализа самого маркера.

Если приложение является интерфейсом API, защищенным с помощью Azure AD, который ожидает маркеры доступа в HTTP-запросах, то следует осуществлять проверку и контроль получаемых маркеров. Приложение должно проверить маркер доступа, прежде чем использовать его для доступа к ресурсам. Дополнительные сведения о проверке см. в разделе [Проверка маркеров](#validating-tokens).  
Дополнительные сведения о том, как это сделать с помощью .NET, см. в разделе [Защита веб-интерфейса API с помощью маркеров носителя из Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Маркеры обновления

Маркеры обновления — это маркеры безопасности, которые приложение может использовать для получения новых маркеров доступа в потоке OAuth 2.0.  За счет этого приложение может получить длительный доступ к ресурсам от лица пользователя без необходимости участия самого пользователя.

Маркеры обновления относятся к нескольким ресурсам.  Иными словами, маркер обновления, полученный при запросе маркера для одного ресурса, можно обменять на маркеры доступа к совершенно другому ресурсу. Для этого в запросе к целевому источнику необходимо задать параметр `resource` .

Токены обновления абсолютно непрозрачны для приложения. Они действительны в течение продолжительного времени, но при создании приложение не следует ожидать, что срок действия маркера обновления неограничен.  Маркеры обновления могут стать недействительными в любое время по разным причинам. Дополнительные сведения см. в разделе [об отзыве маркеров](#token-revocation).  Единственный способ, с помощью которого приложение может выяснить, является ли токен действительным, — попытаться использовать его путем отправки запроса к конечной точке токена Azure.

При обмене токена обновления на новый токен доступа вы получите новый токен обновления в ответе токена.  Сохраняйте новый маркер обновления, заменив им использованный в запросе.  Такой подход обеспечит максимальный срок действия маркеров обновления.

## <a name="validating-tokens"></a>Проверка маркеров

Для проверки маркера id_token или access_token приложение должно проверить подпись маркера и содержащиеся в нем утверждения. Чтобы проверить маркеры доступа, приложению также следует проверить издателя, аудиторию и маркеры подписывания. Они должны проверяться на соответствие значениям в документе обнаружения OpenID. Например, версия документа для любых клиентов находится в [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration). ПО промежуточного слоя Azure AD имеет встроенные возможности для проверки маркеров доступа, и вы можете ознакомиться с нашими [примерами](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples), чтобы найти пример для выбранного вами языка. Чтобы получить дополнительные сведения о том, как явно проверить токен JWT, ознакомьтесь с [примером проверки JWT вручную](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).  

Мы предоставляем библиотеки и примеры кода, демонстрирующие удобный способ проверки токенов. Дальнейшие сведения приведены специально для желающих понять суть происходящего.  Для проверки токенов JWT также доступны несколько сторонних библиотек с открытым исходным кодом. Среди них вы найдете как минимум один вариант для каждой платформы и языка. Дополнительные сведения о библиотеках аутентификации Azure AD и примеры кода см. в статье [Библиотеки проверки подлинности Azure Active Directory](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Проверка подписи

Маркер JWT содержит три сегмента, разделенных символом `.` .  Первый сегмент называется **заголовком**, второй — **телом**, а третий — **подписью**.  Сегмент подписи можно использовать для проверки подлинности маркера, чтобы приложение доверяло ему.

Маркеры, выдаваемые Azure AD, подписываются при помощи стандартных отраслевых алгоритмов асимметричного шифрования, таких как RSA 256. Заголовок JWT содержит сведения о ключе и методе шифрования, используемых для подписания маркера.

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

Утверждение `alg` определяет алгоритм, использованный для подписания токена, а утверждение `x5t` — конкретный открытый ключ, использованный для подписания токена.

Azure AD может в любой момент времени подписать маркер идентификации с использованием любой пары открытого и закрытого ключей из определенного набора пар. Azure AD периодически изменяет возможный набор ключей, поэтому при создании приложения необходимо обеспечить автоматическую обработку подобных изменений ключей.  Рекомендуем проверять наличие обновлений открытых ключей, которые использует служба Azure AD, каждые 24 часа.

Данные ключа подписи, необходимые для проверки подписи, можно найти в документе метаданных OpenID Connect по ссылке:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Попробуйте ввести этот URL-адрес в браузере.
> 
> 

Этот документ метаданных представляет собой объект JSON, содержащий несколько ценных блоков информации, таких как расположение различных конечных точек, необходимых для проверки подлинности OpenID Connect.  

Он также включает `jwks_uri`, который содержит расположение набора общедоступных ключей, использованных для подписания маркеров.  Документ JSON, который находится в `jwks_uri` , содержит все сведения об открытых ключах, используемые в конкретный момент времени.  Приложение может использовать утверждение `kid` в заголовке маркера JWT, чтобы выбрать открытый ключ в этом документе, использованный для подписания определенного маркера.  Затем оно может выполнить проверку подписи с использованием правильного общедоступного ключа и указанного алгоритма.

Выполнение проверки подписи выходит за рамки этого документа. Если вам требуется помощь в этом вопросе, воспользуйтесь всевозможными библиотеками с открытым исходным кодом.

#### <a name="validating-the-claims"></a>Проверка утверждений

Когда приложение получает маркер (маркер id_token при входе пользователя или маркер доступа в качестве токена носителя в HTTP-запросе), оно может также выполнить несколько проверок утверждений этом в маркере.  Помимо прочего, к ним относятся:

* Утверждение **Audience**. Необходимо для подтверждения того, что маркер действительно должен был быть выдан вашему приложению.
* Утверждения **Not Before** и **Expiration Time**. Необходимы для подтверждения того, что срок действия маркера не истек.
* Утверждение **Издатель**. Необходимо, чтобы убедиться, что маркер действительно был выдан Azure AD для вашего приложения.
* **Специальное** утверждение. Необходимо для предотвращения атак с использованием воспроизведения маркеров.
* и многое другое...

Полный список проверок утверждений, которые ваше приложение должно выполнять для маркеров идентификации, см. в [спецификации OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Подробные сведения об ожидаемых значениях для этих утверждений можно найти выше в разделе о [маркерах id_token](#id-tokens).

## <a name="token-revocation"></a>Отзыв маркеров

Маркеры обновления могут стать недействительными или могут быть отозваны в любое время по разным причинам.  Эти причины делятся на две основные категории: время ожидания и отзыв. 
* Время ожидания для маркеров
  * MaxInactiveTime: ели маркер обновления не использовался в течение времени, указанного в параметре MaxInactiveTime, этот маркер обновления считается недействительным. 
  * MaxSessionAge: если MaxAgeSessionMultiFactor или MaxAgeSessionSingleFactor имеют значения, отличные от значений по умолчанию (Until-revoked), то по истечении периода, заданного параметром MaxAgeSession*, потребуется повторная аутентификация.  
  * Примеры:
    * Для клиента параметр MaxInactiveTime имеет значение 5 дней. Пользователь провел неделю в отпуске, а значит в течение 7 дней от него не поступало запросов к AAD на получение маркеров.  При следующем запросе маркера от этого пользователя выяснится, что маркер обновления уже отозван, а значит учетные данные нужно ввести заново. 
    * Для важного приложения параметр MaxAgeSessionSingleFactor имеет значение 1 день.  Если пользователь выполнит вход в понедельник, то по истечении 25 часов (во вторник) ему придется пройти аутентификацию повторно.  
* Запрет доступа
  * Добровольная смена пароля. Когда пользователь меняет свой пароль, для некоторых приложений может потребоваться повторная аутентификация, в зависимости от метода получения маркеров.  В примечаниях ниже описаны исключения. 
  * Принудительная смена пароля. Если пользователь изменяет или сбрасывает пароль в соответствии с обязательной политикой администратора, все маркеры этого пользователя, полученные с помощью старого пароля, становятся недействительными.  В примечаниях ниже описаны исключения. 
  * Нарушения безопасности. В случае нарушения безопасности системы (например, взлома локального хранилища паролей) администратор может отозвать все маркеры обновления, выданные на текущий момент.  В этом случае всем пользователям нужно пройти повторную аутентификацию. 

Примечание. 

Если для получения маркера используется метод аутентификации без пароля (Windows Hello, приложение для аутентификации или биометрические данные, например лица или отпечатки пальцев), то изменения пароля пользователя не потребует повторной аутентификации от самого пользователя (но приведет к принудительной повторной аутентификации для приложения аутентификации).  Это объясняется тем, что выбранный метод аутентификации (например, лицо) не изменяется, а значит может по-прежнему применяться для аутентификации.

## <a name="sample-tokens"></a>Примеры токенов

В этом разделе приводятся примеры токенов SAML и JWT, которые возвращает служба Azure AD. Они позволяют увидеть утверждения в контексте.

### <a name="saml-token"></a>Токен SAML

Ниже приведен пример типичного токена SAML.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>Токен JWT — олицетворение пользователя
Ниже приведен пример типичного токена JSON Web Token, используемого в потоке предоставления кода авторизации.
Помимо утверждений токен включает в себя номер версии в утверждениях **ver** и **appidacr**, а также ссылку на класс контекста аутентификации, указывающий на способ аутентификации клиента. Для общедоступного клиента значение равно 0. При использовании идентификатора или секрета клиента значение равно 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Связанная информация
* Чтобы узнать больше об управлении политикой времени существования маркера посредством API Graph Azure AD, ознакомьтесь с [операциями с политиками](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) и [сущностью политики](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) Azure AD Graph.
* Дополнительные сведения об управлении политиками посредством командлетов PowerShell, включая примеры, см. в разделе [Configurable Token Lifetimes in Azure Active Directory (Public Preview)](../active-directory-configurable-token-lifetimes.md) (Настраиваемое время существования маркеров в Azure Active Directory (общедоступная предварительная версия)). 
* Добавьте [пользовательские и необязательные утверждения](active-directory-optional-claims.md) в токены для приложения. 
