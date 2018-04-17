---
title: Создание внутренней подсистемы балансировки нагрузки Службы контейнеров Azure (AKS)
description: Использование внутренней подсистемы балансировки нагрузки со Службой контейнеров Azure (AKS).
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7b9ecdb5364f7c0f5bb68ce693e53bc2c5327337
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
---
# <a name="use-an-internal-load-balancer-with-azure-container-service-aks"></a>Использование внутренней подсистемы балансировки нагрузки со Службой контейнеров Azure (AKS)

Внутренняя балансировка нагрузки позволяет сделать службу Kubernetes доступной для приложений, работающих в той же виртуальной сети, что и кластер Kubernetes. В этом документе описывается использование внутренней подсистемы балансировки нагрузки со Службой контейнеров Azure (AKS).

## <a name="create-internal-load-balancer"></a>Создание внутренней подсистемы балансировки нагрузки

Чтобы создать внутреннюю подсистему балансировки нагрузки, создайте манифест службы с типом службы `LoadBalancer` и заметкой `azure-load-balancer-internal`, как показано в следующем примере.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

После развертывания служба Azure Load Balancer будет создана и доступна в той же виртуальной сети, что и кластер AKS. 

![Изображение внутренней подсистемы балансировки нагрузки AKS](media/internal-lb/internal-lb.png)

При получении сведений о службе в столбце `EXTERNAL-IP` отображается IP-адрес внутренней подсистемы балансировки нагрузки. 

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>Указание IP-адреса

Если вы хотите использовать определенный IP-адрес для внутренней подсистемы балансировки нагрузки, добавьте свойство `loadBalancerIP` в ее спецификацию. Указанный IP-адрес должен находиться в той же подсети, что и кластер AKS, и не должен быть назначен какому-либо ресурсу.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

При получении сведений о службе в столбце `EXTERNAL-IP` должен отображаться указанный IP-адрес. 

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>удаление подсистемы балансировки нагрузки

При удалении всех служб, использующих внутреннюю подсистему балансировки нагрузки, она также удаляется.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о службах Kubernetes в [документации по службам Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/