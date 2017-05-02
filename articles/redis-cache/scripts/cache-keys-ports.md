---
title: "Пример сценария Azure CLI — получение имени узла, портов и ключей для кэша Redis для Azure | Документы Майкрософт"
description: "Пример сценария Azure CLI — получение имени узла, портов и ключей для кэша Redis для Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: cd9adc784bceb0fff5e7c2bbee2be0950c51c8f6
ms.lasthandoff: 04/15/2017

---

# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Получение имени узла, портов и ключей для кэша Redis для Azure

В этом сценарии вы узнаете, как получить имя узла, порты и ключи, используемые для подключения к экземпляру кэша Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Кэш Redis для Azure")]


## <a name="script-explanation"></a>Описание скрипта

Чтобы получить имя узла, порты и ключи для экземпляра кэша Redis для Azure, в этом сценарии используются приведенные ниже команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#show) | Извлекает сведения об экземпляре кэша Redis для Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys) | Извлекает ключи доступа для экземпляра кэша Redis для Azure. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).
