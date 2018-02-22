---
title: "Развертывание приложения Go в Веб-приложении Azure для контейнеров | Документация Майкрософт"
description: "Как развернуть пользовательский образ Docker с запущенным приложением Go в Веб-приложении Azure для контейнеров."
keywords: azure app service, web app, go, docker, container
services: app-service
author: sptramer
manager: cfowler
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.devlang: go
ms.topic: quickstart
ms.date: 01/17/2018
ms.author: sttramer
ms.custom: mvc
ms.openlocfilehash: dbe4d7bc6f5f1d83a079993c9616206fd82a1b9d
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2018
---
# <a name="deploy-a-go-app-in-azure-web-app-for-containers"></a>Развертывание приложения Go в Веб-приложении Azure для контейнеров

Служба приложений включает предопределенные стеки приложений на платформе Linux с поддержкой определенных версий, включая PHP 7.0 и Node.js 4.5. Вы также можете использовать пользовательский образ Docker для запуска веб-приложения в стеке приложений, который еще не определен в Azure. В этом кратком руководстве показано, как использовать существующий контейнер Docker с приложением Go для его запуска в Службе приложений Azure. Создайте веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-the-container"></a>Создание Веб-приложения для контейнеров

Создайте [веб-приложение](../app-service-web-overview.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Не забудьте указать глобально уникальное имя приложения вместо `<app name>`.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name microsoft/appservice-go-quickstart
```

В предыдущей команде `--deployment-container-image-name` указывает на общедоступный образ Docker Hub [microsoft/appservice-go-quickstart](https://hub.docker.com/r/microsoft/appservice-go-quickstart).

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app name>.scm.azurewebsites.net/<app name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

## <a name="get-data-from-the-apps-endpoint"></a>Получение данных из конечной точки приложения

С помощью команды `curl` обратитесь к конечной точке REST API, предоставляемой приложением контейнера.

```bash
curl -X GET http://<app_name>.azurewebsites.net:8080/hello
```

```output
Hello world!
```

**Поздравляем!** Вы развернули пользовательский образ Docker в Веб-приложении Azure для контейнеров.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Использование пользовательского образа Docker](tutorial-custom-docker-image.md)
