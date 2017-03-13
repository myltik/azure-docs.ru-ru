---
title: "Пример скрипта Azure CLI. Создание веб-приложения и развертывание кода из локального репозитория Git | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание веб-приложения и развертывание кода из локального репозитория Git"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 048f98aa-f708-44cb-9b9e-953f67dc6da8
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 09851d3f2b3cf33015e508aec6cdae613a747038
ms.openlocfilehash: b10da867c4ab43053019313956b09635917781e4
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Создание веб-приложения и развертывание кода из локального репозитория Git

Этот пример скрипта создает веб-приложение со связанными ресурсами в службе приложений, а затем развертывает код веб-приложения в локальном репозитории Git.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`, а также что в локальном репозитории Git фиксируется код приложения.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh?highlight=3-5 "Создание веб-приложения и развертывание кода из локального репозитория Git")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, приложение службы приложений и все связанные ресурсы, использовав следующую команду.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az appservice web create](https://docs.microsoft.com/cli/azure/appservice/web#delete) | Создает веб-приложение Azure. |
| [az appservice web deployment user set](https://docs.microsoft.com/cli/azure/appservice/web/deployment/user#set) | Настраивает учетные данные развертывания на уровне учетной записи для службы приложений. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config-local-git) | Создает конфигурацию системы управления версиями для локального репозитория Git. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Открывает веб-приложение Azure в браузере. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
