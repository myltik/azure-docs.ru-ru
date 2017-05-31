---
title: "Поток On-Behalf-Of в OAuth 2.0 в Azure AD 2.0 | Документы Майкрософт"
description: "В этой статье описывается, как использовать HTTP-сообщения для проверки подлинности между службами с помощью потока On-Behalf-Of в OAuth 2.0."
services: active-directory
documentationcenter: 
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 09f6f318-e88b-4024-9ee1-e7f09fb19a82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 00eccbf0778f241b1405eab6b77bcc6ab0c15072
ms.contentlocale: ru-ru
ms.lasthandoff: 05/08/2017


---
# <a name="azure-active-directory-v20-and-oauth-20-on-behalf-of-flow"></a>Azure Active Directory версии 2.0 и поток On-Behalf-Of в OAuth 2.0
Поток On-Behalf-Of в OAuth 2.0 используется в том случае, когда приложение вызывает API службы или веб-API, который, в свою очередь, должен вызывать другой API службы или веб-API. Идея состоит в том, чтобы распространить делегированное удостоверение пользователя и разрешения с помощью цепочки запросов. Для того чтобы служба среднего уровня могла выполнять запросы к службе нижнего уровня с проверкой подлинности, служба среднего уровня должна защитить токен доступа из Azure Active Directory (Azure AD) от имени пользователя.

> [!NOTE]
> Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, стоит ли вам использовать конечную точку версии 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).
>
>

## <a name="protocol-diagram"></a>Схема протокола
Предположим, что пользователь прошел проверку подлинности для приложения с использованием [потока для предоставления кода проверки подлинности в OAuth 2.0](active-directory-v2-protocols-oauth-code.md). На этом этапе приложение содержит токен доступа (токен A) с утверждениями пользователя и разрешение на доступ к веб-API среднего уровня (API A). Теперь API A должен выполнить запрос к веб-API нижнего уровня (API B) с проверкой подлинности.

Последующие шаги образуют поток On-Behalf-Of. Эти шаги поясняются на следующей схеме.

![Поток On-Behalf-Of в OAuth 2.0](media/active-directory-protocols-oauth-on-behalf-of/active-directory-protocols-oauth-on-behalf-of-flow.png)


1. Клиентское приложение отправляет запрос к API A с токеном A.
2. API A выполняет проверку подлинности на конечной точке выдачи токена Azure AD и запрашивает токен доступа к API B.
3. Конечная точка выдачи токена Azure AD проверяет учетные данные API A с помощью токена A и выдает токен доступа для API B (токен B).
4. Токен B устанавливается в заголовке проверке подлинности запроса к API B.
5. API B возвращает данные из защищенного ресурса.

> [!NOTE]
> В этом сценарии служба среднего уровня получает согласие пользователя на доступ к API нижнего уровня без взаимодействия с пользователем. Поэтому согласие на доступ к API нижнего уровня необходимо получить заранее при принятии условий во время проверки подлинности.
>

## <a name="service-to-service-access-token-request"></a>Запрос токена доступа между службами
Чтобы запросить токен доступа, отправьте запрос HTTP POST к конечной точке Azure AD версии 2.0 конкретного клиента с указанными параметрами.

```
https://login.microsoftonline.com/<tenant>/oauth2/v2.0/token
```

| Параметр |  | Описание |
| --- | --- | --- |
| grant_type |обязательно | Тип запроса токена. Для запроса с использованием JWT это значение должно быть равно **urn:ietf:params:oauth:grant-type:jwt-bearer**. |
| client_id |обязательно | Идентификатор приложения, присвоенный приложению на [портале регистрации приложений](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList). |
| client_secret |обязательно | Секрет приложения, созданный для приложения на портале регистрации приложений. |
| assertion |обязательно | Значение токена, используемого в запросе. |
| scope |обязательно | Список областей для запроса токена, разделенный пробелами. Дополнительные сведения см. в разделе [Области](active-directory-v2-scopes.md).|
| requested_token_use |обязательно | Указывает, как должен быть обработан запрос. Для потока On-Behalf-Of это значение должно быть равно **on_behalf_of**. |

### <a name="example"></a>Пример
Следующий запрос HTTP POST запрашивает токен доступа с областью `user.read` для веб-API https://graph.microsoft.com.

