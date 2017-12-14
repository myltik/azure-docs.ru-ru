---
title: "Пример политики службы управления API Azure. Создание подписанного URL-адреса | Документация Майкрософт"
description: "Пример политики службы управления API Azure. Создание подписанного URL-адреса с помощью выражений и перенаправление запроса в службу хранилища Azure с использованием политики rewrite-uri."
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
ms.openlocfilehash: e1f17f9f4e17a3eebb55e4ec1905aec19a2165a5
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
---
# <a name="generate-shared-access-signature"></a>Создание подписанного URL-адреса

В этой статье на примере политики службы управления API Azure показано, как создать [подписанный URL-адрес](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) с помощью выражений и перенаправить запрос в службу хранилища Azure с использованием политики rewrite-uri. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md)

