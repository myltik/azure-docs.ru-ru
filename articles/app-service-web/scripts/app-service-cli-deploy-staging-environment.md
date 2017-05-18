---
title: "Пример скрипта Azure CLI. Создание веб-приложения и развертывание кода в промежуточной среде | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание веб-приложения и развертывание кода в промежуточной среде"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.service: app-service-web
ms.workload: web
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 6ee3008ab00fcdca3d1eb8330237bed196fad813
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017

---

# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Создание веб-приложения и развертывание кода в промежуточной среде

Этот пример скрипта создает веб-приложение в службе приложений с дополнительным слотом развертывания под названием staging, а затем развертывает в нем пример приложения.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Создание веб-приложения и развертывание кода в промежуточной среде")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Создает веб-приложение Azure. |
| [az appservice web deployment slot create](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#create) | Создает слот развертывания. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Связывает веб-приложение Azure с репозиторием Git или Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Открывает веб-приложение Azure в браузере. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#swap) | Переключает указанный слот развертывания в рабочую среду. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).

