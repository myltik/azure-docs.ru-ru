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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: ru-ru
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>Обновление приложения в Kubernetes

После развертывания приложения в Kubernetes его можно обновить, указав новый образ контейнера или версию образа. При обновлении приложения выпуск обновления выполняется поэтапно, поэтому одновременно обновляется только часть развертывания. 

Поэтапное обновление позволяет приложению продолжать работать во время обновления и предоставляет механизм отката при возникновении сбоя развертывания. 

В этом руководстве обновляется пример приложения Azure для голосования. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Обновление кода внешнего приложения.
> * Создание обновленного образа контейнера.
> * Отправка образа контейнера в реестр контейнеров Azure.
> * Развертывание обновленного приложения.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах мы упаковывали приложение в образы контейнеров, отправляли образы в реестр контейнеров Azure и создавали кластер Kubernetes. Затем мы запустили приложение в кластере Kubernetes. 

Если вы еще не выполнили эти шаги и хотите попробовать сделать их сейчас, вернитесь к статье [Создание образов контейнеров для использования со Службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

Для этого руководства как минимум необходим кластер Kubernetes с выполняющимся приложением.

## <a name="update-application"></a>Обновление приложения

Для выполнения действий, описанных в этом руководстве, необходимо клонировать копию приложения Azure для голосования. При необходимости создайте клонированную копию с помощью следующей команды:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Откройте файл `config_file.cfg` в любом редакторе кода или текста. Этот файл можно найти в следующем каталоге клонированного репозитория.

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

Измените значения для `VOTE1VALUE` и `VOTE2VALUE`, а затем сохраните файл.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

Используйте `docker build` для повторного создания образа внешнего приложения.

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>Тестирование приложения

Создайте сеть Docker. Эта сеть используется для обмена данными между контейнерами.  

```bash
docker network create azure-vote
```

Запустите экземпляр образа контейнера серверной части с помощью команды `docker run`.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Запустите экземпляр образа контейнера интерфейсной части.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

Перейдите к `http://localhost:8080`, чтобы увидеть обновленное приложение. Инициализация приложения займет несколько секунд. Если возникает ошибка, повторите попытку.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Пометка и отправка образов

Пометьте образ *azure-vote-front* с помощью тега loginServer реестра контейнеров.

При использовании реестра контейнеров Azure получите имя сервера входа с помощью команды [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте для пометки образа команду [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). При этом укажите в команде имя сервера входа реестра контейнеров Azure или имя узла общедоступного реестра.

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

Отправьте образ в реестр. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure или именем узла общедоступного реестра.

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>Развертывание обновления в Kubernetes

### <a name="verify-multiple-pod-replicas"></a>Проверка нескольких реплик POD

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


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>Обновление приложения

Чтобы обновить приложение, выполните следующую команду. Обновите `<acrLoginServer>`, используя имя сервера входа или имя узла реестра контейнеров.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Для мониторинга развертывания используйте команду [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). По мере развертывания обновленного приложения ваши группы контейнеров прекращают работу и воссоздаются с новым образом контейнера.

```bash
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

```bash
kubectl get service
```

Перейдите по IP-адресу, чтобы увидеть обновленное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве мы обновили приложение и развернули это обновление в кластер Kubernetes. Мы выполнили следующие задачи:  

> [!div class="checklist"]
> * Обновили код внешнего приложения.
> * Создали обновленный образ контейнера.
> * Отправили образ контейнера в реестр контейнеров Azure.
> * Развернули обновленное приложение.

Перейдите к следующему руководству, чтобы узнать о мониторинге Kubernetes с помощью Operations Management Suite.

> [!div class="nextstepaction"]
> [Мониторинг кластера Kubernetes с помощью Operations Management Suite](./container-service-tutorial-kubernetes-monitor.md)
