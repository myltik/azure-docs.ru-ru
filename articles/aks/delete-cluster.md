---
title: Удаление кластера службы Azure Kubernetes (AKS)
description: Удаление кластера AKS с помощью интерфейса командной строки или портала Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e006466d1450471900a8635c49d3bc6c3a73d476
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Удаление кластера службы Azure Kubernetes (AKS)

При удалении кластера Службы Azure Kubernetes группа ресурсов, в которой развернут этот кластер, остается, но удаляются все относящиеся к AKS ресурсы. В этом документе показано, как удалить кластер AKS с помощью портала Azure и Azure CLI.

Помимо кластера можно удалить группу ресурсов, в которой он был развернут. При этом также удаляется и кластер AKS.

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Используйте команду [az aks delete][az-aks-delete], чтобы удалить кластер AKS.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Для команды `az aks delete` доступны следующие параметры.

| Аргумент | ОПИСАНИЕ | Обязательно |
|---|---|:---:|
| `--name` `-n` | Имя ресурса для управляемого кластера. | Да |
| `--resource-group` `-g` | Имя группы ресурсов Службы Azure Kubernetes. | Да |
| `--no-wait` | Не ожидать завершения длительной операции. | Нет |
| `--yes` `-y` | Не запрашивать подтверждение. | Нет |

## <a name="azure-portal"></a>Портал Azure

На портале Azure перейдите к группе ресурсов, содержащей ресурс Службы Azure Kubernetes (AKS), выберите этот ресурс и щелкните **Удалить**. Отобразится запрос на подтверждение операции удаления.

![Удаление кластера AKS с помощью портала](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete