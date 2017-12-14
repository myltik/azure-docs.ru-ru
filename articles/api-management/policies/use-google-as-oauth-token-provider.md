---
title: "Пример политики службы управления API Azure. Авторизации доступа с помощью токена OAuth Google | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Авторизация доступа к конечным точкам, используя Google в качестве поставщика токена OAuth."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 3889c6fcaef0845c05e455ad173c6880ef053892
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="authorize-access-using-google-oauth-token"></a>Авторизации доступа с помощью токена OAuth Google

В этой статье на примере политики службы управления API Azure показано, как авторизовать доступ к конечным точкам, используя Google в качестве поставщика токена OAuth. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md)

