---
title: "Создание первого веб-приложения ASP.NET в Azure за пять минут | Документация Майкрософт"
description: "Убедитесь, как просто запускать веб-приложения в службе приложений, развернув пример приложения ASP.NET."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: c55f844b89729b1ef5a6316b0b2731472074219e
ms.lasthandoff: 03/15/2017


---
# <a name="create-your-first-aspnet-web-app-in-azure-in-five-minutes"></a>Создание первого веб-приложения ASP.NET в Azure за пять минут

[!INCLUDE [app-service-web-selector-get-started](../../includes/app-service-web-selector-get-started.md)] 

Это краткое руководство поможет вам развернуть первое веб-приложение ASP.NET в [службе приложений Azure](../app-service/app-service-value-prop-what-is.md) за считаные минуты.

Прежде чем начать работу с этим руководством, убедитесь, что на вашем компьютере [установлен Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

## <a name="create-an-aspnet-web-app-in-azure"></a>Создание веб-приложения ASP.NET в Azure
2. Войдите в Azure, выполнив команду `az login`, и следуйте инструкциям на экране.
   
    ```azurecli
    az login
    ```
   
3. Создайте [группу ресурсов](../azure-resource-manager/resource-group-overview.md). Здесь будут размещаться все ресурсы Azure, которыми вы хотите управлять совместно, например веб-приложение и его серверная часть базы данных SQL.

    ```azurecli
    az group create --location "West Europe" --name myResourceGroup
    ```

    Чтобы увидеть доступные значения для `---location`, используйте команду `az appservice list-locations` Azure CLI.

3. Создайте [план службы приложений](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) уровня "Бесплатный". 

    ```azurecli
    az appservice plan create --name my-free-appservice-plan --resource-group myResourceGroup --sku FREE
    ```

4. Создайте веб-приложение с уникальным именем в `<app_name>`.

    ```azurecli
    az appservice web create --name <app_name> --resource-group myResourceGroup --plan my-free-appservice-plan
    ```

4. Разверните пример приложения ASP.NET из GitHub.

    ```azurecli
    az appservice web source-control config --name <app_name> --resource-group myResourceGroup \
    --repo-url "https://github.com/Azure-Samples/app-service-web-dotnet-get-started.git" --branch master --manual-integration 
    ```

5. Чтобы увидеть работу приложения в Azure в реальном времени, выполните следующую команду:

    ```azurecli
    az appservice web browse --name <app_name> --resource-group myResourceGroup
    ```

Поздравляем, ваше первое веб-приложение ASP.NET работает в службе приложений Azure в режиме реального времени.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. сведения о предварительно созданных [скриптах интерфейса командной строки для веб-приложений](app-service-cli-samples.md).

