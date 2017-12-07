---
title: "Краткое руководство. Создание частного реестра Docker в Azure с помощью Azure CLI"
description: "Быстрый способ изучить создание частного реестра контейнеров Docker с помощью Azure CLI."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 5cddf0ffea256ed6d1c51d48a61ac8176d08b9cc
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Создание реестра контейнеров с помощью Azure CLI

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра реестра контейнеров Azure с помощью Azure CLI.

Для этого руководства требуется Azure CLI 2.0.20 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Также необходим локально установленный модуль Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом кратком руководстве мы создадим реестр уровня *Базовый*. Реестр контейнеров Azure доступен в нескольких номерах SKU, которые кратко описаны в следующей таблице. См. дополнительные сведения о [номерах SKU реестра контейнеров](container-registry-skus.md).

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Создайте экземпляр ACR с помощью команды [az acr create](/cli/azure/acr#create).

Имя реестра **должно быть уникальным**. В следующем примере используется имя *myContainerRegistry007*. Замените его уникальным значением.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

При создании реестра выходные данные выглядят так:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

В дальнейшем в этом кратком руководстве будет использоваться `<acrname>` как заполнитель имени реестра контейнеров.

## <a name="log-in-to-acr"></a>Вход в ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Чтобы сделать это, используйте команду [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

После выполнения эта команда возвращает сообщение Login Succeeded (Вход выполнен).

## <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. При необходимости выполните следующую команду, чтобы извлечь предварительно созданный образ из Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Образу должен быть присвоен тег имени сервера для входа в ACR. Выполните следующую команду, чтобы получить имя сервера входа для экземпляра ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Присвойте образу тег с помощью команды [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Замените значение *<acrLoginServer>* именем сервера входа для вашего экземпляра ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Наконец, воспользуйтесь командой [docker push](https://docs.docker.com/engine/reference/commandline/push/) для отправки образа в экземпляр ACR. Замените значение *<acrLoginServer>* именем сервера входа для вашего экземпляра ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Список образов контейнеров

В следующем примере перечисляются репозитории в реестре:

```azurecli
az acr repository list -n <acrname> -o table
```

Выходные данные:

```bash
Result
----------------
aci-helloworld
```

В следующем примере перечисляются теги в репозитории **aci helloworld**.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Выходные данные:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Ненужные группу ресурсов, экземпляр ACR и все образы контейнеров можно удалить с помощью команды [az group delete](/cli/azure/group#delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали реестр контейнеров Azure с помощью Azure CLI. Если вы хотите использовать реестр контейнеров Azure со службой "Экземпляры контейнеров Azure", перейдите к соответствующему руководству.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"](../container-instances/container-instances-tutorial-prepare-app.md)