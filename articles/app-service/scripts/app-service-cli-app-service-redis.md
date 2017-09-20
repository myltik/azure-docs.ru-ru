---
title: "Пример скрипта Azure CLI. Подключение веб-приложения к кэшу Redis | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Подключение веб-приложения к кэшу Redis"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: bc8345b2-8487-40c6-a91f-77414e8688e6
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/30/2017
ms.author: cfowler
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: c1277a04e3a63c09b2ed7f9304bfc482ab9189a8
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---

# <a name="connect-a-web-app-to-a-redis-cache"></a>Подключение веб-приложения к кэшу Redis

Из этой статьи вы узнаете, как создать кэш Redis для Azure и веб-приложение Azure. Затем вы свяжете базу кэш Redis с веб-приложением, используя параметры приложения.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/connect-to-redis/connect-to-redis.sh "Кэш Redis для Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, веб-приложения, кэша Redis и всех связанных ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#az_appservice_plan_create) | Создает план службы приложений. Это как ферма сервера для веб-приложения Azure. |
| [az webapp create](https://docs.microsoft.com/cli/azure/webapp#az_webapp_create) | Создает веб-приложение Azure. |
| [az redis create](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_create) | Создает экземпляр кэша Redis. Здесь будут храниться данные. |
| [az redis list-keys](https://docs.microsoft.com/en-us/cli/azure/redis#az_redis_list_keys) | Создает список ключей доступа для экземпляра кэша Redis. |
| [az webapp config appsettings set](https://docs.microsoft.com/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) | Создает или обновляет параметр приложения для веб-приложения Azure. Параметры приложения представляются в качестве переменных среды для приложения. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).

