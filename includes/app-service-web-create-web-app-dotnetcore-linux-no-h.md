---
title: включение файла
description: включение файла
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 521a99f486fb76200767e43585702572aae099c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
Создайте [веб-приложение](../articles/app-service/containers/app-service-linux-intro.md) в плане службы приложений `myAppServicePlan`. 

В Cloud Shell можно использовать команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). В следующем примере замените `<app_name>`глобальным уникальным именем приложения (допустимые символы: `a-z`, `0-9` и `-`). Для среды выполнения установлено значение `dotnetcore|1.1`. Список всех поддерживаемых сред выполнения можно получить с помощью команды [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "dotnetcore|1.1" --deployment-local-git
```

Когда веб-приложение будет создано, в Azure CLI отобразится примерно следующее:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Вы создали пустое веб-приложение в контейнере Linux со включенным развертыванием Git.

> [!NOTE]
> URL-адрес удаленного репозитория Git отображается в свойстве `deploymentLocalGitUrl` в формате `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Сохраните этот URL-адрес для дальнейшего использования.
>
