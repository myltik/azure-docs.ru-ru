---
title: "Создание статического веб-приложения HTML в Azure за пять минут | Документация Майкрософт"
description: "Узнайте, как можно быстро запускать веб-приложения в службе приложений, развернув пример приложения."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 472353a2d099db869f43649cd46c8b004ebd53cc
ms.lasthandoff: 03/18/2017


---
# <a name="create-a-static-html-web-app-in-azure-in-five-minutes"></a>Создание статического веб-приложения HTML в Azure за пять минут
[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Это краткое руководство поможет развернуть простой сайт HTML+CSS в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) за считаные минуты.

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
Создайте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Бесплатный". 

```azurecli
az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
```

## <a name="create-a-web-app"></a>Создание веб-приложения
Создайте веб-приложение, указав уникальное имя вместо заполнителя `<app_name>`.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
```

## <a name="deploy-sample-application"></a>Разверните пример приложения
Разверните пример HTML-сайта из GitHub.

```azurecli
az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
--repo-url "https://github.com/Azure-Samples/app-service-web-html-get-started.git" --branch master --manual-integration 
```

## <a name="browse-to-web-app"></a>Перейдите к веб-приложению
Чтобы увидеть работу приложения в Azure в реальном времени, выполните следующую команду:

```azurecli
az appservice web browse --name <app_name> --resource-group myResourceGroup
```

Поздравляем, ваш первый статический HTML-сайт работает в службе приложений Azure в режиме реального времени.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о предварительно созданных [скриптах интерфейса командной строки для веб-приложений](app-service-cli-samples.md).

