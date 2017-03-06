---
title: "Пример скрипта Azure CLI. Создание веб-приложения и развертывание кода из GitHub | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Создание веб-приложения и развертывание кода из GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0205c991-0989-4ca3-bb41-237dcc964460
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 9d30f8a40606cb1f5830905c6d172709800feeec
ms.openlocfilehash: e20e35b01ba9bdd723776ea3d990041a83d5cdcf
ms.lasthandoff: 02/27/2017

---


# <a name="create-a-web-app-and-deploy-code-from-github"></a>Создание веб-приложения и развертывание кода из GitHub

Этот пример скрипта создает веб-приложение в службе приложений со связанными ресурсами, а затем развертывает код веб-приложения из открытого репозитория GitHub (без непрерывного развертывания). Дополнительные сведения о непрерывном развертывании на GitHub см. в статье [Создание веб-приложения с непрерывным развертыванием из GitHub](app-service-cli-continuous-deployment-github.md).

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`, а также что у вас есть URL-адрес репозитория GitHub для развертывания.

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="create-app-sample"></a>Создание примера приложения

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-github/deploy-github.sh?highlight=3 "Создание веб-приложения и развертывание кода из GitHub")]

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
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Связывает веб-приложение Azure с репозиторием Git или Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Открывает веб-приложение Azure в браузере. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
