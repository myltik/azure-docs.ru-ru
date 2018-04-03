---
title: Руководство по Kubernetes в Azure. Развертывание кластера
description: Руководство по AKS. Развертывание кластера
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a28f2f3e397c33e8bd98fcee68c5a051432cb3db
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2018
---
# <a name="tutorial-deploy-an-azure-container-service-aks-cluster"></a>Руководство. Развертывание кластера Службы контейнеров Azure (AKS)

Kubernetes предоставляет распределенную платформу для контейнерных приложений. AKS упрощает и ускоряет подготовку кластера Kubernetes для использования в рабочей среде. В этом руководстве (третья часть из восьми) кластер Kubernetes развертывается в AKS. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * развертывание кластера AKS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

При помощи последующих руководств приложение Azure для голосования развертывается в кластере, масштабируется и обновляется, а Log Analytics настраивается для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Prepare application for Azure Container Service (AKS)][aks-tutorial-prepare-app] (Подготовка приложений для службы контейнеров Azure (AKS)).

## <a name="enable-aks-preview"></a>Включение предварительной версии AKS

Пока AKS находится н этапе предварительной версии, для создания новых кластеров требуется флаг этого компонента в подписке. Можно запросить этот компонент для любого числа подписок, которые вы хотите использовать. Используйте команду `az provider register`, чтобы зарегистрировать поставщик AKS.

```azurecli
az provider register -n Microsoft.ContainerService
```

После регистрации все будет готово к созданию кластера Kubernetes с AKS.

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes

В следующем примере создается кластер `myAKSCluster` в группе ресурсов `myResourceGroup`. Эта группа ресурсов создана в [предыдущем руководстве][aks-tutorial-prepare-acr].

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

Через несколько минут развертывание завершится и отобразятся сведения о развертывании AKS в формате JSON.

## <a name="install-the-kubectl-cli"></a>Установка интерфейса командной строки kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте средство [kubectl][kubectl] (клиент командной строки Kubernetes).

Если вы используете Azure CloudShell, клиент kubectl уже установлен. Если нужно установить его локально, выполните следующую команду:

```azurecli
az aks install-cli
```

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить kubectl для подключения к кластеру Kubernetes, выполните следующую команду:

```azurecli
az aks get-credentials --resource-group=myResourceGroup --name=myAKSCluster
```

Чтобы проверить подключение к кластеру, выполните команду [kubectl get nodes][kubectl-get].

```azurecli
kubectl get nodes
```

Выходные данные:

```
NAME                          STATUS    AGE       VERSION
k8s-myAKSCluster-36346190-0   Ready     49m       v1.7.9
```

По завершении руководства вы получите кластер AKS, готовый к обработке рабочих нагрузок. В следующих руководствах в этот кластер будет развернуто многоконтейнерное приложение, которое затем будет масштабировано, обновлено и отслежено.

## <a name="configure-acr-authentication"></a>Настройка проверки подлинности ACR

Для проверки подлинности необходимо настроить кластер AKS и реестр ACR. Этот процесс включает в себя предоставление удостоверению AKS необходимых прав для извлечения образов из реестра ACR.

Сначала получите идентификатор субъекта-службы, настроенного для AKS. Измените имена группы ресурсов и кластера AKS в соответствии со своей средой.

```azurecli
CLIENT_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId" --output tsv)
```

Получите идентификатор ресурса реестра ACR. Измените имя реестра на имя своего реестра ACR, а имя группы ресурсов — на имя той группы ресурсов, в которой находится реестр ACR.

```azurecli
ACR_ID=$(az acr show --name <acrName> --resource-group myResourceGroup --query "id" --output tsv)
```

Создайте назначение ролей для предоставления необходимого уровня доступа.

```azurecli
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы развернули кластер Kubernetes в AKS. Были выполнены следующие действия:

> [!div class="checklist"]
> * развертывание кластера AKS;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

Перейдите к следующему руководству, чтобы узнать о выполнении приложения в кластере.

> [!div class="nextstepaction"]
> [Развертывание приложений в Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
