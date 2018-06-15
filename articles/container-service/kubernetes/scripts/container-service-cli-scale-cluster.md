---
title: Пример скрипта Azure CLI. Масштабирование кластера ACS | Документы Майкрософт
description: Пример скрипта Azure CLI. Масштабирование кластера ACS
services: container-service
documentationcenter: ''
author: neilpeterson
manager: jeconnoc
editor: ''
tags: acs, azure-container-service
keywords: Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure
ms.assetid: ''
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: nepeters
ms.openlocfilehash: 2daff49e90213b2fadc0f75e0209f668a94d3dea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32161928"
---
# <a name="scale-an-azure-container-service-cluster"></a>Масштабирование кластера службы контейнеров Azure

В этом примере выполняется масштабирование в службе контейнеров Azure. 

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

```azurecli
az acs scale --resource-group myResourceGroup --name myK8SCluster --new-agent-count 5
```

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [az acs scale](/cli/azure/acs#az_acs_scale) | Масштабирование кластера ACS. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов CLI службы контейнеров Azure см. в [документации по службе контейнеров Azure](../cli-samples.md).

