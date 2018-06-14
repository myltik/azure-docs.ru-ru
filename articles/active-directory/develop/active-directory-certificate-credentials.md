---
title: Учетные данные сертификата в Azure AD | Документация Майкрософт
description: В этой статье рассматривается регистрация и использование учетных данных сертификата для аутентификации приложения.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c782429ac2d8ee030ca8b589b4241242c7b101d6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156506"
---
# <a name="certificate-credentials-for-application-authentication"></a>Учетные данные сертификата для аутентификации приложения

Azure Active Directory позволяет приложению использовать собственные учетные данные для аутентификации. Например, в потоке предоставления учетных данных клиента OAuth 2.0 ([версия 1](active-directory-protocols-oauth-service-to-service.md), [версия 2](active-directory-v2-protocols-oauth-client-creds.md)) и потоке On-Behalf-Of ([версия 1](active-directory-protocols-oauth-on-behalf-of.md), [версия 2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
Одной из форм учетных данных, которая может использоваться, является утверждение JSON Web Token (JWT), подписанное с помощью сертификата приложения.

## <a name="format-of-the-assertion"></a>Формат утверждения
Чтобы сформировать утверждение, воспользуйтесь одной из многих библиотек [JSON Web Token](https://jwt.ms/) на удобном для вас языке. Маркер содержит следующие сведения:

#### <a name="header"></a>Заголовок

| Параметр |  Комментарий |
| --- | --- |
| `alg` | Должен иметь значение **RS256** |
| `typ` | Должен иметь значение **JWT** |
| `x5t` | Это должен быть отпечаток SHA-1 сертификата X.509 |

#### <a name="claims-payload"></a>Утверждения (полезные данные)

| Параметр |  Комментарий |
| --- | --- |
| `aud` | Аудитория: должно быть значение **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Срок действия: дата, когда истекает срок действия маркера. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до истечения срока действия маркера.|
| `iss` | Издатель: параметр должен иметь значение client_id (идентификатор приложения службы клиента) |
| `jti` | GUID: идентификатор JWT |
| `nbf` | Не ранее: дата, до которой маркер не может использоваться. Время представлено как количество секунд с 1 января 1970 года (1970-01-01T0:0:0Z) в формате UTC до времени выдачи маркера. |
| `sub` | Субъект: параметр должен иметь значение client_id (идентификатор приложения службы клиента), как и `iss` |

#### <a name="signature"></a>Подпись

Подпись формируется через применение сертификата, как описано в [документе RFC7519 о спецификации JSON Web Token](https://tools.ietf.org/html/rfc7519).

### <a name="example-of-a-decoded-jwt-assertion"></a>Пример декодированного утверждения JWT

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>Пример закодированного утверждения JWT

Ниже приводится строка, которая является примером закодированного утверждения. Если внимательно ее изучить, то можно заметить, что она состоит из трех разделов, разделенных точками (.).
Первый раздел кодирует заголовок, второй — полезные данные, а последний является подписью, сформированной с помощью сертификатов на основе содержимого первых двух разделов.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Регистрация сертификат в Azure AD

Учетные данные сертификата можно связать с клиентским приложением в Azure AD через портал Azure с помощью любого из следующих методов:

**Отправка файла сертификата**

На странице регистрации приложения Azure для клиентского приложения последовательно выберите **Параметры**, **Ключи** и **Отправить открытый ключ**. Выберите файл сертификата, который требуется отправить, и щелкните **Сохранить**. После сохранения выполняется отправка сертификата и отображаются значения отпечатка сертификата, даты начала и истечения срока действия. 

**Обновление манифеста приложения**

Получив сертификат, необходимо вычислить следующие значения:

- `$base64Thumbprint` — хэш сертификата в кодировке base64;
- `$base64Value` — необработанные данные сертификата в кодировке base64.

Также необходимо предоставить идентификатор GUID для определения ключа в манифесте приложения (`$keyId`).

При регистрации клиентского приложения в Azure откройте манифест приложения и замените свойство *keyCredentials* данными нового сертификата, используя следующую схему:

```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Сохраните изменения в манифесте приложения и отправьте его в Azure AD. Свойство keyCredentials является многозначным, поэтому для расширенного управления ключами можно передать несколько сертификатов.
