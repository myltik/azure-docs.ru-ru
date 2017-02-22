---
title: "Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure | Документация Майкрософт"
description: "Узнайте, как обеспечить безопасный доступ к API-интерфейсам с помощью сертификатов клиентов"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b04ce0fe0db7649cebc7a1eeb2a35f1d53bf9636
ms.openlocfilehash: 1ce9f07c3e91eacdc74ccab3fbb7dc433a25c197

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Защита API-интерфейсов с помощью аутентификации на основе сертификата клиента в службе управления API Azure

Служба управления API помогает защитить доступ к API-интерфейсам (например, осуществляемый клиентом к службе управления API) с помощью сертификатов клиента. Сейчас можно проверить отпечаток сертификата клиента для сопоставления с требуемым значением. Можно также сопоставить отпечаток с существующими сертификатами, отправленными в службу управления API.  

См. дополнительные сведения о [защите серверных служб с помощью аутентификации на основе сертификата клиента](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates).

## <a name="checking-a-thumbprint-against-a-desired-value"></a>Проверка отпечатка на соответствие требуемому значению

Следующие политики можно настроить для проверки отпечатка сертификата клиента:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Проверка отпечатка на соответствие сертификатам, переданным в службу управления API

В примере ниже показано, как проверить отпечаток сертификата клиента на соответствие сертификатам, переданным в службу управления API. 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Дальнейшие действия

*  [Как защитить серверные службы с помощью аутентификации на основе сертификата клиента](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [Как передавать сертификаты](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)




<!--HONumber=Feb17_HO1-->


