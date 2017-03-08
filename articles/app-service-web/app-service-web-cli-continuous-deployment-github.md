---
title: "Пример скрипта Azure CLI. Непрерывное развертывание веб-приложения из GitHub | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Непрерывное развертывание веб-приложения из GitHub"
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
ms.sourcegitcommit: d19b07667bc2d34f860be46c299560fe9a788d53
ms.openlocfilehash: 649521d6f3740b099ab41c8ef70f7e4430aa5de9
ms.lasthandoff: 02/27/2017

---

# <a name="continuously-deploy-web-app-from-github"></a>Непрерывное развертывание веб-приложения из GitHub

С помощью Azure CLI 2.0 этот пример скрипта выполняет следующие действия: 

* создает веб-приложение в службе приложений Azure в регионе Azure "Западная Европа"; 
* развертывает код веб-приложения из GitHub;
* отображает развернутое веб-приложение в браузере.

## <a name="prerequisites"></a>Предварительные требования

* Чтобы войти в Azure, выполните команду `az login`.
* Поместите код веб-приложения в собственный репозиторий GitHub.

> [!NOTE]
> Если вы используете чужой общедоступный репозиторий GitHub, служба приложений развернет код из него, но не сможет настроить ключ SSH и объекты webhook, необходимые для непрерывного развертывания.
>
>

## <a name="create-vm-sample"></a>Создание примера виртуальной машины

[!code-azurecli[main](../../cli_scripts/app-service/deploy-github/deploy-github.sh "Непрерывное развертывание веб-приложения из GitHub")]

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
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Связывает веб-приложения с репозиторием Git или Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Открывает веб-приложение в браузере. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/en-us/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для веб-приложений службы приложений можно найти [здесь]().

