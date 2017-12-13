---
title: "Пример политики службы управления API Azure. Использование OAuth2 для авторизации между шлюзом и серверной частью | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Использование OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть."
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
ms.openlocfilehash: e0aeec66f23033f916c782c8a895e725b0735b62
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Использование OAuth2 для авторизации между шлюзом и серверной частью

В этой статье на примере политики службы управления API Azure показано, как использовать OAuth2 для авторизации между шлюзом и серверной частью. В статье показано, как получить маркер доступа из AAD и перенаправить его в серверную часть. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md)

