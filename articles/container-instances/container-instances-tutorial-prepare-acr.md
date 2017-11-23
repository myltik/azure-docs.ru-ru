---
title: "Руководство по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure"
description: "Руководство по службе \"Экземпляры контейнеров Azure\". Подготовка реестра контейнеров Azure"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: mmacy
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0beadcca2247fb64c03835c4cb1e1e4fb9426d6f
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Развертывание реестра контейнеров Azure и его использование

Это вторая часть руководства, состоящего из трех частей. На [предыдущем шаге](container-instances-tutorial-prepare-app.md) мы создали образ контейнера для простого веб-приложения, написанного на [Node.js](http://nodejs.org). В этом руководстве мы поместим образ в реестр контейнеров Azure. Если вы еще не создали образ контейнера, вернитесь к первой части этой серии — [руководству по созданию образа контейнера](container-instances-tutorial-prepare-app.md).

Реестр контейнеров Azure — это частный реестр на базе Azure для образов контейнеров Docker. В этом руководстве описывается развертывание экземпляра реестра контейнеров Azure и отправка в него образа контейнера. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * Развертывание экземпляра реестра контейнеров Azure.
> * Добавление тегов к образу контейнера для помещения в реестр контейнеров Azure.
> * Отправка образа в реестр контейнеров Azure.

В последующих руководствах мы развернем контейнер из частного реестра в службе "Экземпляры контейнеров Azure".

## <a name="before-you-begin"></a>Перед началом работы

Для этого руководства требуется Azure CLI 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Для работы с этим руководством требуется среда разработки Docker. Docker содержит пакеты, которые позволяют быстро настроить Docker в любой системе [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) или [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell не включает в себя компоненты Docker, необходимые для выполнения каждого шага этого руководства. Таким образом, мы рекомендуем выполнить локальную установку среды разработки Azure CLI и Docker.

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте реестр контейнеров Azure с помощью команды[az acr create](/cli/azure/acr#create). Имя реестра контейнеров **должно быть уникальным** в пределах Azure и содержать от 5 до 50 буквенно-цифровых знаков. Замените `<acrName>` уникальным именем реестра.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Например, чтобы создать реестр контейнеров Azure *mycontainerregistry082*, выполните следующую команду.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

В этом руководстве будет использоваться имя `<acrName>` как заполнитель выбранного имени реестра контейнеров.

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Войдите в свой экземпляр ACR, прежде чем отправлять в него образы. Используйте команду [az acr login](/cli/azure/acr#az_acr_login), чтобы выполнить операцию. Укажите уникальное имя реестра контейнеров, заданное при его создании.

```azurecli
az acr login --name <acrName>
```

По завершении команда возвращает сообщение `Login Succeeded`.

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

Чтобы получить имя loginServer, выполните следующую команду. Замените `<acrName>` именем реестра контейнеров.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Выходные данные примера:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

Добавьте к образу *aci-tutorial-app* тег loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег указывает номер версии образа. Замените `<acrLoginServer>` результатом только что выполненной команды `az acr show`.

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

Отправьте образ *aci-tutorial-app* в реестр командой `docker push`. Замените `<acrLoginServer>` полным именем сервера входа, полученным на предыдущем шаге.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Операция `push` займет от нескольких секунд до нескольких минут, в зависимости от подключения к Интернету. Ее выходные данные будут иметь следующий вид.

```bash
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
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
> * развертывание экземпляра реестра контейнеров Azure;
> * добавление тегов к образу контейнера для помещения в реестр контейнеров Azure;
> * отправка образа в реестр контейнеров Azure.

Перейдите к следующему руководству, чтобы узнать о развертывании контейнера в Azure с помощью службы "Экземпляры контейнеров Azure".

> [!div class="nextstepaction"]
> [Deploy a container to Azure Container Instances](./container-instances-tutorial-deploy-app.md) (Развертывание контейнера в службе "Экземпляры контейнеров Azure")
