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
ms.openlocfilehash: 9b0d37e4f7930389d3399e51de905db2b2ce8c27
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="generate-shared-access-signature"></a>Создание подписанного URL-адреса

В этой статье на примере политики службы управления API Azure показано, как создать [подписанный URL-адрес](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) с помощью выражений и перенаправить запрос в службу хранилища Azure с использованием политики rewrite-uri. См. дополнительные сведения о [создании и изменении кода политик](../set-edit-policies.md). Также для ознакомления доступны другие [примеры политик](../policy-samples.md).

## <a name="policy"></a>Политика

Вставьте код в блок **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Дополнительная информация

Подробнее о политиках службы управления API:

+ [Политики преобразования](../api-management-transformation-policies.md)
+ [Примеры политик](../policy-samples.md).

