---
title: "Руководство по Kubernetes в Azure. Развертывание кластера | Документация Майкрософт"
description: "Руководство по AKS. Развертывание кластера"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 69dea4ab748d88d18cf01dc9b3fc1bdddd562681
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>Развертывание кластера Службы контейнеров Azure (AKS)

Kubernetes предоставляет распределенную платформу для контейнерных приложений. AKS упрощает и ускоряет подготовку кластера Kubernetes для использования в рабочей среде. В этом руководстве (третья часть из восьми) кластер Kubernetes развертывается в AKS. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * развертывание кластера AKS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

В последующих руководствах приложение Azure для голосования развертывается в кластере, масштабируется и обновляется, а Operations Management Suite настраивается для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./tutorial-kubernetes-prepare-app.md).

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Включение предварительной версии AKS в подписке Azure
Пока AKS находится н этапе предварительной версии, для создания новых кластеров требуется флаг этого компонента в подписке. Можно запросить этот компонент для любого числа подписок, которые вы хотите использовать. Используйте команду `az provider register`, чтобы зарегистрировать поставщик AKS.

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

После регистрации все будет готово к созданию кластера Kubernetes с AKS.

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes

В следующем примере создается кластер `myK8sCluster` в группе ресурсов `myResourceGroup`. Эта группа ресурсов была создана в [предыдущем руководстве](./tutorial-kubernetes-prepare-acr.md).

```azurecli
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

Через несколько минут развертывание завершится и отобразятся сведения о развертывании AKS в формате JSON.

## <a name="install-the-kubectl-cli"></a>Установка интерфейса командной строки kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), клиент командной строки Kubernetes.

Если вы используете Azure CloudShell, клиент kubectl уже установлен. Если нужно установить его локально, выполните следующую команду:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить kubectl для подключения к кластеру Kubernetes, выполните следующую команду:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Чтобы проверить подключение к кластеру, выполните команду [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

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
> [Запуск приложений в Kubernetes](./tutorial-kubernetes-deploy-application.md)
