---
title: 'Руководство по Службе контейнеров Azure: развертывание кластера'
description: 'Руководство по Службе контейнеров Azure: развертывание кластера'
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 762a659e588a9b26b98241fce4c46fb831d355aa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163084"
---
# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Развертывание кластера Kubernetes в службе контейнеров Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Kubernetes предоставляет распределенную платформу для контейнерных приложений. Служба контейнеров Azure упрощает и убыстряет подготовку кластера Kubernetes для использования в рабочей среде. В этом руководстве (часть 3 из 7) развертывается кластер Kubernetes службы контейнеров Azure. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * развертывание кластера ACS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

При помощи последующих руководств приложение Azure для голосования развертывается в кластере, масштабируется и обновляется, а Log Analytics настраивается для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes

Создание кластера Kubernetes в Службе контейнеров Azure с помощью команды [az acs create](/cli/azure/acs#az_acs_create). 

В следующем примере создается кластер `myK8sCluster` в группе ресурсов `myResourceGroup`. Эта группа ресурсов была создана в [предыдущем руководстве](./container-service-tutorial-kubernetes-prepare-acr.md).

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

В некоторых случаях, например при использовании ограниченной пробной версии, доступ подписки Azure к ресурсам Azure ограничен. Если происходит сбой развертывания из-за ограничения доступных ядер, уменьшите количество агентов по умолчанию, добавив `--agent-count 1` в команду [az acs create](/cli/azure/acs#az_acs_create). 

Через несколько минут развертывание завершится и отобразятся сведения о развертывании ACS в формате JSON.

## <a name="install-the-kubectl-cli"></a>Установка интерфейса командной строки kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), клиент командной строки Kubernetes. 

Если вы используете Azure CloudShell, клиент kubectl уже установлен. Для локальной установки можно использовать команду [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Для работы в macOS или Linux может потребоваться запуск с помощью sudo. В Windows убедитесь, что оболочка запущена от имени администратора.

```azurecli-interactive 
az acs kubernetes install-cli 
```

В Windows установка по умолчанию выполняется в папку *c:\program files (x86)\kubectl.exe*. Может потребоваться добавить этот файл в путь Windows. 

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить kubectl для подключения к кластеру Kubernetes, выполните команду [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

Чтобы проверить подключение к кластеру, выполните команду [kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get nodes
```

Выходные данные:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

По завершении руководства вы получите кластер ACS Kubernetes, готовый к обработке рабочих нагрузок. В следующих руководствах в этот кластер будет развернуто многоконтейнерное приложение, которое затем будет масштабировано, обновлено и отслежено.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве был развернут кластер Kubernetes Службы контейнеров Azure. Были выполнены следующие действия:

> [!div class="checklist"]
> * развертывание кластера ACS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

Перейдите к следующему руководству, чтобы узнать о выполнении приложения в кластере.

> [!div class="nextstepaction"]
> [Запуск приложений в Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)
