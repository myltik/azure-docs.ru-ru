---
title: 'Руководство по службе контейнеров Azure: обновление приложения'
description: 'Руководство по Службе контейнеров Azure: обновление приложения'
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f54179329b521cc861e90f023ff0b010b7ce1f75
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164968"
---
# <a name="update-an-application-in-kubernetes"></a>Обновление приложения в Kubernetes

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

После развертывания приложения в Kubernetes его можно обновить, указав новый образ контейнера или версию образа. При этом обновление выполняется поэтапно, поэтому одновременно обновляется только часть развертывания. Такое поэтапное обновление позволяет приложению продолжать работать во время обновления. Оно также обеспечивает механизм отката на случай, если произойдет сбой развертывания. 

В этом руководстве (часть 7 из 8) обновляется пример приложения Vote Azure. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Обновление кода внешнего приложения.
> * Создание обновленного образа контейнера.
> * Отправка образа контейнера в реестр контейнеров Azure.
> * Развертывание обновленного образа контейнера.

В последующих руководствах предполагается, что среда Log Analytics настроена для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes. 

Также был клонирован репозиторий приложения, включая исходный код приложения и предварительно созданный файл Docker Compose, используемый в этом руководстве. Проверьте, создали ли вы клон репозитория и изменили ли каталоги на клонированный каталог. Внутри репозитория находится каталог `azure-vote` и файл `docker-compose.yml`.

Если вы не выполнили эти действия и хотите продолжить изучение материала, вернитесь к разделу [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Обновление приложения

Для задач этого руководства в приложение внесены изменения, и обновленное приложение развернуто в кластере Kubernetes. 

Исходный код приложения можно найти в каталоге `azure-vote`. Откройте файл `config_file.cfg` в любом редакторе кода или текста. В этом примере используется `vi` .

```bash
vi azure-vote/azure-vote/config_file.cfg
```

Измените значения для `VOTE1VALUE` и `VOTE2VALUE`, а затем сохраните файл.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Сохраните и закройте файл.

## <a name="update-container-image"></a>Обновление образа контейнера

Используйте команду [docker-compose](https://docs.docker.com/compose/) для повторного создания образа внешнего приложения и запуска обновленного приложения. Аргумент `--build` указывает Docker Compose, что требуется повторно создать образ приложения.

```bash
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Локальное тестирование приложения

Перейдите к http://localhost:8080, чтобы увидеть обновленное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Пометка и отправка образов

Добавьте к образу `azure-vote-front` тег loginServer реестра контейнеров. 

Получите имя сервера входа, выполнив команду [az acr list](/cli/azure/acr#az_acr_list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте команду [docker tag](https://docs.docker.com/engine/reference/commandline/tag/), чтобы добавить тег для образа. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure или именем узла общедоступного реестра. Также обратите внимание на то, что образ обновлен до версии `redis-v2`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Используйте команду [docker push](https://docs.docker.com/engine/reference/commandline/push/), чтобы передать образ в реестр. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure.

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

Если у вас нет нескольких pod, выполняющих образ azure-vote-front, измените масштаб развертывания `azure-vote-front`.


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

Получите внешний IP-адрес службы `azure-vote-front`.

```azurecli-interactive
kubectl get service azure-vote-front
```

Перейдите по IP-адресу, чтобы увидеть обновленное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обновили приложение и развернули это обновление в кластер Kubernetes. Были выполнены следующие задачи:

> [!div class="checklist"]
> * Обновление кода внешнего приложения.
> * Создание обновленного образа контейнера.
> * Отправка образа контейнера в реестр контейнеров Azure.
> * Развертывание обновленного приложения.

Перейдите к следующему руководству, чтобы узнать о мониторинге Kubernetes с помощью Log Analytics.

> [!div class="nextstepaction"]
> [Мониторинг кластера Kubernetes с помощью Log Analytics](./container-service-tutorial-kubernetes-monitor.md)