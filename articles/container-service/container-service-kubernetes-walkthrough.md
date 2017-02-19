---
title: "Быстрый запуск кластера Kubernetes в Azure | Документация Майкрософт"
description: "Развертывание кластера Kubernetes в службе контейнеров Azure и начало работы с ним."
services: container-service
documentationcenter: 
author: anhowe
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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Обработчик службы контейнеров Microsoft Azure. Пошаговое руководство по использованию Kubernetes

## <a name="prerequisites"></a>Предварительные требования
В этом пошаговом руководстве предполагается, что вы установили [программу командной строки azure-cli](https://github.com/azure/azure-cli#installation) и создали [открытый ключ SSH](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) в `~/.ssh/id_rsa.pub`.

## <a name="overview"></a>Обзор

В инструкциях ниже описывается создание кластера Kubernetes с одним главным и двумя рабочими узлами.
Основной узел обслуживает REST API кластера Kubernetes.  Рабочие узлы группируются в группы доступности Azure. На них также запускаются контейнеры. Все виртуальные машины подключены к одной частной виртуальной сети и полностью доступны друг для друга.

> [!NOTE]
> Поддержка Kubernetes в службе контейнеров Azure сейчас доступна в предварительной версии.
>

На следующем рисунке показана архитектура кластера службы контейнеров с одним главным узлом и двумя агентами.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Создание кластера Kubernetes

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Чтобы создать кластер, необходимо сначала создать группу ресурсов в определенном расположении. Это можно сделать с помощью следующей команды.
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Создание кластера
Создав группу ресурсов, можно приступить к созданию кластера в этой группе с помощью следующей команды.
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Интерфейс командной строки Azure отправит файл `~/.ssh/id_rsa.pub` на виртуальные машины Linux.
>

После выполнения этой команды появится рабочий кластер Kubernetes.

### <a name="configure-kubectl"></a>Настройка kubectl
`kubectl` — клиент командной строки Kubernetes.  Установите его с помощью команды ниже, если вы еще этого не сделали.

```console
az acs kubernetes install-cli
```

После установки `kubectl` запустите приведенную ниже команду, чтобы скачать конфигурацию главного кластера Kubernetes в файл ~/.kube/config.
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

На этом этапе все должно быть готово для доступа к кластеру с компьютера. Попробуйте запустить следующую команду.
```console
kubectl get nodes
```

Проверьте, отображаются ли компьютеры в кластере.

## <a name="create-your-first-kubernetes-service"></a>Создание первой службы Kubernetes

Выполнив инструкции из этого руководства, вы научитесь:
 * развертывать приложение Docker и предоставлять к нему доступ;
 * использовать `kubectl exec` для выполнения команд в контейнере; 
 * получать доступ к панели мониторинга Kubernetes.

### <a name="start-a-simple-container"></a>Запуск простого контейнера
Чтобы запустить простой контейнер (в данном случае веб-сервер `nginx`), выполните команду ниже.

```console
kubectl run nginx --image nginx
```

Эта команда запускает контейнер Docker nginx в модуле на одном из узлов.

Можно запустить,
```console
kubectl get pods
```

чтобы просмотреть запущенный контейнер.

### <a name="expose-the-service-to-the-world"></a>Предоставление доступа к службе
Чтобы предоставить доступ к службе,  создайте службу Kubernetes `Service` типа `LoadBalancer`.

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Таким образом Kubernetes создаст балансировщик Azure Load Balancer с общедоступным IP-адресом. Распространение изменений в подсистеме балансировки нагрузки занимает около 2–3 минут.

Чтобы просмотреть изменение состояния службы с ожидания на использование внешнего IP-адреса, введите:
```console
watch 'kubectl get svc'
```

  ![Изображение выходных данных с переходом от ожидания к использованию внешнего IP-адреса](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Полученный внешний IP-адрес можно ввести в браузере:

  ![Изображение перехода на страницу nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Обзор пользовательского интерфейса Kubernetes
Чтобы просмотреть веб-интерфейс Kubernetes, можно использовать:

```console
kubectl proxy
```
Эта команда запускает простой проверенный прокси-сервер на локальном узле, который можно использовать для просмотра [пользовательского интерфейса Kubernetes](http://localhost:8001/ui).

![Изображение панели мониторинга Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Удаленные сеансы в контейнерах
Kubernetes позволяет выполнять команды в удаленном контейнере Docker, запущенном в кластере.

```console
# Get the name of your nginx pod
kubectl get pods
```

Используя имя модуля, можно запустить удаленную команду в модуле.  Например:
```console
kubectl exec nginx-701339712-retbj date
```

Кроме того, можно получить полностью интерактивный сеанс с помощью флагов `-it`:

```console
kubectl exec nginx-701339712-retbj -it bash
```

![Изображение выполнения curl с IP-адресом модуля](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>Сведения
### <a name="installing-the-kubectl-configuration-file"></a>Установка файла конфигурации kubectl
После запуска `az acs kubernetes get-credentials` файл конфигурации kube для удаленного доступа сохранился в корневом каталоге ~/.kube/config.

Если вам когда-либо понадобится загрузить его напрямую, можете воспользоваться клиентом `ssh` в Linux или OS X либо `Putty` в Windows:

#### <a name="windows"></a>Windows
Чтобы воспользоваться средством pscp из [putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html), сделайте следующее.  Проверьте, предоставлен ли сертификат, с помощью [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant):
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>В OS X или Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>Дополнительные сведения

### <a name="azure-container-service"></a>Служба контейнеров Azure

1. [Документация по службе контейнеров Azure](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure Container Service Open Source Engine](https://github.com/azure/acs-engine) (Обработчик с открытым исходным кодом для Службы контейнеров Azure)

### <a name="kubernetes-community-documentation"></a>Документация сообщества Kubernetes

1. В [учебном курсе по Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) описано, как выполнять развертывание, масштабирование, обновление и отладку контейнерных приложений.
2. В [руководстве по Kubernetes](http://kubernetes.io/docs/user-guide/) показано, как выполнять команды в существующем кластере Kubernetes.
3. На странице с [примерами Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) представлены примеры запуска реальных приложений с использованием Kubernetes.



<!--HONumber=Jan17_HO4-->


