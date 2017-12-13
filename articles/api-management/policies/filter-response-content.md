---
title: "Пример политики службы управления API Azure. Фильтрация содержимого ответа | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Фильтрация элементов данных из полезных данных ответа по продукту, связанному с запросом."
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
ms.openlocfilehash: 2acaad9e97f18cca22893c8c41ead4533ac5ae85
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="filter-response-content"></a>Фильтрация содержимого ответа

В этой статье на примере политики службы управления API Azure показано, как фильтровать элементы данных из полезных данных ответа по продукту, связанному с запросом. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md)

