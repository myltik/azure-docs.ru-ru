---
title: "Пример скрипта Azure CLI. Удаление кэша Redis для Azure | Документы Майкрософт"
description: "Пример скрипта Azure CLI. Удаление кэша Redis для Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: f959823b3a7c5b0262f693ecad1e6efc4eec4f35
ms.contentlocale: ru-ru
ms.lasthandoff: 04/15/2017

---

# <a name="delete-an-azure-redis-cache"></a>Удаление кэша Redis для Azure

В этом сценарии вы узнаете, как удалить кэш Redis для Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Кэш Redis для Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

В этом сценарии используются следующие команды для удаления экземпляра кэша Redis для Azure. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis#delete) | Удаляет экземпляр кэша Redis. |


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры сценариев Azure CLI для кэша Redis для Azure см. в [документации по кэшу Redis для Azure](../cli-samples.md).
