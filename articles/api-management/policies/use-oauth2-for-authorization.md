---
title: Пример политики службы управления API Azure. Использование OAuth2 для авторизации между шлюзом и серверной частью | Документация Майкрософт
description: Пример политики службы управления API Azure. Использование OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть.
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
ms.openlocfilehash: 240f78bc66af681d0089c45229ab142adf515e60
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Использование OAuth2 для авторизации между шлюзом и серверной частью

В этой статье на примере политики службы управления API Azure показано, как использовать OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть. 

См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

Следующий сценарий использует свойства, которые отображаются в {{property}}. Чтобы узнать о свойствах и их использовании в политиках управления API, ознакомьтесь с [этим](../api-management-howto-properties.md) разделом.
 
## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

