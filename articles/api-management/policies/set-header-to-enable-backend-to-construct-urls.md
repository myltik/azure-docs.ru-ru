---
title: Пример политики службы управления API Azure. Добавление заголовка перенаправления | Документация Майкрософт
description: Пример политики службы управления API Azure. Способы добавления заголовка перенаправления во входящий запрос для обеспечения создания серверным API правильных URL-адресов.
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
ms.openlocfilehash: 00c8ac567b476d0591069c83c371d987d651de9d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="add-a-forwarded-header"></a>Добавление заголовка перенаправления

В этой статье на примере политики службы управления API Azure показано, как добавить заголовок перенаправления во входящий запрос, чтобы обеспечить создание серверным API правильных URL-адресов. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="code"></a>Код

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).
