---
title: "Пример сценария Azure CLI — получение имени узла, портов и ключей для кэша Redis для Azure | Документы Майкрософт"
description: "Пример сценария Azure CLI — получение имени узла, портов и ключей для кэша Redis для Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 6cfff74ed037835abb6d3e3975fe33d71b9730a3
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Получение имени узла, портов и ключей для кэша Redis для Azure

В этом сценарии вы узнаете, как получить имя узла, порты и ключи, используемые для подключения к экземпляру кэша Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Описание скрипта

Чтобы получить имя узла, порты и ключи для экземпляра кэша Redis для Azure, в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Извлекает сведения об экземпляре кэша Redis для Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Извлекает ключи доступа для экземпляра кэша Redis для Azure. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).