```
//line breaks for legibility only

POST /oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer
&client_id=2846f71b-a7a4-4987-bab3-760035b2f389
&client_secret=BYyVnAt56JpLwUcyo47XODd
&assertion=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6InowMzl6ZHNGdWl6cEJmQlZLMVRuMjVRSFlPMCJ9.eyJhdWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE0OTM5MjA5MTYsIm5iZiI6MTQ5MzkyMDkxNiwiZXhwIjoxNDkzOTI0ODE2LCJhaW8iOiJBU1FBMi84REFBQUFnZm8vNk9CR0NaaFV2NjJ6MFFYSEZKR0VVYUIwRUlIV3NhcGducndMMnVrPSIsIm5hbWUiOiJOYXZ5YSBDYW51bWFsbGEiLCJvaWQiOiJkNWU5NzljNy0zZDJkLTQyYWYtOGYzMC03MjdkZDRjMmQzODMiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJuYWNhbnVtYUBtaWNyb3NvZnQuY29tIiwic3ViIjoiZ1Q5a1FMN2hXRUpUUGg1OWJlX1l5dVZNRDFOTEdiREJFWFRhbEQzU3FZYyIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjN5U3F4UHJweUVPd0ZsTWFFMU1PQUEiLCJ2ZXIiOiIyLjAifQ.TPPJSvpNCSCyUeIiKQoLMixN1-M-Y5U0QxtxVkpepjyoWNG0i49YFAJC6ADdCs5nJXr6f-ozIRuaiPzy29yRUOdSz_8KqG42luCyC1c951HyeDgqUJSz91Ku150D9kP5B9-2R-jgCerD_VVuxXUdkuPFEl3VEADC_1qkGBiIg0AyLLbz7DTMp5DvmbC09DhrQQiouHQGFSk2TPmksqHm3-b3RgeNM1rJmpLThis2ZWBEIPx662pjxL6NJDmV08cPVIcGX4KkFo54Z3rfwiYg4YssiUc4w-w3NJUBQhnzfTl4_Mtq2d7cVlul9uDzras091vFy32tWkrpa970UvdVfQ
&scope=https://graph.microsoft.com/user.read
&requested_token_use=on_behalf_of
```

## <a name="service-to-service-access-token-response"></a>Ответ для токена доступа между службами
Если доступ предоставлен, ответ будет содержать JSON-файл OAuth 2.0 со следующими параметрами.

