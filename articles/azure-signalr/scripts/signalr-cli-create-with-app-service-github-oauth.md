---
title: Пример скрипта для Azure CLI. Создание веб-приложения с аутентификацией через службу SignalR и GitHub | Документация Майкрософт
description: Пример скрипта для Azure CLI. Создание веб-приложения с аутентификацией через службу SignalR и GitHub
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
ms.date: 04/22/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: ec095572dc8fe20c913d543ffcf926355b5715df
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-web-app-that-uses-signalr-service-and-github-authentication"></a>Создание веб-приложения с аутентификацией через службу SignalR и GitHub

Этот пример скрипта создает новый ресурс службы Azure SignalR, который используется для принудительной отправки обновлений клиентам в режиме реального времени. Также скрипт добавляет новое веб-приложение и план службы приложений, чтобы разместить веб-приложение ASP.NET Core, которое использует эту службу SignalR. Для веб-приложения указываются настройки, позволяющие подключиться к созданному ресурсу службы SignalR и применить [аутентификацию GitHub](https://developer.github.com/v3/guides/basics-of-authentication/). Также в веб-приложении настраивается локальный репозиторий Git в качестве источника развертывания.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

Этот скрипт использует расширение *signalr* для Azure CLI. Выполните следующую команду, чтобы установить расширение *signalr* для Azure CLI, прежде чем использовать скрипт из этого примера:

```azurecli-interactive
az extension add -n signalr
```

[!code-azurecli-interactive[main](../../../cli_scripts/azure-signalr/create-signalr-with-app-service-github-oauth/create-signalr-with-app-service-github-oauth.sh "Create a new SignalR Service and Web App configured to use SignalR, GitHub OAuth, and local Git repository deployment source.")]

Запишите фактическое имя, созданное для новой группы ресурсов. Оно отобразится в выходных данных. Это имя группы ресурсов вам понадобится для удаления групп ресурсов.

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для каждой команды в таблице приведены ссылки на соответствующую документацию. Этот сценарий использует следующие команды:

| Get-Help | Заметки |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az signalr create](/cli/azure/signalr#az-signalr-create) | Создает ресурс службы Azure SignalR. |
| [az signalr key list](/cli/azure/signalr/key#az-signalr-key-list) | Выводит список ключей, которые будут использоваться приложением для принудительной отправки обновлений через SignalR в режиме реального времени. |
| [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) | Создает план службы приложений Azure для размещения веб-приложений. |
| [az webapp create](/cli/azure/webapp#az-webapp-create) | Создает веб-приложение Azure в плане размещения службы приложений. |
| [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) | Добавляет новые параметры приложения для веб-приложения. Эти параметры приложения используются для хранения строки подключения SignalR и секретов GitHub OAuth для приложения. |
| [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) | Обновляет учетные данные для развертывания. |
| [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) | Получает URL-адрес конечной точки репозитория Git, который позволяет клонировать и обновлять развертывание веб-приложения. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](/cli/azure).

Дополнительные примеры скриптов CLI для службы Azure SignalR см. в [документации по службе Azure SignalR](../signalr-cli-samples.md).
