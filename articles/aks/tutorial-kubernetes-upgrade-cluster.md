---
title: Руководство по Kubernetes в Azure. Обновление кластера
description: Руководство по Kubernetes в Azure. Обновление кластера
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a293ebbd2ec07d9de53d168f79b8546576499bcb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-container-service-aks"></a>Руководство. Обновление Kubernetes в Службе контейнеров Azure (AKS)

Кластер Службы контейнеров Azure (AKS) можно обновить с помощью Azure CLI. При обновлении узлы Kubernetes [блокируются и останавливаются][kubernetes-drain], чтобы минимизировать время простоя запущенных приложений.

В этом руководстве (часть восьмая из восьми) обновляется кластер Kubernetes. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, этот образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes.

Если вы не выполнили эти действия и хотите продолжить работу, вернитесь к руководству по [созданию образов контейнеров (часть 1)][aks-tutorial-prepare-app].


## <a name="get-cluster-versions"></a>Получение версий кластера

Перед обновлением кластера выполните команду `az aks get-upgrades`, чтобы проверить выпуски Kubernetes, доступные для обновления.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Здесь вы видите, что текущая версия узла — `1.7.9`, а доступные версии обновления находятся ниже в столбце обновлений.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>Обновление кластера

Используйте команду `az aks upgrade`, чтобы обновить узлы кластера. В следующих примерах кластер обновляется до версии `1.8.2`.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Выходные данные:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
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
        "name": "myAKSCluster",
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

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Выходные данные:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обновили Kubernetes в кластере AKS. Были выполнены следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

Перейдите по ссылке для получения дополнительных сведений об AKS.

> [!div class="nextstepaction"]
> [Общие сведения о службе AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md