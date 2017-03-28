---
title: "Пример скрипта Azure CLI. Создание веб-приложения ASP.NET Core в контейнере Docker из реестра контейнеров Azure | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание веб-приложения ASP.NET Core в контейнере Docker из реестра контейнеров Azure"
services: appservice
documentationcenter: appservice
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 3a2d1983-ff7b-476a-ac44-49ec2aabb31a
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/23/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 711aaed5521625ee4c4250ad7a2aa6f8b1d6666f
ms.lasthandoff: 03/11/2017

---

# <a name="create-an-aspnet-core-web-app-in-a-docker-container-from-azure-container-registry"></a>Создание веб-приложения ASP.NET Core в контейнере Docker из реестра контейнеров Azure

В этом сценарии вы узнаете, как создать группу ресурсов, план службы приложений Linux и веб-приложение, а также как развернуть приложение ASP.NET Core, используя контейнер Docker из реестра контейнеров Azure.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`.

## <a name="create-app-sample"></a>Создание примера приложения

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-linux-acr/deploy-linux-acr.sh?highlight=6-9 "Реестр контейнеров Azure в Linux")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, веб-приложений и всех связанных с ними ресурсов этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. Это как ферма сервера для веб-приложения Azure. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#create) | Создает веб-приложение Azure в плане службы приложений. |
| [az appservice web config container update](https://docs.microsoft.com/cli/azure/appservice/web/config/container#update) | Настраивает контейнер Docker для веб-приложения Azure. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
