---
title: "Пример сценария Azure CLI. Создание кэша Redis для Azure | Документация Майкрософт"
description: "Пример сценария Azure CLI. Создание кэша Redis для Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: afd7f6e0-9297-4c98-a95e-597be939cef7
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 19b582a0cecd72b9f30283ccf822b74cf400a155
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---

# <a name="create-an-azure-redis-cache"></a>Создание кэша Redis для Azure

С помощью этого сценария вы узнаете, как создать кэш Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Кэш Redis для Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов и кэша Redis этот сценарий использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Создает экземпляр кэша Redis. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).
