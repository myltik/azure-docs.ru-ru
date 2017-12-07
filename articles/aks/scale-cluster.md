---
title: "Масштабирование кластера Службы контейнеров Azure (AKS)"
description: "Масштабируйте кластер Службы контейнеров Azure (AKS)."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 299eb74686f00dc6d5eb9a1c6127aa134dcd9b77
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Масштабирование кластера Службы контейнеров Azure (AKS)

Вы можете легко масштабировать кластер AKS до различного количества узлов.  Выберите требуемое число узлов и запустите команду `az aks scale`.  При уменьшении масштаба узлы будут тщательно [заблокированы и остановлены](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/), чтобы свести к минимуму время простоя работающих приложений.  При увеличении масштаба `az` команда ожидает, пока кластер Kubernetes не задаст для узлов состояние `Ready`.

## <a name="scale-the-cluster-nodes"></a>Масштабирование узлов кластера

Используйте команду `az aks scale`, чтобы масштабировать узлы кластера. В следующем примере масштабируется кластер *myK8SCluster* к одному узлу.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о развертывании AKS и управлении ею из руководств по AKS.

> [!div class="nextstepaction"]
> [Подготовка приложения к Службе контейнеров Azure (AKS)](./tutorial-kubernetes-prepare-app.md)
