---
title: Пример политики службы управления API Azure. Авторизации доступа на основе утверждений JWT | Документация Майкрософт
description: Пример политики управления API Azure. Авторизации доступа к определенным методам HTTP API на основе утверждений JWT.
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
ms.openlocfilehash: 08cb15ada07485f39ad24b782cfda29a431c46da
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933366"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Авторизация доступа на основе утверждений JWT

В этой статье на примере политики службы управления API Azure показано, как авторизовать доступ к определенным методам HTTP API на основе утверждений JWT. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

