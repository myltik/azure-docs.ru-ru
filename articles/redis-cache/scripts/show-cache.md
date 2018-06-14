---
title: Пример сценария Azure CLI. Получение сведений о кэше Redis для Azure | Документация Майкрософт
description: Пример сценария Azure CLI. Получение сведений о кэше Redis для Azure
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 00ae8e00d456d4660cadf645d4521173d254bbd7
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846253"
---
# <a name="get-details-of-an-azure-redis-cache"></a>Получение сведений о кэше Redis для Azure

В этом сценарии вы узнаете, как получить сведения об экземпляре кэша Redis для Azure, включая его состояние подготовки.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Описание скрипта

Чтобы получить сведения об экземпляре кэша Redis для Azure, в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Извлекает сведения об экземпляре кэша Redis для Azure. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).