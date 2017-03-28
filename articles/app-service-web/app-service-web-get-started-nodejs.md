---
title: "Создание первого веб-приложения Node.js в Azure за пять минут | Документация Майкрософт"
description: "Убедитесь, как просто запускать веб-приложения в службе приложений, развернув пример приложения Node.js."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 412cc786-5bf3-4e1b-b696-6a08cf46501e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 89a9e29261e338aceb4ff6feb55cf344afeeb3d4
ms.lasthandoff: 03/21/2017


---
# <a name="create-your-first-nodejs-web-app-in-azure-in-five-minutes"></a>Создание первого веб-приложения Node.js в Azure за пять минут
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Это краткое руководство поможет вам развернуть первое веб-приложение Node.js в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) за считаные минуты.

Перед началом работы убедитесь, что вы установили Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Вход в Azure
Войдите в Azure, выполнив команду `az login`, и следуйте инструкциям на экране.
   
```azurecli
az login
```
   
## <a name="create-a-resource-group"></a>Создание группы ресурсов   
Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md). Здесь будут размещаться все ресурсы Azure, которыми вы хотите управлять совместно, например веб-приложение и его серверная часть базы данных SQL.

```azurecli
az group create --location "West Europe" --name myResourceGroup
```

Чтобы увидеть доступные значения для `---location`, используйте команду `az appservice list-locations` Azure CLI.

## <a name="create-an-app-service-plan"></a>Создание плана службы приложений
Создайте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Стандартный" для запуска контейнера Linux. 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --is-linux --sku S1
```

## <a name="create-a-web-app"></a>Создание веб-приложения
Создайте веб-приложение, указав уникальное имя вместо заполнителя `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="configure-the-linux-container"></a>Настройка контейнера Linux
Настройте для контейнера Linux использование образа Node.js 6.9.3 по умолчанию.

```azurecli
az appservice web config update --node-version 6.9.3 --name <app_name> --resource-group myResourceGroup
```

## <a name="deploy-sample-application"></a>Разверните пример приложения
Разверните пример приложения Node.js из GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-nodejs-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Перейдите к веб-приложению
Чтобы увидеть работу приложения в Azure в реальном времени, выполните следующую команду:

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Поздравляем, ваше первое веб-приложение Node.js работает в службе приложений Azure в режиме реального времени.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о предварительно созданных [скриптах интерфейса командной строки для веб-приложений](app-service-cli-samples.md).

