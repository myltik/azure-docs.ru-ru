---
title: "Руководство по Службе контейнеров Azure: обновление приложения | Документация Майкрософт"
description: "Руководство по Службе контейнеров Azure: обновление приложения"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 72cdbfe2fe65e5152ca748cbb3989e3ef980ee50
ms.contentlocale: ru-ru
ms.lasthandoff: 07/25/2017

---

# <a name="update-an-application-in-kubernetes"></a>Обновление приложения в Kubernetes

После развертывания приложения в Kubernetes его можно обновить, указав новый образ контейнера или версию образа. При обновлении приложения выпуск обновления выполняется поэтапно, поэтому одновременно обновляется только часть развертывания. Поэтапное обновление позволяет приложению продолжать работать во время обновления и предоставляет механизм отката при возникновении сбоя развертывания. 

В этом руководстве (часть 7 из 8) обновляется пример приложения Vote Azure. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Обновление кода внешнего приложения.
> * Создание обновленного образа контейнера.
> * Отправка образа контейнера в реестр контейнеров Azure.
> * Развертывание обновленного образа контейнера.

В последующих руководствах среда Operations Management Suite настроена для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes. 

Если вы не выполнили эти действия и хотите продолжить изучение материала, вернитесь к разделу [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Обновление приложения

Для выполнения действий, описанных в этом руководстве, необходимо клонировать копию приложения Azure для голосования. При необходимости создайте клонированную копию с помощью следующей команды:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Откройте файл `config_file.cfg` в любом редакторе кода или текста. Этот файл можно найти в следующем каталоге клонированного репозитория.

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

Измените значения для `VOTE1VALUE` и `VOTE2VALUE`, а затем сохраните файл.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Используйте команду [docker-compose](https://docs.docker.com/compose/) для повторного создания образа внешнего приложения и запуска обновленного приложения.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yaml up --build -d
```

## <a name="test-application-locally"></a>Локальное тестирование приложения

Перейдите к `http://localhost:8080`, чтобы увидеть обновленное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Пометка и отправка образов

Пометьте образ *azure-vote-front* с помощью тега loginServer реестра контейнеров.

При использовании реестра контейнеров Azure получите имя сервера входа с помощью команды [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте команду [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), чтобы добавить тег для образа. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure или именем узла общедоступного реестра.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Используйте команду [docker push](https://docs.docker.com/engine/reference/commandline/push/), чтобы передать образ в реестр. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure или именем узла общедоступного реестра.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Развертывание обновленного приложения

Чтобы обеспечить максимальное время доступности, должны быть запущены несколько экземпляров группы контейнеров приложения. Проверьте эту конфигурацию с помощью команды [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Выходные данные:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Если у вас нет нескольких групп контейнеров, выполняющих образ azure-vote-front, измените масштаб развертывания *azure-vote-front*.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Чтобы обновить приложение, используйте команду [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set). Обновите `<acrLoginServer>`, используя имя сервера входа или имя узла реестра контейнеров.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Для мониторинга развертывания используйте команду [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). По мере развертывания обновленного приложения ваши группы контейнеров прекращают работу и воссоздаются с новым образом контейнера.

```azurecli-interactive
kubectl get pod
```

Выходные данные:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Тестирование обновленного приложения

Получите внешний IP-адрес службы *azure-vote-front*.

```azurecli-interactive
kubectl get service azure-vote-front
```

Перейдите по IP-адресу, чтобы увидеть обновленное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы обновили приложение и развернули это обновление в кластер Kubernetes. Были выполнены следующие задачи:

> [!div class="checklist"]
> * Обновление кода внешнего приложения.
> * Создание обновленного образа контейнера.
> * Отправка образа контейнера в реестр контейнеров Azure.
> * Развертывание обновленного приложения.

Перейдите к следующему руководству, чтобы узнать о мониторинге Kubernetes с помощью Operations Management Suite.

> [!div class="nextstepaction"]
> [Мониторинг кластера Kubernetes с помощью Operations Management Suite](./container-service-tutorial-kubernetes-monitor.md)
