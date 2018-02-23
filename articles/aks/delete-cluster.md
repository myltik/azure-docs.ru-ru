---
title: "Удаление кластера Службы контейнеров Azure (AKS)"
description: "Удаление кластера AKS с помощью интерфейса командной строки или портала Azure."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 78056288f45616eda427f8e708efc679f8a5202c
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2018
---
# <a name="delete-an-azure-container-service-aks-cluster"></a>Удаление кластера Службы контейнеров Azure (AKS)

При удалении кластера Службы контейнеров Azure группа ресурсов, в которой был развернут этот кластер, остается, однако удаляются все относящиеся к AKS ресурсы. В этом документе показано, как удалить кластер AKS с помощью портала Azure и Azure CLI. 

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
| `--resource-group` `-g` | Имя группы ресурсов Службы контейнеров Azure. | Да |
| `--no-wait` | Не ожидать завершения длительной операции. | Нет |
| `--yes` `-y` | Не запрашивать подтверждение. | Нет |

## <a name="azure-portal"></a>Портал Azure

На портале Azure перейдите к группе ресурсов, содержащей ресурс Службы контейнеров Azure (AKS), выберите этот ресурс и щелкните **Удалить**. Отобразится запрос на подтверждение операции удаления.

![Удаление кластера AKS с помощью портала](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete