---
title: Краткое руководство по работе с кластером Azure Kubernetes для Windows
description: Вы научитесь быстро создавать кластеры Kubernetes для контейнеров Windows в Службе контейнеров Azure с помощью Azure CLI.
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 74ce913548fbcefdc441d0d2b772c864dacd4482
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165005"
---
# <a name="deploy-kubernetes-cluster-for-windows-containers"></a>Кластер Azure Kubernetes для Windows | Документация Майкрософт

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве рассматривается развертывание кластера [Kubernetes](https://kubernetes.io/docs/home/) с помощью Azure CLI в [Службе контейнеров Azure](../container-service-intro.md). К развернутому кластеру можно подключиться с помощью средства командной строки Kubernetes `kubectl` и развернуть первый контейнер Windows.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Поддержка контейнеров Windows для Kubernetes в Службе контейнеров Azure реализована в режиме предварительной версии. 
>

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure. 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Создание кластера Kubernetes
Создание кластера Kubernetes в Службе контейнеров Azure с помощью команды [az acs create](/cli/azure/acs#az_acs_create). 

В следующем примере создается кластер с именем *myK8sCluster* с помощью одного главного узла Linux и двух узлов агентов Windows. В этом примере создаются ключи SSH, необходимые для подключения к главному узлу Linux. В этом примере используются имя администратора *azureuser* и его пароль *myPassword12* на узлах Windows. Измените эти значения в соответствии со своей средой. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username azureuser \
    --admin-password myPassword12
```

Через несколько минут выполнение команды завершается, и отображаются сведения о развертывании.

## <a name="install-kubectl"></a>Установка kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), клиент командной строки Kubernetes. 

Если вы используете Azure CloudShell, установка `kubectl` уже выполнена. Для локальной установки можно использовать команду [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli).

В следующем примере `kubectl` устанавливается в систему с помощью Azure CLI. В Windows запустите следующую команду от имени администратора:

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

## <a name="deploy-a-windows-iis-container"></a>Развертывание контейнера Windows IIS

Можно запустить контейнер Docker внутри группы контейнеров Kubernetes (объект *pod*), которая содержит один или несколько контейнеров. 

В этом простом примере используется JSON-файл. Вы сможете определить контейнер Microsoft Internet Information Server (IIS), а затем с помощью команды `kubctl apply` создать модуль. 

Создайте локальный файл `iis.json` и скопируйте в него следующий текст. Этот файл указывает Kubernetes запустить службы IIS в Nano Server Windows Server 2016 с помощью образа общедоступного контейнера из [концентратора Docker](https://hub.docker.com/r/microsoft/iis/). Контейнер использует порт 80, но изначально он доступен только в сети кластера.

 ```JSON
 {
  "apiVersion": "v1",
  "kind": "Pod",
  "metadata": {
    "name": "iis",
    "labels": {
      "name": "iis"
    }
  },
  "spec": {
    "containers": [
      {
        "name": "iis",
        "image": "microsoft/iis:nanoserver",
        "ports": [
          {
          "containerPort": 80
          }
        ]
      }
    ],
    "nodeSelector": {
     "beta.kubernetes.io/os": "windows"
     }
   }
 }
 ```

Чтобы запустить модуль, введите:
  
```azurecli-interactive
kubectl apply -f iis.json
```  

Для отслеживания развертывания введите:
  
```azurecli-interactive
kubectl get pods
```

Во время развертывания модуль находится в состоянии `ContainerCreating`. Переход контейнера в состояние `Running` может занять несколько минут.

```azurecli-interactive
NAME     READY        STATUS        RESTARTS    AGE
iis      1/1          Running       0           32s
```

## <a name="view-the-iis-welcome-page"></a>Просмотр страницы приветствия IIS

Чтобы обеспечить интернет-доступ к группе контейнеров с помощью общедоступного IP-адреса, введите следующую команду:

```azurecli-interactive
kubectl expose pods iis --port=80 --type=LoadBalancer
```

С ее помощью Kubernetes создаст службу и правило подсистемы балансировки нагрузки Azure с общедоступным IP-адресом для службы. 

Выполните следующую команду, чтобы просмотреть состояние службы.

```azurecli-interactive
kubectl get svc
```

Изначально IP-адрес выглядит так: `pending`. Через несколько минут установка внешнего IP-адреса для группы контейнеров `iis` будет завершена:
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
iis          10.0.111.25    13.64.158.233   80/TCP         22m
```

Страницу приветствия IIS, отображающуюся при переходе по внешнему IP-адресу по умолчанию, можно просмотреть в любом веб-браузере:

![Изображение перехода к IIS](./media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  


## <a name="delete-cluster"></a>Удаление кластера
Чтобы удалить ненужные кластер, группу ресурсов, службу контейнеров и все связанные с ней ресурсы, выполните команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве описано, как развертывать подключенный к `kubectl` кластер Kubernetes и группу контейнеров с контейнером IIS. Дополнительные сведения о Службе контейнеров Azure см. в руководстве по Kubernetes.

> [!div class="nextstepaction"]
> [Create container images to be used with Azure Container Service](container-service-tutorial-kubernetes-prepare-app.md) (Создание образов контейнеров для использования со Службой контейнеров Azure)
