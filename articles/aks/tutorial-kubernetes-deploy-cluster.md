---
title: "Руководство по Kubernetes в Azure. Развертывание кластера"
description: "Руководство по AKS. Развертывание кластера"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2c2318d9a5f72800f9cfbd430dca448fd1e5746f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Развертывание кластера Службы контейнеров Azure (AKS)

Kubernetes предоставляет распределенную платформу для контейнерных приложений. AKS упрощает и ускоряет подготовку кластера Kubernetes для использования в рабочей среде. В этом руководстве (третья часть из восьми) кластер Kubernetes развертывается в AKS. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * развертывание кластера AKS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

В последующих руководствах приложение Azure для голосования развертывается в кластере, масштабируется и обновляется, а Operations Management Suite настраивается для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не были выполнены следующие действия и при необходимости дальнейшей работы, вернуться к [учебник 1 – Создание образов контейнеров][aks-tutorial-prepare-app].

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Включение предварительной версии AKS в подписке Azure
Пока AKS находится н этапе предварительной версии, для создания новых кластеров требуется флаг этого компонента в подписке. Можно запросить этот компонент для любого числа подписок, которые вы хотите использовать. Используйте команду `az provider register`, чтобы зарегистрировать поставщик AKS.

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

После регистрации все будет готово к созданию кластера Kubernetes с AKS.

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes

В следующем примере создается кластер `myK8sCluster` в группе ресурсов `myResourceGroup`. Эта группа ресурсов был создан в [с предыдущим учебником][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Через несколько минут развертывание завершится и отобразятся сведения о развертывании AKS в формате JSON.

## <a name="install-the-kubectl-cli"></a>Установка интерфейса командной строки kubectl

Чтобы подключиться к кластеру Kubernetes с клиентского компьютера, используйте [kubectl][kubectl], Kubernetes клиент командной строки.

Если вы используете Azure CloudShell, клиент kubectl уже установлен. Если нужно установить его локально, выполните следующую команду:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить kubectl для подключения к кластеру Kubernetes, выполните следующую команду:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Чтобы проверить подключение к кластеру, запустите [kubectl получения узлов] [ kubectl-get] команды.

```azurecli
kubectl get nodes
```

Выходные данные:

```
NAME                          STATUS    AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     49m       v1.7.7
```

По завершении руководства вы получите кластер AKS, готовый к обработке рабочих нагрузок. В следующих руководствах в этот кластер будет развернуто многоконтейнерное приложение, которое затем будет масштабировано, обновлено и отслежено.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы развернули кластер Kubernetes в AKS. Были выполнены следующие действия:

> [!div class="checklist"]
> * развертывание кластера AKS;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

Перейдите к следующему руководству, чтобы узнать о выполнении приложения в кластере.

> [!div class="nextstepaction"]
> [Развертывание приложения в Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md