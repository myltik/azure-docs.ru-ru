---
title: "Руководство по Kubernetes в Azure. Обновление кластера | Документация Майкрософт"
description: "Руководство по Kubernetes в Azure. Обновление кластера"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d0193e891c6c41687f1aaa3a8033bc71e85b10c3
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Обновление Kubernetes в Службе контейнеров Azure (AKS)

Кластер Службы контейнеров Azure (AKS) можно обновить с помощью Azure CLI. В процессе обновления узлы Kubernetes тщательно [блокируются и останавливаются](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), чтобы свести к минимуму время простоя работающих приложений.

В этом руководстве (часть восьмая из восьми) обновляется кластер Kubernetes. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, этот образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes.

Если вы не выполнили эти действия, то можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./tutorial-kubernetes-prepare-app.md).


## <a name="get-cluster-versions"></a>Получение версий кластера

Перед обновлением кластера выполните команду `az aks get-versions`, чтобы проверить выпуски Kubernetes, доступные для обновления.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Здесь можно увидеть, что текущая версия узла — `1.7.7`, а версии `1.7.9`, `1.8.1` и `1.8.2` доступны.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>Обновление кластера

Используйте команду `az aks upgrade`, чтобы обновить узлы кластера. В следующих примерах кластер обновляется до версии `1.8.2`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Выходные данные:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
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
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Проверка обновления

Теперь можно проверить, успешно ли выполнено обновление, выполнив команду `az aks show`.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Выходные данные:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы обновили Kubernetes в кластере AKS. Были выполнены следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

Перейдите по ссылке для получения дополнительных сведений об AKS.

> [!div class="nextstepaction"]
> [Знакомство со Службой контейнеров Azure (AKS)](./intro-kubernetes.md)