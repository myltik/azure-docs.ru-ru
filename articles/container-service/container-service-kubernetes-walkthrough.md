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
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: d342e21e8e0bd78b713f3a6b2f7b3c319185eb43
ms.lasthandoff: 03/22/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Приступая к работе с кластером Kubernetes в службе контейнеров Azure


В этой статье показано, как использовать команды Azure CLI 2.0 для создания кластера Kubernetes. После этого можно использовать программу командной строки `kubectl`, чтобы приступить к работе с контейнерами в кластере.

На следующем рисунке показана архитектура кластера службы контейнеров с одним главным узлом и двумя агентами. Основной узел обслуживает REST API кластера Kubernetes. Узлы агента группируются в группы доступности Azure. На них запускаются контейнеры. Все виртуальные машины размещены в одной частной виртуальной сети и полностью доступны друг для друга.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Предварительные требования
В этом пошаговом руководстве предполагается, что вы установили и настроили [Azure CLI 2.0](/cli/azure/install-az-cli2). Также необходимо, чтобы открытый ключ RSA ( SSH) находился в `~/.ssh/id_rsa.pub`. Если его у вас нет, ознакомьтесь с инструкциями для [OS X и Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) или [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md).






## <a name="create-your-kubernetes-cluster"></a>Создание кластера Kubernetes

Ниже кратко описаны команды оболочки, использующие Azure CLI 2.0 для создания кластера. Дополнительные сведения см. в разделе [Использование Azure CLI 2.0 для создания кластера Службы контейнеров Azure](container-service-create-acs-cluster-cli.md).

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Чтобы создать кластер, необходимо сначала создать группу ресурсов в определенном расположении. Выполните команды, как показано ниже.

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Создание кластера
Создав группу ресурсов, можно приступить к созданию кластера в этой группе.

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Во время развертывания интерфейс командной строки передает `~/.ssh/id_rsa.pub` на виртуальные машины Linux.
>

После выполнения этой команды появится рабочий кластер Kubernetes.

### <a name="connect-to-the-cluster"></a>Подключение к кластеру

Ниже перечислены команды Azure CLI для подключения к кластеру Kubernetes с клиентского компьютера с помощью `kubectl`, клиента командной строки Kubernetes. Дополнительные сведения см. в статье [Подключение к кластеру службы контейнеров Azure](container-service-connect.md).

Установите `kubectl` с помощью команды ниже, если вы еще этого не сделали.

```console
az acs kubernetes install-cli
```

После установки `kubectl` выполните приведенную ниже команду, чтобы скачать конфигурацию главного кластера Kubernetes в файл ~/.kube/config.

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

На этом этапе все должно быть готово для доступа к кластеру с компьютера. Попробуйте выполнить следующую команду.
```console
kubectl get nodes
```

Проверьте, отображается ли список компьютеров в кластере.

## <a name="create-your-first-kubernetes-service"></a>Создание первой службы Kubernetes

Выполнив инструкции из этого руководства, вы научитесь:
 * развертывать приложение Docker и предоставлять к нему доступ;
 * использовать `kubectl exec` для выполнения команд в контейнере; 
 * получать доступ к панели мониторинга Kubernetes.

### <a name="start-a-simple-container"></a>Запуск простого контейнера
Чтобы запустить простой контейнер (в данном случае веб-сервер Nginx), выполните приведенную ниже команду.

```console
kubectl run nginx --image nginx
```

Эта команда запускает контейнер Docker Nginx в pod на одном из узлов.

Чтобы просмотреть запущенный контейнер, выполните приведенную команду.

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Предоставление доступа к службе
Чтобы сделать службу доступной по всему миру, создайте `Service` Kubernetes типа `LoadBalancer`.

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Тогда Kubernetes создаст правило Azure Load Balancer с общедоступным IP-адресом. Распространение изменений в подсистеме балансировки нагрузки занимает несколько минут. Дополнительные сведения см. в разделе [Балансировка нагрузки контейнеров в кластере Kubernetes в Службе контейнеров Azure](container-service-kubernetes-load-balancing.md).

Выполните следующую команду, чтобы посмотреть, как изменится состояние службы `pending` и отобразится внешний IP-адрес.

```console
watch 'kubectl get svc'
```

  ![Изображение смены состояния ожидания на использование внешнего IP-адреса](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Полученный внешний IP-адрес можно ввести в браузере.

  ![Изображение перехода на страницу Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Обзор пользовательского интерфейса Kubernetes
Чтобы просмотреть веб-интерфейс Kubernetes, можно использовать:

```console
kubectl proxy
```
Эта команда запускает простой проверенный прокси-сервер на локальном узле. Его можно использовать для просмотра пользовательского веб-интерфейса Kubernetes здесь: [http://localhost:8001/ui](http://localhost:8001/ui). Дополнительные сведения см. в разделе [Использование веб-интерфейса Kubernetes со Службой контейнеров Azure](container-service-kubernetes-ui.md).

![Изображение панели мониторинга Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Удаленные сеансы в контейнерах
Kubernetes позволяет выполнять команды в удаленном контейнере Docker, запущенном в кластере.

```console
# Get the name of your nginx pods
kubectl get pods
```

Используя имя модуля, можно запустить удаленную команду в модуле.  Например:

```console
kubectl exec <pod name> date
```

Кроме того, можно получить полностью интерактивный сеанс с помощью флагов `-it`:

```console
kubectl exec <pod name> -it bash
```

![Удаленный сеанс внутри контейнера](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Дальнейшие действия

Чтобы расширить возможности работы с кластером Kubernetes, воспользуйтесь приведенными ниже ресурсами.

* В [учебном курсе по Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) описано, как выполнять развертывание, масштабирование, обновление и отладку контейнерных приложений.
* В [руководстве по Kubernetes](http://kubernetes.io/docs/user-guide/) показано, как выполнять команды в существующем кластере Kubernetes.
* На странице с [примерами Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) представлены примеры запуска реальных приложений с использованием Kubernetes.

