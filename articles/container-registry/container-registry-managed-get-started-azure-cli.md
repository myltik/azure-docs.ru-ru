---
title: "Создание частного реестра контейнеров Docker с помощью Azure CLI | Документация Майкрософт"
description: "Приступите к созданию частных реестров контейнеров Docker и управлению ими с помощью Azure CLI 2.0"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Создание управляемого реестра контейнеров с помощью Azure CLI

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра управляемого реестра контейнеров Azure с помощью Azure CLI.

Управляемые реестры контейнеров Azure находятся на стадии предварительной версии и не доступны во всех регионах.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

Создайте экземпляр ACR с помощью команды [az acr create](/cli/azure/acr#create).

> [!NOTE]
> При создании реестра укажите глобально уникальное доменное имя верхнего уровня, содержащее только буквы и цифры.

 Имя реестра в примере — *myContainerRegistry1*, подставьте собственное уникальное имя.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

При создании реестра выходные данные выглядят так:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Вход в экземпляр ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Чтобы сделать это, используйте команду [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

После выполнения эта команда возвращает сообщение Login Succeeded (Вход выполнен).

## <a name="use-azure-container-registry"></a>Использование реестра контейнеров Azure

### <a name="list-container-images"></a>Список образов контейнеров

Используйте команду `az acr` интерфейса командной строки, чтобы запросить образы и теги в репозитории.

> [!NOTE]
> В настоящее время реестр контейнеров не поддерживает команду `docker search`, используемую для запроса образов и тегов.

### <a name="list-repositories"></a>Вывод списка репозиториев

Следующий пример выводит список репозиториев реестра в формате JSON (нотация объектов JavaScript).

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Вывод списка тегов

Следующий пример выводит список тегов репозитория **samples/nginx** в формате JSON.

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали управляемый экземпляр реестра контейнеров Azure с помощью Azure CLI.

> [!div class="nextstepaction"]
> [Отправка первого образа с помощью интерфейса командной строки Docker](container-registry-get-started-docker-cli.md)

