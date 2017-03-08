---
title: "Пример скрипта Azure CLI. Развертывание веб-приложения из локального репозитория Git | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Развертывание веб-приложения из локального репозитория Git"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 343f7509e43c2305e6fa7d5e5cb9e6772e1a7e01
ms.openlocfilehash: 7c970e6d4447fed96d91143a502ec823eb60df62
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-from-a-local-git-repository"></a>Развертывание веб-приложения из локального репозитория Git

С помощью Azure CLI 2.0 этот пример скрипта выполняет следующие действия: 

* создает веб-приложение в службе приложений Azure в регионе Azure "Западная Европа";
* развертывает веб-приложение из локального репозитория Git;
* отображает развернутое веб-приложение в браузере.

## <a name="prerequisites"></a>Предварительные требования

* Чтобы войти в Azure, выполните команду `az login`.
* Зафиксируйте код веб-приложения в локальном репозитории Git.

## <a name="create-vm-sample"></a>Создание примера виртуальной машины

[!code-azurecli[main](../../cli_scripts/app-service/deploy-local-git/deploy-local-git.sh "Развертывание веб-приложения из локального репозитория Git")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

После выполнения примера скрипта можно удалить группу ресурсов, веб-приложение и все связанные ресурсы, использовав следующую команду.

```azurecli
az group delete --name $webappname
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [az group create](https://docs.microsoft.com/en-us/cli/azure/group#create) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [az appservice plan create](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#create) | Создает план службы приложений. |
| [az appservice web create](https://docs.microsoft.com/en-us/cli/azure/appservice/web#delete) | Создает веб-приложение. |
| [az appservice web source-control config-local-git](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config-local-git) | Создает конфигурацию системы управления версиями для локального репозитория Git. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Открывает веб-приложение в браузере. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/en-us/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для веб-приложений службы приложений можно найти [здесь]().
