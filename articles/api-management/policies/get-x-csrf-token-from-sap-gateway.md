---
title: "Пример политики службы управления API Azure. Реализация шаблона X-CSRF | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Реализация шаблона X-CSRF, который используется во многих интерфейсах API. Этот пример относится только к шлюзу SAP."
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
ms.openlocfilehash: a99ff25f7f8a3851936ba8d28e76eff98b1b0458
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="implement-x-csrf-pattern"></a>Реализация шаблона X-CSRF

В этой статье на примере политики службы управления API Azure показано, как реализовать шаблон X-CSRF, который используется во многих интерфейсах API. Этот пример относится только к шлюзу SAP. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