| Параметр | Описание |
| --- | --- |
| token_type |Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — **носитель**. Дополнительные сведения о токенах носителей см. в разделе [OAuth 2.0 Authorization Framework: использование токена носителя (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| scope |Область доступа, предоставляемая токеном. |
| expires_in |Срок действия доступа для токена (в секундах). |
| access_token |Запрашиваемый маркер доступа. Вызывающая служба может использовать этот токен для проверки подлинности принимающей службы. |
| refresh_token |Токен обновления для запрошенного токена доступа. Вызывающая служба может использовать этот токен для запроса другого токена доступа после того, как срок действия текущего токена доступа истек. |

### <a name="success-response-example"></a>Пример ответа с успешным предоставлением доступа
В следующем примере показано сообщение о предоставлении доступа в ответ на запрос токена доступа для веб-API https://graph.microsoft.com.

```
{
  "token_type": "Bearer",
  "scope": "https://graph.microsoft.com/user.read",
  "expires_in": 3269,
  "ext_expires_in": 0,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkQ0NDYy0tY0hGa18wZE50MVEtc2loVzRMd2RwQVZISGpnTVdQZ0tQeVJIaGlDbUN2NkdyMEpmYmRfY1RmMUFxU21TcFJkVXVydVJqX3Nqd0JoN211eHlBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMzA1LCJuYmYiOjE0OTM5MzAzMDUsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQU9KYnFFWlRNTnEyZFcxYXpKN1RZMDlYeDdOT29EMkJEUlRWMXJ3b2ZRc1k9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJWR1ItdmtEZlBFQ2M1dWFDaENRSkFBIiwidmVyIjoiMS4wIn0.cubh1L2VtruiiwF8ut1m9uNBmnUJeYx4x0G30F7CqSpzHj1Sv5DCgNZXyUz3pEiz77G8IfOF0_U5A_02k-xzwdYvtJUYGH3bFISzdqymiEGmdfCIRKl9KMeoo2llGv0ScCniIhr2U1yxTIkIpp092xcdaDt-2_2q_ql1Ha_HtjvTV1f9XR3t7_Id9bR5BqwVX5zPO7JMYDVhUZRx08eqZcC-F3wi0xd_5ND_mavMuxe2wrpF-EZviO3yg0QVRr59tE3AoWl8lSGpVc97vvRCnp4WVRk26jJhYXFPsdk4yWqOKZqzr3IFGyD08WizD_vPSrXcCPbZP3XWaoTUKZSNJg",
  "refresh_token": "OAQABAAAAAABnfiG-mA6NTae7CdWW7QfdAALzDWjw6qSn4GUDfxWzJDZ6lk9qRw4AnqPnvFqnzS3GiikHr5wBM1bV1YyjH3nUeIhKhqJWGwqJFRqs2sE_rqUfz7__3J92JDpi6gDdCZNNaXgreQsH89kLCVNYZeN6kGuFGZrjwxp1wS2JYc97E_3reXBxkHrA09K5aR-WsSKCEjf6WI23FhZMTLhk_ZKOe_nWvcvLj13FyvSrTMZV2cmzyCZDqEHtPVLJgSoASuQlD2NXrfmtcmgWfc3uJSrWLIDSn4FEmVDA63X6EikNp9cllH3Gp7Vzapjlnws1NQ1_Ff5QrmBHp_LKEIwfzVKnLLrQXN0EzP8f6AX6fdVTaeKzm7iw6nH0vkPRpUeLc3q_aNsPzqcTOnFfgng7t2CXUsMAGH5wclAyFCAwL_Cds7KnyDLL7kzOS5AVZ3Mqk2tsPlqopAiHijZaJumdTILDudwKYCFAMpUeUwEf9JmyFjl2eIWPmlbwU7cHKWNvuRCOYVqbsTTpJthwh4PvsL5ov5CawH_TaV8omG_tV6RkziHG9urk9yp2PH9gl7Cv9ATa3Vt3PJWUS8LszjRIAJmyw_EhgHBfYCvEZ8U9PYarvgqrtweLcnlO7BfnnXYEC18z_u5wemAzNBFUje2ttpGtRmRic4AzZ708tBHva2ePJWGX6pgQbiWF8esOrvWjfrrlfOvEn1h6YiBW291M022undMdXzum6t1Y1huwxHPHjCAA"
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
GET /v1.0/me HTTP/1.1
Host: graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkFRQUJBQUFBQUFCbmZpRy1tQTZOVGFlN0NkV1c3UWZkSzdNN0RyNXlvUUdLNmFEc19vdDF3cEQyZjNqRkxiNlVrcm9PcXA2cXBJclAxZVV0QktzMHEza29HN3RzXzJpSkYtQjY1UV8zVGgzSnktUHZsMjkxaFNBQSIsImFsZyI6IlJTMjU2IiwieDV0IjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIiwia2lkIjoiejAzOXpkc0Z1aXpwQmZCVksxVG4yNVFIWU8wIn0.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaWF0IjoxNDkzOTMwMDE2LCJuYmYiOjE0OTM5MzAwMTYsImV4cCI6MTQ5MzkzMzg3NSwiYWNyIjoiMCIsImFpbyI6IkFTUUEyLzhEQUFBQUlzQjN5ZUljNkZ1aEhkd1YxckoxS1dlbzJPckZOUUQwN2FENTVjUVRtems9IiwiYW1yIjpbInB3ZCJdLCJhcHBfZGlzcGxheW5hbWUiOiJUb2RvRG90bmV0T2JvIiwiYXBwaWQiOiIyODQ2ZjcxYi1hN2E0LTQ5ODctYmFiMy03NjAwMzViMmYzODkiLCJhcHBpZGFjciI6IjEiLCJmYW1pbHlfbmFtZSI6IkNhbnVtYWxsYSIsImdpdmVuX25hbWUiOiJOYXZ5YSIsImlwYWRkciI6IjE2Ny4yMjAuMC4xOTkiLCJuYW1lIjoiTmF2eWEgQ2FudW1hbGxhIiwib2lkIjoiZDVlOTc5YzctM2QyZC00MmFmLThmMzAtNzI3ZGQ0YzJkMzgzIiwib25wcmVtX3NpZCI6IlMtMS01LTIxLTIxMjc1MjExODQtMTYwNDAxMjkyMC0xODg3OTI3NTI3LTI2MTE4NDg0IiwicGxhdGYiOiIxNCIsInB1aWQiOiIxMDAzM0ZGRkEwNkQxN0M5Iiwic2NwIjoiVXNlci5SZWFkIiwic3ViIjoibWtMMHBiLXlpMXQ1ckRGd2JTZ1JvTWxrZE52b3UzSjNWNm84UFE3alVCRSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInVuaXF1ZV9uYW1lIjoibmFjYW51bWFAbWljcm9zb2Z0LmNvbSIsInVwbiI6Im5hY2FudW1hQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJzUVlVekYxdUVVS0NQS0dRTVFVRkFBIiwidmVyIjoiMS4wIn0.Hrn__RGi-HMAzYRyCqX3kBGb6OS7z7y49XPVPpwK_7rJ6nik9E4s6PNY4XkIamJYn7tphpmsHdfM9lQ1gqeeFvFGhweIACsNBWhJ9Nx4dvQnGRkqZ17KnF_wf_QLcyOrOWpUxdSD_oPKcPS-Qr5AFkjw0t7GOKLY-Xw3QLJhzeKmYuuOkmMDJDAl0eNDbH0HiCh3g189a176BfyaR0MgK8wrXI_6MTnFSVfBePqklQeLhcr50YTBfWg3Svgl6MuK_g1hOuaO-XpjUxpdv5dZ0SvI47fAuVDdpCE48igCX5VMj4KUVytDIf6T78aIXMkYHGgW3-xAmuSyYH_Fr0yVAQ
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о протоколе OAuth 2.0 и другом способе проверки подлинности между службами с использованием учетных данных клиента.
* [Предоставление учетных данных клиента OAuth 2.0 в Azure AD 2.0](active-directory-v2-protocols-oauth-client-creds.md)
* [OAuth 2.0 в Azure AD 2.0](active-directory-v2-protocols-oauth-code.md)

