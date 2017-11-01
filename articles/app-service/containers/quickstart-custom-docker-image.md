---
title: "Использование пользовательского образа Docker Hub для платформы \"Веб-приложения для контейнеров\" | Документация Майкрософт"
description: "Использование пользовательского образа Docker для платформы \"Веб-приложения для контейнеров\"."
keywords: "служба приложений azure, веб-приложение, docker, контейнер"
services: app-service
documentationcenter: 
author: naziml
manager: cfowler
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/05/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: eadc0f7eb20b9e8d1cacc79b2907559e2b2535a2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2017
---
# <a name="run-a-custom-docker-hub-image-in-web-app-for-containers"></a>Использование пользовательского образа Docker Hub для платформы "Веб-приложения для контейнеров"

Служба приложений включает предопределенные стеки приложений на платформе Linux с поддержкой определенных версий, включая PHP 7.0 и Node.js 4.5. Вы также можете использовать пользовательский образ Docker для развертывания веб-приложения в стек приложений, который еще не определен в Azure. В этом кратком руководстве показано, как создать веб-приложение и развернуть в нем образ Docker на основе Python. Создайте веб-приложение с помощью [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app-for-container"></a>Создание платформы "Веб-приложения для контейнеров"

Создайте [веб-приложение](../app-service-web-overview.md) в рамках плана `myAppServicePlan` службы приложений с помощью команды [az webapp create](/cli/azure/webapp#create). Не забудьте указать уникальное имя приложения вместо `<app name>`.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app name> --deployment-container-image-name elnably/dockerimagetest
```

В предыдущей команде `--deployment-container-image-name` указывает на общедоступный образ Docker Hub [https://hub.docker.com/r/elnably/dockerimagetest/](https://hub.docker.com/r/elnably/dockerimagetest/). Здесь можно просмотреть его содержимое: [https://github.com/rachelappel/docker-image](https://github.com/rachelappel/docker-image).

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

## <a name="browse-to-the-app"></a>Переход в приложение

Откройте веб-браузер и перейдите по следующему URL-адресу:

```bash
http://<app_name>.azurewebsites.net
```

![Пример приложения, выполняющегося в Azure](media/quickstart-custom-docker-image/hello-world-in-browser.png)

**Поздравляем!** Вы развернули пользовательский образ Docker для веб-приложения Azure для контейнеров.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL](tutorial-docker-python-postgresql-app.md)
