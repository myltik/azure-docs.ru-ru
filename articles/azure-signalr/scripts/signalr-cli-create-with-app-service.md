---
title: Пример скрипта Azure CLI. Создание службы SignalR с использованием Службы приложений Azure | Документация Майкрософт
description: Пример скрипта Azure CLI. Создание службы SignalR с использованием Службы приложений Azure
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
ms.openlocfilehash: 6ac1646da4c952c78bfb787b0d6ab30f4876f36a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33766399"
---
# <a name="create-a-signalr-service-with-an-app-service"></a>Создание службы SignalR с использованием Службы приложений Azure

В этом примере скрипта создается новый ресурс службы Azure SignalR, который используется для принудительной передачи обновлений клиентам в режиме реального времени. Также с помощью скрипта добавляется новое веб-приложение и план службы приложений, чтобы разместить веб-приложение ASP.NET Core, которое использует эту службу SignalR. Веб-приложение настраивается с использованием параметра приложения с именем *AzureSignalRConnectionString* для подключения к новому ресурсу службы SignalR.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

В скрипте используется расширение *signalr* для Azure CLI. Выполните следующую команду, чтобы установить расширение *signalr* для Azure CLI, прежде чем использовать скрипт из этого примера:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service/create-signalr-with-app-service.sh "Create a new Azure SignalR Service and Web App")]

Запишите фактическое имя, созданное для новой группы ресурсов. Оно будет отображаться в выходных данных. Это имя группы ресурсов вам понадобится для удаления групп ресурсов.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Создание ресурса службы Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Перечисление ключей, которые будут использоваться приложением для принудительной передачи обновлений через SignalR в режиме реального времени. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Создание плана Службы приложений Azure для размещения веб-приложений. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Создание веб-приложения Azure в плане размещения Службы приложений Azure. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Добавление нового параметра приложения для веб-приложения. Этот параметр приложения используется для хранения строки подключения SignalR. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов CLI для службы Azure SignalR см. в [документации по службе Azure SignalR](../signalr-cli-samples.md).
