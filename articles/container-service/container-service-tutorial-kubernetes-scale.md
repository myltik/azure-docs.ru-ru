---
title: "Руководство по Службе контейнеров Azure: масштабирование приложения | Документация Майкрософт"
description: "Руководство по Службе контейнеров Azure: масштабирование приложения"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: f0e2c0bc0eeeceb86bc5108a4e4866392a79e016
ms.contentlocale: ru-ru
ms.lasthandoff: 07/08/2017

---

# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Масштабирование pod и инфраструктуры Kubernetes

Если вы выполнили инструкции в руководствах, то у вас имеется работающий кластер Kubernetes в Службе контейнеров Azure и вы развернули в нем приложение Vote Azure. 

В этом руководстве описывается масштабирование pod, содержащихся в приложении, и их автомасштабирование. Вы также узнаете, как масштабировать количество узлов агентов, чтобы менять емкость кластера для размещения рабочих нагрузок. Вам предстоят следующие задачи:

> [!div class="checklist"]
> * масштабирование pod Kubernetes вручную;
> * настройка автомасштабирования pod, в которых выполняется интерфейсная часть приложения;
> * масштабирование узлов агентов Kubernetes в Azure.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образы контейнеров, образы были отправлены в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes. Если вы не выполнили эти действия, то можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

Для этого руководства как минимум необходим кластер Kubernetes с выполняющимся приложением.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, то для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="manually-scale-pods"></a>Масштабирование pod вручную

При изучении предыдущего руководства внешний интерфейс и серверная часть приложения Vote Azure были развернуты в отдельных pod. Чтобы проверить это, выполните команду [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pods
```

Результат аналогичен приведенному ниже:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Вручную измените число pod в развертывании `azure-vote-front`, выполнив команду [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). В этом примере их число увеличивается до 5.

```bash
kubectl scale --replicas=5 deployment/azure-vote-front
```

Выполните команду [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get), чтобы убедиться, что Kubernetes создает новые pod. Дополнительные pod будут запущены примерно через минуту.

```bash
kubectl get pods
```

Выходные данные:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Автомасштабирование pod

Kubernetes поддерживает [горизонтальное автомасштабирование pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) для изменения числа pod в развертывании в зависимости от использования ЦП или других метрик. 

Чтобы использовать инструмент автомасштабирования, для ваших pod необходимо определить запросы и лимиты ресурсов ЦП. В развертывании `azure-vote-front` каждый контейнер запрашивает 0,25 ресурсов ЦП с лимитом в 0,5 ресурсов ЦП. Параметры имеют следующий вид.

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

В следующем примере используется команда [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) для автомасштабирования числа модулей в развертывании `azure-vote-front`. Если использование ЦП превышает 50 %, то инструмент автомасштабирования увеличивает число pod максимум до 10.


```bash
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Выполните следующую команду, чтобы просмотреть состояние инструмента автомасштабирования.

```bash
kubectl get hpa
```

Выходные данные:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Через несколько минут минимальной нагрузки на приложение Vote Azure число реплик pod автоматически уменьшится до 3.

## <a name="scale-the-agents"></a>Масштабирование агентов

Если вы создали кластер Kubernetes с помощью команд по умолчанию в предыдущем руководстве, то у него три узла агентов. Если вы планируете увеличение или уменьшение рабочих нагрузок контейнеров в кластере, то можете соответствующим образом изменить число агентов вручную. Выполните команду [az acs scale](/cli/azure/acs#scale), указав количество агентов с помощью параметра `--new-agent-count`.

В следующем примере в кластере Kubernetes *myK8sCluster* число узлов агентов увеличивается до 4. Для выполнения этой команды требуется несколько минут.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

Выходные данные команды содержат число узлов агентов в значении `agentPoolProfiles:count`.

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы использовали различные возможности масштабирования кластера Kubernetes. Были рассмотрены такие задачи:

> [!div class="checklist"]
> * масштабирование pod Kubernetes вручную;
> * настройка автомасштабирования pod, в которых выполняется интерфейсная часть приложения;
> * масштабирование узлов агентов Kubernetes в Azure.

Перейдите к следующему руководству, чтобы узнать об обновлении приложения в Kubernetes.

> [!div class="nextstepaction"]
> [Обновление приложения в Kubernetes](./container-service-tutorial-kubernetes-app-update.md)


