---
title: "Пример скрипта Azure CLI. Удаление кэша Redis для Azure | Документы Майкрософт"
description: "Пример скрипта Azure CLI. Удаление кэша Redis для Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 59736b2f932efc13ece5c5e3b5db8708af0abe14
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
---
# <a name="delete-an-azure-redis-cache"></a>Удаление кэша Redis для Azure

В этом сценарии вы узнаете, как удалить кэш Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды для удаления экземпляра кэша Redis для Azure. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | Удаляет экземпляр кэша Redis. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).