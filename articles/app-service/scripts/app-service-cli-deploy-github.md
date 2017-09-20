---
title: "Пример скрипта Azure CLI. Создание веб-приложения с развертыванием из GitHub | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание веб-приложения с развертыванием из GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: sample
ms.topic: sample
ms.date: 06/19/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 1b97faa1216b5dc0e80a743d69bdf94fa6a9c58d
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---
# <a name="create-a-web-app-with-deployment-from-github"></a>Создание веб-приложения с развертыванием из GitHub

Этот пример скрипта создает веб-приложение в службе приложений со связанными ресурсами, а затем развертывает код веб-приложения из открытого репозитория GitHub (без непрерывного развертывания). Дополнительные сведения о непрерывном развертывании на GitHub см. в статье [Создание веб-приложения с непрерывным развертыванием из GitHub](app-service-cli-continuous-deployment-github.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Создание веб-приложения с развертыванием из GitHub")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта 

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Создает план службы приложений. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Создает веб-приложение Azure. |
| [az webapp deployment source config](https://docs.microsoft.com/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Связывает веб-приложение Azure с репозиторием Git или Mercurial. |
| [az webapp browse](https://docs.microsoft.com/cli/azure/webapp#az_webapp_browse) | Открывает веб-приложение Azure в браузере. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).

