---
title: "Руководство по Kubernertes в Azure. Масштабирование приложения | Документация Майкрософт"
description: "Руководство по AKS. Масштабирование приложения"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: c60bdf18f80f6922631e02855b83adeb876daa4c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2017
---
# <a name="scale-application-in-azure-container-service-aks"></a>Масштабирование приложения Службы контейнеров Azure (AKS)

Если вы выполнили инструкции в руководствах, то у вас имеется работающий кластер Kubernetes в AKS и вы развернули в нем приложение Vote Azure.

В этом руководстве (часть пять из восьми) описывается масштабирование pod, содержащихся в приложении, и их автомасштабирование. Вы также узнаете, как масштабировать количество узлов виртуальной машины Azure, чтобы менять емкость кластера для размещения рабочих нагрузок. Вам предстоят следующие задачи:

> [!div class="checklist"]
> * Масштабирование узлов Kubernetes Azure
> * масштабирование pod Kubernetes вручную;
> * настройка автомасштабирования pod, в которых выполняется интерфейсная часть приложения;

В последующих руководствах выполняется обновление приложения Vote Azure, а также настройка Operations Management Suite для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, этот образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes.

Если вы не выполнили эти действия, то можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./tutorial-kubernetes-prepare-app.md).

## <a name="scale-aks-nodes"></a>Масштабирование узлов AKS

Если вы создали кластер Kubernetes с помощью команд в предыдущем руководстве, то у него один узел. Если вы планируете увеличение или уменьшение рабочих нагрузок контейнеров в кластере, то можете соответствующим образом изменить число узлов вручную.

В следующем примере в кластере Kubernetes *myK8sCluster* число узлов увеличивается до трех. Для выполнения этой команды требуется несколько минут.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myK8SCluster --agent-count 3
```

Выходные данные должны быть следующего вида.

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myK8sCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Масштабирование pod вручную

К настоящему моменту было развернуто по отдельной реплике внешнего приложения Vote Azure и экземпляра Redis. Чтобы проверить это, выполните команду [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli
kubectl get pods
```

Выходные данные:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Вручную измените число pod в развертывании `azure-vote-front`, выполнив команду [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). В этом примере их число увеличивается до 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Выполните команду [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get), чтобы убедиться, что Kubernetes создает новые pod. Дополнительные pod будут запущены примерно через минуту.

```azurecli
kubectl get pods
```

Выходные данные:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Автомасштабирование pod

Kubernetes поддерживает [горизонтальное автомасштабирование pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) для изменения числа pod в развертывании в зависимости от использования ЦП или других выбранных метрик.

Чтобы использовать инструмент автомасштабирования, для ваших pod необходимо определить запросы и лимиты ресурсов ЦП. В развертывании `azure-vote-front` каждый контейнер внешнего приложения запрашивает 0,25 ресурсов ЦП с лимитом в 0,5 ресурсов ЦП. Параметры имеют следующий вид.

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

В следующем примере используется команда [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) для автомасштабирования числа модулей в развертывании `azure-vote-front`. Если использование ЦП превышает 50 %, то инструмент автомасштабирования увеличивает число pod максимум до 10.


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Выполните следующую команду, чтобы просмотреть состояние инструмента автомасштабирования.

```azurecli
kubectl get hpa
```

Выходные данные:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Через несколько минут минимальной нагрузки на приложение Vote Azure число реплик pod автоматически уменьшится до 3.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы использовали различные возможности масштабирования кластера Kubernetes. Были рассмотрены такие задачи:

> [!div class="checklist"]
> * масштабирование pod Kubernetes вручную;
> * настройка автомасштабирования pod, в которых выполняется интерфейсная часть приложения;
> * Масштабирование узлов Kubernetes Azure

Перейдите к следующему руководству, чтобы узнать об обновлении приложения в Kubernetes.

> [!div class="nextstepaction"]
> [Обновление приложения в Kubernetes](./tutorial-kubernetes-app-update.md)