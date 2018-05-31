---
title: Пример сценария Azure CLI для маршрутизации трафика для обеспечения высокого уровня доступности приложений | Документация Майкрософт
description: Пример сценария Azure CLI для маршрутизации трафика для обеспечения высокого уровня доступности приложений.
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
editor: tysonn
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: kumud
ms.openlocfilehash: 82264e51fcd64615a41f5fa652c4ad58ad9dabfb
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32313177"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Маршрутизация трафика для обеспечения высокого уровня доступности приложений с помощью Azure CLI

Этот скрипт создает группу ресурсов, два плана службы приложений, два веб-приложения, профиль и две конечные точки диспетчера трафика. Диспетчер трафика направляет трафик в приложение в основном регионе. Если оно недоступно, трафик направляется в дополнительный регион. Перед выполнением этого скрипта необходимо задать уникальные в Azure значения MyWebApp, MyWebAppL1 и MyWebAppL2. После выполнения вы сможете подключиться к приложению в основном регионе, используя URL-адрес mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример скрипта, вы можете удалить группу ресурсов, приложение службы приложений и все связанные ресурсы с помощью следующей команды.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, веб-приложения, профиля диспетчера трафика и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Заметки |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Создает план службы приложений. Это как ферма сервера для веб-приложения Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#az_appservice_web_create) | Создает веб-приложение Azure в плане службы приложений. |
| [az network traffic-manager profile create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Создает профиль диспетчера трафика Azure. |
| [az network traffic-manager endpoint create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) | Добавляет конечную точку в профиль диспетчера трафика Azure. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по сетям Azure](../cli-samples.md).
