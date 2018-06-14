---
title: Пример скрипта Azure CLI. Создание службы SignalR | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание службы SignalR
services: signalr
documentationcenter: signalr
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: a8df62fe229a627b2e551ab04528ad601c5d5d2a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766433"
---
# <a name="create-a-signalr-service"></a>Создание службы SignalR 

В этом примере скрипта создается ресурс службы Azure SignalR со случайным именем в новой группе ресурсов.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

В скрипте используется расширение *signalr* для Azure CLI. Выполните следующую команду, чтобы установить расширение *signalr* для Azure CLI, прежде чем использовать скрипт из этого примера:

```azurecli-interactive
az extension add -n signalr
```

С помощью этого скрипта создается ресурс службы SignalR и группа ресурсов. 

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-service-and-group/create-signalr-service-and-group.sh "Creates a new Azure SignalR Service resource and resource group")]

Запишите фактическое имя, созданное для новой группы ресурсов. Это имя группы ресурсов вам понадобится для удаления групп ресурсов.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az signalr create](/cli/azure/group#az-group-create) | Создание ресурса службы Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Перечисление ключей, которые будут использоваться приложением для принудительной передачи обновлений через SignalR в режиме реального времени. |


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов CLI для службы Azure SignalR см. в [документации по службе Azure SignalR](../signalr-cli-samples.md).
