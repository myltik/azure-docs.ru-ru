---
title: Пример политики службы управления API Azure. Авторизации доступа с помощью токена OAuth Google | Документация Майкрософт
description: Пример политики службы управления API Azure. Авторизация доступа к конечным точкам, используя Google в качестве поставщика токена OAuth.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 46ffe21b31597f889b411a40a9f3e1f2dd76f1ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933468"
---
# <a name="authorize-access-using-google-oauth-token"></a>Авторизации доступа с помощью токена OAuth Google

В этой статье на примере политики службы управления API Azure показано, как авторизовать доступ к конечным точкам, используя Google в качестве поставщика токена OAuth. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

