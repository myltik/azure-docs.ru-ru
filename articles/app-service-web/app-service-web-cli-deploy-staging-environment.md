---
title: "Пример скрипта Azure CLI. Развертывание веб-приложения в промежуточной среде | Документация Майкрософт"
description: "Пример скрипта Azure CLI. Развертывание веб-приложения в промежуточной среде"
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
ms.openlocfilehash: 6e24be1f9525b976864d63dc433e853f399a545d
ms.lasthandoff: 02/27/2017

---

# <a name="deploy-a-web-app-to-a-staging-environment"></a>Развертывание веб-приложения в промежуточной среде

С помощью Azure CLI 2.0 этот пример скрипта выполняет следующие действия: 

* создает веб-приложение в службе приложений Azure в регионе Azure "Западная Европа";
* обновляет план службы приложения до уровня "Стандартный" (минимум для слотов развертывания);
* настраивает слот развертывания с именем staging;
* развертывает код веб-приложения из GitHub в промежуточном слоте;
* открывает развернутый промежуточной слот в браузере для проверки;
* переключает промежуточный слот в рабочую среду.

## <a name="prerequisites"></a>Предварительные требования

* Чтобы войти в Azure, выполните команду `az login`.
* Поместите код веб-приложения в собственный репозиторий GitHub.

> [!NOTE]
> Если вы используете чужой общедоступный репозиторий GitHub, служба приложений развернет код из него, но не сможет настроить ключ SSH и объекты webhook, необходимые для непрерывного развертывания.
>
>

## <a name="create-vm-sample"></a>Создание примера виртуальной машины

[!code-azurecli[main](../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Развертывание веб-приложения в промежуточной среде")]

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
| [az appservice plan update](https://docs.microsoft.com/en-us/cli/azure/appservice/plan#update) | Обновляет план службы приложений для масштабирования ценовой категории. |
| [az appservice web deployment slot create](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#create) | Создает слот развертывания. |
| [az appservice web source-control config](https://docs.microsoft.com/en-us/cli/azure/appservice/web/source-control#config) | Связывает веб-приложения с репозиторием Git или Mercurial. |
| [az appservice web browse](https://docs.microsoft.com/en-us/cli/azure/appservice/web#browse) | Открывает веб-приложение в браузере. |
| [az appservice web deployment slot swap](https://docs.microsoft.com/en-us/cli/azure/appservice/web/deployment/slot#swap) | Переключает указанный слот развертывания в рабочую среду. |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об Azure CLI см. в [документации по Azure CLI](https://docs.microsoft.com/en-us/cli/azure/overview).

Дополнительные примеры скриптов Azure CLI для веб-приложений службы приложений можно найти [здесь]().

