---
title: "Пример политики службы управления API Azure. Отправка ошибок в Stackify для записи в журнал | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Добавление политики регистрации ошибок для отправки ошибок в Stackify и их записи в журнал."
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
ms.openlocfilehash: 34319e43789d0f2c7d3e48a0277926eb598e88fc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="send-errors-to-stackify-for-logging"></a>Отправка ошибок Stackify для записи в журнал

В этой статье на примере политики службы управления API Azure показано, как добавить политику регистрации ошибок для отправки ошибок в Stackify и их записи в журнал. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

