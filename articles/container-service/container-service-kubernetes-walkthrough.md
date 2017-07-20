---
title: "Краткое руководство: кластер Azure Kubernetes для Linux | Документация Майкрософт"
description: "Вы научитесь быстро создавать кластер Kubernetes для контейнеров Linux в Службе контейнеров Azure при помощи Azure CLI."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: 3be2079d205d6bfd4c796e5f6abcd7ac5fe595a2
ms.contentlocale: ru-ru
ms.lasthandoff: 07/19/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Развертывание кластера Kubernetes для контейнеров Linux

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве рассматривается развертывание кластера [Kubernetes](https://kubernetes.io/docs/home/) с помощью Azure CLI в [Службе контейнеров Azure](container-service-intro.md). После развертывания кластера к нему можно подключиться при помощи программы командной строки Kubernetes `kubectl` и развернуть первый контейнер Linux.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes
Создание кластера Kubernetes в Службе контейнеров Azure с помощью команды [az acs create](/cli/azure/acs#create). 

В следующем примере создается кластер с именем *myK8sCluster* при помощи одного главного узла Linux и двух узлов агентов Linux. В этом примере создаются ключи SSH, если их еще нет в расположениях по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`. Измените имя кластера в соответствии со своей средой. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

Через несколько минут выполнение команды завершается, и отображаются сведения о развертывании.

## <a name="install-kubectl"></a>Установка kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), клиент командной строки Kubernetes. 

Если вы используете Azure CloudShell, установка `kubectl` уже выполнена. Для локальной установки можно использовать команду [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

В следующем примере `kubectl` устанавливается в систему с помощью Azure CLI. Если вы используете Azure CLI в macOS или Linux, возможно, потребуется выполнить команду с `sudo`.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Подключение с помощью kubectl

Чтобы настроить `kubectl` для подключения к кластеру Kubernetes, запустите команду [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials). В следующем примере загружается конфигурация для кластера Kubernetes.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

Чтобы проверить подключение между кластером и компьютером, попробуйте выполнить следующую команду:

```azurecli-interactive
kubectl get nodes
```

При помощи `kubectl` выводится список главных узлов и узлов агентов.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>Развертывание контейнера nginx

Можно запустить контейнер Docker внутри группы контейнеров Kubernetes (объект *pod*), которая содержит один или несколько контейнеров. 

Команда ниже запускает контейнер Docker nginx в модуле Kubernetes на одном из узлов. В данном случае контейнер работает с веб-сервером nginx, извлеченным из образа в [концентраторе Docker](https://hub.docker.com/_/nginx/).

```azurecli-interactive
kubectl run nginx --image nginx
```
Чтобы убедиться, что контейнер работает, запустите команду:

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>Просмотр страницы приветствия nginx
Чтобы предоставить серверу nginx общедоступный IP-адрес, введите следующую команду:

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

С ее помощью Kubernetes создаст службу и [правило подсистемы балансировки нагрузки Azure](container-service-kubernetes-load-balancing.md) с общедоступным IP-адресом для службы. 

Выполните следующую команду, чтобы просмотреть состояние службы.

```azurecli-interactive
kubectl get svc
```

Изначально IP-адрес выглядит так: `pending`. Через несколько минут установка внешнего IP-адреса для службы будет завершена:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

Страницу приветствия nginx, отображающуюся для внешнего IP-адреса по умолчанию, можно просмотреть в любом веб-браузере:

![Изображение перехода на страницу Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>Удаление кластера
Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete](/cli/azure/group#delete).

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве развернуты кластер Kubernetes, подключенный к `kubectl`, и модуль с контейнером nginx. Дополнительные сведения о Службе контейнеров Azure см. далее в руководстве по кластерам Kubernetes.

> [!div class="nextstepaction"]
> [Create container images to be used with Azure Container Service](./container-service-tutorial-kubernetes-prepare-app.md) (Создание образов контейнеров для использования со Службой контейнеров Azure)

