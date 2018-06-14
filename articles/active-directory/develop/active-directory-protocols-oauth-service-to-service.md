---
title: Взаимная проверка подлинности между службами Azure AD с помощью OAuth 2.0 | Документация Майкрософт
description: В этой статье описывается, как использовать HTTP-сообщения для проверки подлинности между службами с помощью потока предоставления учетных данных клиента OAuth2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1a4da1af61be4b632b51f5b8f921f6f48e925559
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158199"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Вызовы между службами с помощью учетных данных клиента (общий секрет или сертификат)
Процесс предоставления учетных данных клиента OAuth 2.0 позволяет веб-службе (*конфиденциальный клиент*) вместо олицетворения пользователя использовать свои собственные учетные данные для аутентификации при вызове другой веб-службы. В этом сценарии клиент обычно является службой среднего уровня, веб-службой, службой управляющей программы или веб-сайтом. Для большей надежности Azure AD также позволяет вызывающей службе использовать в качестве учетных данных сертификат (вместо общего секрета).

## <a name="client-credentials-grant-flow-diagram"></a>Схема потока предоставления учетных данных клиента
На следующей схеме показано, как работает поток предоставления учетных данных клиента в Azure Active Directory (Azure AD).

![Поток предоставления учетных данных клиента OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. Клиентское приложение выполняет проверку подлинности на конечной точке выдачи маркера Azure AD и запрашивает маркер доступа.
2. Конечная точка выдачи маркера Azure AD выдает маркер доступа.
3. Маркер доступа используется для проверки подлинности при обращении к защищенному ресурсу.
4. Данные из защищенного ресурса возвращаются в веб-приложение.

## <a name="register-the-services-in-azure-ad"></a>Регистрация служб в Azure AD
Зарегистрируйте вызывающую службу и службу, принимающую вызов, в Azure Active Directory (Azure AD). Подробные инструкции см. в статье [Интеграция приложений с Azure Active Directory](active-directory-integrating-applications.md).

## <a name="request-an-access-token"></a>Запрос маркера доступа
Чтобы запросить маркер доступа, используйте запрос HTTP POST к конечной точке Azure AD конкретного клиента.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Запрос маркера взаимного доступа между службами
Существует два сценария. Их выбор зависит от типа защиты клиентского приложения — с помощью общего секрета или с помощью сертификата.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Первый сценарий: запрос маркера доступа с помощью общего секрета
При использовании общего секрета запрос маркера взаимного доступа между службами содержит следующие параметры:

| Параметр |  | ОПИСАНИЕ |
| --- | --- | --- |
| grant_type |обязательно |Указывает запрашиваемый тип предоставления. В потоке предоставления учетных данных клиента этот параметр должен иметь значение **client_credentials**. |
| client_id |обязательно |Указывает идентификатор клиента Azure AD вызывающей веб-службы. Чтобы узнать идентификатор клиента вызывающего приложения, на [портале Azure](https://portal.azure.com) последовательно выберите **Azure Active Directory** и **Регистрация приложений**, затем щелкните приложение. Параметр client_id — это *идентификатор приложения*. |
| client_secret |обязательно |Введите ключ, зарегистрированный для вызывающей веб-службы или управляющей программы в Azure AD. Чтобы создать ключ, на портале Azure последовательно выберите **Azure Active Directory** и **Регистрация приложений**, щелкните приложение, последовательно выберите **Параметры** и **Ключи**, затем добавьте ключ.|
| resource |обязательно |Введите URI идентификатора приложения принимающей вызов веб-службы. Чтобы найти URI кода приложения, на портале Azure последовательно выберите **Azure Active Directory** и **Регистрация приложений**, щелкните приложение службы, затем последовательно выберите **Параметры** и **Свойства**. |

#### <a name="example"></a>Пример
Ниже приведен запрос HTTP POST на маркер доступа для веб-службы https://service.contoso.com/. Параметр `client_id` определяет веб-службу, которая запрашивает маркер доступа.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Второй сценарий: запрос маркера доступа с помощью сертификата
Запрос маркера взаимного доступа между службами с помощью сертификата содержит следующие параметры:

| Параметр |  | ОПИСАНИЕ |
| --- | --- | --- |
| grant_type |обязательно |Указывает запрашиваемый тип ответа. В потоке предоставления учетных данных клиента этот параметр должен иметь значение **client_credentials**. |
| client_id |обязательно |Указывает идентификатор клиента Azure AD вызывающей веб-службы. Чтобы узнать идентификатор клиента вызывающего приложения, на [портале Azure](https://portal.azure.com) последовательно выберите **Azure Active Directory** и **Регистрация приложений**, затем щелкните приложение. Параметр client_id — это *идентификатор приложения*. |
| client_assertion_type |обязательно |Значение должно быть `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |обязательно | Утверждение (JSON Web Token), которое необходимо создать и подписать с помощью сертификата, зарегистрированного как учетные данные для приложения. Ознакомьтесь с [учетными данными сертификата](active-directory-certificate-credentials.md), чтобы узнать, как зарегистрировать сертификат и отформатировать утверждение.|
| resource | обязательно |Введите URI идентификатора приложения принимающей вызов веб-службы. Чтобы найти URI кода приложения, на портале Azure последовательно выберите **Azure Active Directory** и **Регистрация приложений**, щелкните приложение службы, затем последовательно выберите **Параметры** и **Свойства**. |

Обратите внимание на то, что параметры являются почти такими же, как и при использовании запроса с помощью общего секрета, за исключением параметра client_secret, который заменяется двумя параметрами: client_assertion_type и client_assertion.

#### <a name="example"></a>Пример
Ниже приведен запрос HTTP POST на маркер доступа для веб-службы https://service.contoso.com/ с сертификатом. Параметр `client_id` определяет веб-службу, которая запрашивает маркер доступа.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Ответ маркера взаимного доступа между службами

Если доступ предоставлен, то ответ будет содержать JSON-файл OAuth 2.0 со следующими параметрами:

| Параметр | ОПИСАНИЕ |
| --- | --- |
| access_token |Запрашиваемый маркер доступа. Вызывающая веб-служба может использовать этот маркер для проверки подлинности принимающей веб-службы. |
| token_type |Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — **носитель**. Дополнительные сведения о маркерах носителей см. в спецификации [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (OAuth2.0 Authorization Framework: использование маркера носителя (RFC 6750)). |
| expires_in |Срок действия маркера доступа (в секундах). |
| expires_on |Время истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. Это значение используется для определения времени существования кэшированных маркеров. |
| not_before |Время, начиная с которого маркер доступа становится доступным. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия маркера.|
| resource |URI идентификатора приложения принимающей вызов веб-службы. |

#### <a name="example-of-response"></a>Пример ответа
В следующем примере показано сообщение о предоставлении доступа в ответ на запрос маркера доступа к веб-службе.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>См. также
* [OAuth 2.0 в Azure AD](active-directory-protocols-oauth-code.md)
* [Пример вызова между службами с помощью общего секрета (на языке C#)](https://github.com/Azure-Samples/active-directory-dotnet-daemon) и [Пример вызова между службами с помощью сертификата (на языке C#)](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
