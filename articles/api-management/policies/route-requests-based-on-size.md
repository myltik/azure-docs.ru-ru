---
title: "Пример политики службы управления API Azure. Маршрутизация запроса на основе размера его текста | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Маршрутизация запроса на основе размера его текста."
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
ms.openlocfilehash: 0558f82a31015af91df135b6a0d4c4050049d11d
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Маршрутизация запроса на основе размера его текста

В этой статье на примере политики службы управления API Azure показано, как настроить маршрутизацию запроса на основе размера его текста. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

