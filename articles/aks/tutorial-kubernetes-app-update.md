---
title: "Руководство по Kubernetes в Azure. Обновление приложения"
description: "Руководство по AKS. Обновление приложения"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6de5173aedc836f7a2d56370ea8e54ad6e77ab5e
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="update-an-application-in-azure-container-service-aks"></a>Обновление приложения Службы контейнеров Azure (AKS)

После развертывания приложения в Kubernetes его можно обновить, указав новый образ контейнера или версию образа. При этом обновление выполняется поэтапно, поэтому одновременно обновляется только часть развертывания. Такое поэтапное обновление позволяет приложению продолжать работать во время обновления. Оно также обеспечивает механизм отката на случай, если произойдет сбой развертывания. 

В этом руководстве (часть шесть из восьми) обновляется пример приложения Vote Azure. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Обновление кода внешнего приложения.
> * Создание обновленного образа контейнера.
> * Отправка образа контейнера в реестр контейнеров Azure.
> * Развертывание обновленного образа контейнера.

В последующих руководствах среда Operations Management Suite настроена для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes. 

Также был клонирован репозиторий приложения, включая исходный код приложения и предварительно созданный файл Docker Compose, используемый в этом руководстве. Проверьте, создали ли вы клон репозитория и изменили ли каталоги на клонированный каталог. Внутри репозитория находится каталог `azure-vote` и файл `docker-compose.yaml`.

Если вы еще не выполнит эти действия и хотите попробовать, вернуться к [учебник 1 – Создание образов контейнеров][aks-tutorial-prepare-app]. 

## <a name="update-application"></a>Обновление приложения

Для задач этого руководства в приложение внесены изменения, и обновленное приложение развернуто в кластере Kubernetes. 

Исходный код приложения можно найти в каталоге `azure-vote`. Откройте файл `config_file.cfg` в любом редакторе кода или текста. В этом примере используется `vi` .

```console
vi azure-vote/azure-vote/config_file.cfg
```

Измените значения для `VOTE1VALUE` и `VOTE2VALUE`, а затем сохраните файл.

```console
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Сохраните и закройте файл.

## <a name="update-container-image"></a>Обновление образа контейнера

Используйте [составления docker] [ docker-compose] для повторного создания образа переднего плана и запуска обновленное приложение. Аргумент `--build` указывает Docker Compose, что требуется повторно создать образ приложения.

```console
docker-compose up --build -d
```

## <a name="test-application-locally"></a>Локальное тестирование приложения

Перейдите по адресу http://localhost:8080, чтобы просмотреть обновленное приложение.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Пометка и отправка образов

Добавьте к образу `azure-vote-front` тег loginServer реестра контейнеров. 

Получите имя сервера входа, выполнив команду [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Используйте [тега docker] [ docker-tag] для маркировки изображения. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure или именем узла общедоступного реестра. Также обратите внимание на то, что образ обновлен до версии `redis-v2`.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Используйте [отправки в docker] [ docker-push] передача изображения в системный реестр. Замените `<acrLoginServer>` именем сервера входа реестра контейнеров Azure.

```console
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Развертывание обновленного приложения

Чтобы обеспечить максимальное время доступности, должны быть запущены несколько экземпляров группы контейнеров приложения. Проверьте конфигурацию [kubectl получить pod] [ kubectl-get] команды.

```
kubectl get pod
```

Выходные данные:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Если у вас нет нескольких pod, выполняющих образ azure-vote-front, измените масштаб развертывания `azure-vote-front`.


```azurecli
kubectl scale --replicas=3 deployment/azure-vote-front
```

Чтобы обновить приложение, используйте [набор kubectl] [ kubectl-set] команды. Обновите `<acrLoginServer>`, используя имя сервера входа или имя узла реестра контейнеров.

```azurecli
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Для мониторинга развертывания, используйте [kubectl получить pod] [ kubectl-get] команды. По мере развертывания обновленного приложения ваши группы контейнеров прекращают работу и воссоздаются с новым образом контейнера.

```azurecli
kubectl get pod
```

Выходные данные:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Тестирование обновленного приложения

Получите внешний IP-адрес службы `azure-vote-front`.

```azurecli
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
> [Монитор Kubernetes с помощью аналитики журналов][aks-tutorial-monitor]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-monitor]: ./tutorial-kubernetes-monitor.md