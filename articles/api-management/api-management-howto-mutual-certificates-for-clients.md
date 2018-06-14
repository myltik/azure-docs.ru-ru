---
title: Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure | Документация Майкрософт
description: Узнайте, как обеспечить безопасный доступ к API-интерфейсам с помощью сертификатов клиентов
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 841825923819bdb257e5b5983071d999cca805e9
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
ms.locfileid: "26406749"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure

Служба управления API помогает защитить доступ к API-интерфейсам (например, осуществляемый клиентом к службе управления API) с помощью сертификатов клиента. Сейчас можно проверить отпечаток сертификата клиента для сопоставления с требуемым значением. Можно также сопоставить отпечаток с существующими сертификатами, отправленными в службу управления API.  

Сведения о защите серверных служб API с помощью сертификата клиента (например, управления API для серверной части) см. в статье [Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates).

## <a name="checking-the-expiration-date"></a>Проверка даты окончания срока действия

Следующие политики можно настроить для проверки даты окончания срока действия сертификата:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>Проверка издателя и субъекта

Следующие политики можно настроить для проверки издателя и субъекта сертификата клиента:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Проверка отпечатка

Следующие политики можно настроить для проверки отпечатка сертификата клиента:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Проверка отпечатка на соответствие сертификатам, переданным в службу управления API

В примере ниже показано, как проверить отпечаток сертификата клиента на соответствие сертификатам, переданным в службу управления API. 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Дальнейшие действия

*  [Как защитить серверные службы с помощью аутентификации на основе сертификата клиента](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Как передавать сертификаты](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

