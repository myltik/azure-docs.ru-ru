---
title: "Руководство по Службе контейнеров Azure: развертывание кластера | Документация Майкрософт"
description: "Руководство по Службе контейнеров Azure: развертывание кластера"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 16070499b7befca26d55259d845d1dbc14110f2a
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Развертывание кластера Kubernetes в службе контейнеров Azure

Kubernetes предоставляет распределенную платформу для контейнерных приложений. Служба контейнеров Azure упрощает и убыстряет подготовку кластера Kubernetes для использования в рабочей среде. В этом руководстве (часть 3 из 7) развертывается кластер Kubernetes службы контейнеров Azure. В частности, рассматриваются такие шаги:

> [!div class="checklist"]
> * развертывание кластера ACS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

В последующих руководствах приложение Azure для голосования развертывается в кластере, масштабируется и обновляется, а Operations Management Suite настраивается для отслеживания кластера Kubernetes.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах образ контейнера был создан и передан в экземпляр реестра контейнеров Azure. Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes

В [предыдущем руководстве](./container-service-tutorial-kubernetes-prepare-acr.md) была создана группа ресурсов *myResourceGroup*. Если вы этого не сделали, создайте эту группу ресурсов сейчас.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Создание кластера Kubernetes в Службе контейнеров Azure с помощью команды [az acs create](/cli/azure/acs#create). 

В следующем примере создается кластер *myK8sCluster* при помощи одного главного узла Linux и трех узлов агентов Linux.

```azurecli-interactive 
az acs create --orchestrator-type=kubernetes --resource-group myResourceGroup --name=myK8SCluster --generate-ssh-keys 
```

Через несколько минут выполнение команды завершается, и отображаются сведения о развертывании кластера ACS в формате JSON.

## <a name="install-the-kubectl-cli"></a>Установка интерфейса командной строки kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), клиент командной строки Kubernetes. 

Если вы используете Azure CloudShell, установка `kubectl` уже выполнена. Для локальной установки можно использовать команду [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

Для работы в macOS или Linux может потребоваться запуск с помощью sudo. В Windows убедитесь, что оболочка запущена от имени администратора.

```azurecli-interactive 
az acs kubernetes install-cli 
```

В Windows установка по умолчанию выполняется в папку *c:\program files (x86)\kubectl.exe*. Может потребоваться добавить этот файл в путь Windows. 

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить `kubectl` для подключения к кластеру Kubernetes, запустите команду [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials).

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8SCluster
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

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве был развернут кластер Kubernetes Службы контейнеров Azure. Были выполнены следующие действия:

> [!div class="checklist"]
> * развертывание кластера ACS Kubernetes;
> * установка интерфейса командной строки Kubernetes (kubectl);
> * настройка kubectl.

Перейдите к следующему руководству, чтобы узнать о выполнении приложения в кластере.

> [!div class="nextstepaction"]
> [Запуск приложений в Kubernetes](./container-service-tutorial-kubernetes-deploy-application.md)

