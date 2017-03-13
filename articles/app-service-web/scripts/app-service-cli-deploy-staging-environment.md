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
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 15a811d35aab9995f4895a9106771310a95f7a41
ms.openlocfilehash: 6ef9b43fb5fb88591186b20b52dd8f06b7d46cec
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Создание веб-приложения и развертывание кода в промежуточной среде

Этот пример скрипта создает веб-приложение в службе приложений с дополнительным слотом развертывания под названием staging, а затем развертывает в нем пример приложения.

Перед выполнением этого скрипта убедитесь, что установлено подключение к Azure, выполнив команду `az login`. 

Этот пример работает в оболочке Bash. Сведения о параметрах выполнения скриптов Azure CLI в клиенте Windows см. в статье [Использование Azure CLI в Windows](../../virtual-machines/virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Пример скрипта

[!code-azurecli[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Создание веб-приложения и развертывание кода в промежуточной среде")]

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
| [az appservice web deployment slot create](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#create) | Создает слот развертывания. |
| [az appservice web source-control config](https://docs.microsoft.com/cli/azure/appservice/web/source-control#config) | Связывает веб-приложение Azure с репозиторием Git или Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/cli/azure/appservice/web#browse) | Открывает веб-приложение Azure в браузере. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/cli/azure/appservice/web/deployment/slot#swap) | Переключает указанный слот развертывания в рабочую среду. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для службы приложений см. в [документации по службе приложений Azure](../app-service-cli-samples.md).
