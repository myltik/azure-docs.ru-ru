---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure | Документация Майкрософт"
description: "Руководство по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: cc96ba9f5abd45a7503ba3327b30e1f809391384
ms.contentlocale: ru-ru
ms.lasthandoff: 08/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Развертывание реестра контейнеров Azure и его использование

Это вторая часть руководства, состоящего из трех частей. На [предыдущем шаге](./container-instances-tutorial-prepare-app.md) мы создали образ контейнера для простого веб-приложения, написанного на [Node.js](http://nodejs.org). Теперь мы поместим этот образ в реестр контейнеров Azure. Если вы еще не создали образ контейнера, вернитесь к первой части этой серии — [руководству по созданию образа контейнера](./container-instances-tutorial-prepare-app.md). 

Реестр контейнеров Azure — это частный реестр на базе Azure для образов контейнеров Docker. В этом руководстве описывается развертывание экземпляра реестра контейнеров Azure и отправка в него образа контейнера. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * Развертывание экземпляра реестра контейнеров Azure.
> * Добавление тегов к образу контейнера для помещения в реестр контейнеров Azure.
> * Отправка образа в реестр контейнеров Azure.

В последующих руководствах мы развернем контейнер из частного реестра в службе "Экземпляры контейнеров Azure".

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте реестр контейнеров Azure с помощью команды[az acr create](/cli/azure/acr#create). Имя контейнера реестра **должно быть уникальным**. В следующем примере мы используем имя *mycontainerregistry082*.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

В этом руководстве будет использоваться имя `<acrname>` как заполнитель выбранного имени реестра контейнеров.

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Войдите в свой экземпляр ACR, прежде чем отправлять в него образы. Используйте команду [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#login), чтобы выполнить операцию. Укажите уникальное имя реестра контейнеров, заданное для него при создании.

```azurecli
az acr login --name <acrName>
```

После выполнения эта команда возвращает сообщение Login Succeeded (Вход выполнен).

## <a name="tag-container-image"></a>Добавление тега к образу контейнера

Чтобы развернуть образ контейнера из частного реестра, необходимо добавить к этому образу тег с именем `loginServer` реестра.

Чтобы просмотреть список текущих образов, используйте команду `docker images`.

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Чтобы получить имя loginServer, выполните следующую команду.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Добавьте к образу *aci-tutorial-app* тег loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег указывает номер версии образа.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

После пометки выполните команду `docker images` для проверки операции.

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Передача образа в реестр контейнеров Azure

Передайте образ *aci-tutorial-app* в реестр.

Используйте следующий пример, заменив имя loginServer реестра контейнеров именем loginServer своей среды.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

## <a name="list-images-in-azure-container-registry"></a>Получение списка образов в реестре контейнеров Azure

Чтобы получить список образов, отправленных в реестр контейнеров Azure, выполните команду [az acr repository list](/cli/azure/acr/repository#list). Обновите команду, используя имя реестра контейнеров.

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```azurecli
Result
----------------
aci-tutorial-app
```

Чтобы увидеть теги для конкретного образа, используйте команду [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Выходные данные:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы подготовили реестр контейнеров Azure к использованию со службой "Экземпляры контейнеров Azure", а также передали образ контейнера. Были выполнены следующие действия:

> [!div class="checklist"]
> * Развертывание экземпляра реестра контейнеров Azure.
> * Добавление тегов к образу контейнера для помещения в реестр контейнеров Azure.
> * Отправка образа в реестр контейнеров Azure.

Перейдите к следующему руководству, чтобы узнать о развертывании контейнера в Azure с помощью службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Deploy a container to Azure Container Instances](./container-instances-tutorial-deploy-app.md) (Развертывание контейнера в службе "Экземпляры контейнеров Azure")

