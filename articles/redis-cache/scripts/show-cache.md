---
title: "Пример сценария Azure CLI. Получение сведений о кэше Redis для Azure | Документация Майкрософт"
description: "Пример сценария Azure CLI. Получение сведений о кэше Redis для Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 9f4eb32227bd8a68837eabd58b9d058bc4995d17
ms.lasthandoff: 04/15/2017

---

# <a name="get-details-of-an-azure-redis-cache"></a>Получение сведений о кэше Redis для Azure

В этом сценарии вы узнаете, как получить сведения об экземпляре кэша Redis для Azure, включая его состояние подготовки.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Кэш Redis для Azure")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы получить сведения об экземпляре кэша Redis для Azure, в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#show) | Извлекает сведения об экземпляре кэша Redis для Azure. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).
