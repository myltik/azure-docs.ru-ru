---
title: "Краткое руководство. Создание частного реестра Docker в Azure с помощью Azure CLI"
description: "Быстрый способ изучить создание частного реестра контейнеров Docker с помощью Azure CLI."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 12/07/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f31f4e5e2b3fe5db85873894a7f2fa9c415392c1
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Создание реестра контейнеров с помощью Azure CLI

Реестр контейнеров Azure — это управляемая служба реестра контейнеров Docker, используемая для хранения частных образов контейнеров Docker. В этом руководстве рассматривается создание экземпляра реестра контейнеров Azure с помощью Azure CLI.

Это краткое руководство требует, что вы используете Azure CLI версии 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить см. в разделе [установить CLI Azure 2.0][azure-cli].

Также необходим локально установленный модуль Docker. Docker содержит пакеты, которые легко настроить Docker для какого-либо [Mac][docker-mac], [Windows][docker-windows], или [Linux] [ docker-linux] системы.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Создание реестра контейнеров

В этом кратком руководстве мы создадим реестр уровня *Базовый*. Реестр контейнеров Azure доступен в нескольких номерах SKU, которые кратко описаны в следующей таблице. Расширенные сведения о каждом разделе [контейнер реестра SKU][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Создание экземпляров контроля доступа с помощью [создать az acr] [ az-acr-create] команды.

Имя реестра **должно быть уникальным**. В следующем примере используется имя *myContainerRegistry007*. Замените его уникальным значением.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
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
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

В дальнейшем в этом кратком руководстве будет использоваться `<acrName>` как заполнитель имени реестра контейнеров.

## <a name="log-in-to-acr"></a>Вход в ACR

Перед отправкой и извлечением образов контейнеров необходимо войти в экземпляр ACR. Чтобы сделать это, используйте [входа acr az] [ az-acr-login] команды.

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает сообщение `Login Succeeded`.

## <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. Если у вас еще нет локальной контейнера изображений, выполните следующую команду для извлечения существующий образ из Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Прежде чем можно отправить изображение в системный реестр, должна быть помечена с полным именем входа сервера контроля доступа. Выполните следующую команду для получения имени сервера полного имени входа экземпляра контроля доступа.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Тег изображения с помощью [тега docker] [ docker-tag] команды. Замените `<acrLoginServer>` с именем входа сервера, экземпляра контроля доступа.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Наконец, используйте [отправки в docker] [ docker-push] для принудительной отправки образа экземпляра контроля доступа. Замените `<acrLoginServer>` с именем входа сервера, экземпляра контроля доступа.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Список образов контейнеров

В следующем примере перечисляются репозитории в реестре:

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```bash
Result
----------------
aci-helloworld
```

В следующем примере перечисляются теги в репозитории **aci helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Выходные данные:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если больше не нужны, можно использовать [удаление группы az] [ az-group-delete] команду, чтобы удалить группу ресурсов, экземпляр контроля доступа и все образы контейнеров.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы создали реестр контейнеров Azure с помощью Azure CLI. Если вы хотите использовать реестр контейнеров Azure со службой "Экземпляры контейнеров Azure", перейдите к соответствующему руководству.

> [!div class="nextstepaction"]
> [Учебник по Azure экземпляры контейнером][container-instances-tutorial-prepare-app]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md