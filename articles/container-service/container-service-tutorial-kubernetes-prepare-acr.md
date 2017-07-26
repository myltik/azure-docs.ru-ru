---
title: "Руководство по службе контейнеров Azure — подготовка ACR | Документация Майкрософт"
description: "Руководство по службе контейнеров Azure — подготовка ACR"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: ru-ru
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Развертывание реестра контейнеров Azure и его использование

Реестр контейнеров Azure (ACR) является частным реестром на базе Azure для образов контейнеров Docker. В этом руководстве рассматриваются основные шаги для развертывания экземпляра ACR и отправки в него образов контейнеров. В частности, здесь рассматриваются такие шаги:

> [!div class="checklist"]
> * Развертывание экземпляра реестра контейнеров Azure.
> * Добавление тегов в образы контейнеров для ACR.
> * Отправка образов в ACR.

В последующих руководствах данный экземпляр ACR интегрируется с кластером Kubernetes службы контейнеров Azure для безопасного выполнения образов контейнеров. 

## <a name="before-you-begin"></a>Перед началом работы

В [предыдущем руководстве](./container-service-tutorial-kubernetes-prepare-app.md) образы контейнеров создавались для простого приложения Azure для голосования. В этом руководстве эти образы помещаются в реестр контейнеров Azure. Если вы не создали образы приложения Azure для голосования, поработайте со статьей [Создание образов контейнеров для использования со Службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). Описанные здесь шаги подходят для любого образа контейнера.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="deploy-azure-container-registry"></a>Развертывание реестра контейнеров Azure

При развертывании реестра контейнеров Azure сначала необходимо создать группу ресурсов. Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). В этом примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Создайте реестр контейнеров Azure с помощью команды[az acr create](/cli/azure/acr#create). Имя контейнера реестра **должно быть уникальным**. В следующем примере добавьте в имя случайные символы.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>Получение сведений об ACR 

После создания экземпляра ACR требуется имя, имя сервера входа и пароль для проверки подлинности. Следующий код возвращает каждое из этих значений. Запишите все значения, так как они понадобятся дальше в этом руководстве.  

Имя ACR и сервер входа:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

Пароль ACR — замените именем ACR.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>Вход в реестр контейнеров

Войдите в свой экземпляр ACR, прежде чем отправлять в него образы. Используйте команду [docker login](https://docs.docker.com/engine/reference/commandline/login/), чтобы выполнить операцию. При выполнении команды docker login необходимо указать имя сервера входа ACR и учетные данные ACR.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

После выполнения эта команда возвращает сообщение Login Succeeded (Вход выполнен).

## <a name="tag-container-images"></a>Присвоение тегов образам контейнеров

Каждый образ контейнера должен быть помечен именем `loginServer` реестра. Данный тег используется для маршрутизации при отправке образов контейнеров в реестр образов.

Чтобы просмотреть список текущих образов, используйте команду `docker images`.

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

Пометьте образ *azure-vote-front* с помощью тега loginServer реестра контейнеров. Кроме того, добавьте `:v1` в конец имени образа. Этот тег указывает номер версии образа.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Повторите команду с образом *azure-vote-back*.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

После пометки выполните команду `docker images` для проверки операции.

```bash
docker images
```

Выходные данные:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>Отправка образов в ACR

Отправьте образ *azure-vote-front* в реестр. 

Используйте следующий пример, заменив в нем имя loginServer ACR именем loginServer своей среды. Для завершения операции требуется несколько минут.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Сделайте то же самое для образа *azure-vote-back*.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>Вывод списка образов в ACR 

Чтобы получить список образов, отправленных в реестр контейнеров Azure, выполните команду [az acr repository list](/cli/azure/acr/repository#list). Укажите в команде имя нужного экземпляра ACR.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

Выходные данные:

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

Чтобы увидеть теги для конкретного образа, используйте команду [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

Выходные данные:

```azurecli
Result
--------
v1
```

По завершении руководства образы двух контейнеров сохраняются в частном экземпляре реестра контейнеров Azure. Эти образы развертываются из ACR в кластер Kubernetes в последующих руководствах.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы подготовили реестр контейнеров Azure для использования в кластере Kubernetes ACS. Были выполнены следующие действия:

> [!div class="checklist"]
> * Развертывание экземпляра реестра контейнеров Azure.
> * Добавление тегов в образы контейнеров для ACR.
> * Отправка образов в ACR.

Перейдите к следующему руководству, чтобы ознакомиться с развертыванием кластера Kubernetes в Azure.

> [!div class="nextstepaction"]
> [Развертывание кластера Kubernetes в службе контейнеров Azure](./container-service-tutorial-kubernetes-deploy-cluster.md)
