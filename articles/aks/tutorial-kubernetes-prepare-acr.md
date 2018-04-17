---
title: Руководство по Kubernetes в Azure. Подготовка ACR
description: Руководство по AKS. Подготовка ACR
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6ef1e126001d40bd18acfd28efab48461e40c56e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Руководство. Развертывание Реестра контейнеров Azure и его использование

Реестр контейнеров Azure (ACR) является частным реестром на базе Azure для образов контейнеров Docker. В этом руководстве (вторая часть из восьми) рассматриваются основные шаги для развертывания экземпляра реестра контейнеров Azure и отправки в него образов контейнеров. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * развертывание экземпляра реестра контейнеров Azure (ACR);
> * добавление тегов к образу контейнера для ACR;
> * отправка образа в ACR.

В последующих руководствах этот экземпляр ACR интегрируется с кластером Kubernetes в AKS.

## <a name="before-you-begin"></a>Перед началом работы

В [предыдущей части руководства][aks-tutorial-prepare-app] мы создали образ контейнера для простого приложения Azure для голосования. Если вы еще не создали образ приложения Azure для голосования, выполните инструкции из статьи [Подготовка приложения для Службы контейнеров Azure (AKS)][aks-tutorial-prepare-app].

Для выполнения задач из этого руководства требуется Azure CLI 2.0.27 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create][az-group-create]. В этом примере создается группа ресурсов `myResourceGroup` в регионе `eastus`.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте реестр контейнеров Azure с помощью команды[az acr create][az-acr-create]. Имя реестра должно быть уникальным в пределах Azure и содержать от 5 до 50 буквенно-цифровых символов.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

В дальнейшем в этом руководстве будет использоваться `<acrName>` как заполнитель имени реестра контейнеров.

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Выполните команду [az acr login][az-acr-login], чтобы войти в экземпляр ACR. Укажите уникальное имя реестра контейнеров, заданное для него при создании.

```azurecli
az acr login --name <acrName>
```

После выполнения эта команда возвращает сообщение Login Succeeded (Вход выполнен).

## <a name="tag-container-images"></a>Присвоение тегов образам контейнеров

Чтобы просмотреть список сохраненных образов, используйте команду [docker images][docker-images].

```console
docker images
```

Выходные данные:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Каждый образ контейнера должен иметь тег с именем сервера входа (loginServer), указанным для реестра. Данный тег используется для маршрутизации при отправке образов контейнеров в реестр образов.

Чтобы получить имя loginServer, выполните команду [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Теперь добавьте к образу `azure-vote-front` тег loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег обозначает номер версии образа.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Добавив тег, выполните команду [docker images][docker-images] для проверки операции.

```console
docker images
```

Выходные данные:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Отправка образов в реестр

Отправьте образ `azure-vote-front` в реестр.

Используйте следующий пример, заменив в нем имя loginServer ACR именем loginServer своей среды.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Для завершения операции требуется несколько минут.

## <a name="list-images-in-registry"></a>Перечисление образов в реестре

Чтобы получить список образов, отправленных в реестр контейнеров Azure, используйте команду [az acr repository list][az-acr-repository-list]. Укажите в команде имя нужного экземпляра ACR.

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```azurecli
Result
----------------
azure-vote-front
```

Чтобы увидеть теги для конкретного образа, используйте команду [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Выходные данные:

```azurecli
Result
--------
v1
```

По завершении работы с этим руководством образ контейнера будет сохранен в частном экземпляре реестра контейнеров Azure. В следующих частях руководства мы развернем этот образ из ACR в кластер Kubernetes.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы подготовили реестр контейнеров Azure для использования в кластере AKS. Были выполнены следующие действия:

> [!div class="checklist"]
> * развертывание экземпляра реестра контейнеров Azure;
> * добавление тегов к образу контейнера для ACR;
> * отправка образа в ACR.

Перейдите к следующему руководству, чтобы ознакомиться с развертыванием кластера Kubernetes в Azure.

> [!div class="nextstepaction"]
> [Развертывание кластера Kubernetes][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-list]: /cli/azure/acr#list
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-list]: https://docs.microsoft.com/cli/azure/acr#az_acr_list
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
