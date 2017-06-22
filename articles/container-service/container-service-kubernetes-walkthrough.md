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
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 0604a85192ed632b621113b98cc44172c584ea01
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Приступая к работе с кластером Kubernetes в службе контейнеров Azure


В этом пошаговом руководстве показано, как с помощью команд Azure CLI 2.0 создать кластер Kubernetes в службе контейнеров Azure. После этого можно использовать программу командной строки `kubectl`, чтобы приступить к работе с контейнерами в кластере.

На следующем рисунке показана архитектура кластера службы контейнеров с одним главным узлом и двумя агентами Linux. Основной узел обслуживает REST API кластера Kubernetes. Узлы агента группируются в группы доступности Azure. На них запускаются контейнеры. Все виртуальные машины размещены в одной частной виртуальной сети и полностью доступны друг для друга.

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-walkthrough/kubernetes.png)

Дополнительные сведения см. в статье [Общие сведения о решениях для размещения контейнера Docker с помощью службы контейнеров Azure](container-service-intro.md) и [документации Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Предварительные требования
Чтобы создать кластер Службы контейнеров Azure с помощью Azure CLI 2.0, необходимо следующее:
* учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/));
* установленный и настроенный [Azure CLI 2.0](/cli/azure/install-az-cli2).

Кроме того, вам понадобится (или вы можете использовать) Azure CLI, чтобы автоматически создать кластер Службы контейнеров Azure во время развертывания кластера.

* **Открытый ключ SSH RSA**. Если вы хотите заранее создать ключи SSH RSA, ознакомьтесь с руководствами [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](../virtual-machines/linux/mac-create-ssh-keys.md) и [Использование ключей SSH с Windows в Azure](../virtual-machines/linux/ssh-from-windows.md). 

* **Секрет и идентификатор клиента субъекта-службы**. Инструкции по созданию субъекта-службы Azure Active Directory см. в статье [Сведения о субъекте-службе Azure Active Directory для кластера Kubernetes в службе контейнеров Azure](container-service-kubernetes-service-principal.md).

 Пример команды в этой статье автоматически создает ключи SSH и субъект-службу.

## <a name="create-your-kubernetes-cluster"></a>Создание кластера Kubernetes

Ниже кратко описаны команды оболочки, использующие Azure CLI 2.0 для создания кластера. 

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Чтобы создать кластер, вам сначала необходимо создать группу ресурсов в расположении, в котором [доступна](https://azure.microsoft.com/regions/services/) Служба контейнеров Azure. Выполните команды, как показано ниже.

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Создание кластера
Создайте кластер Kubernetes в группе ресурсов с помощью команды `az acs create` с `--orchestrator-type=kubernetes`. Синтаксис команды см. в [документации](/cli/azure/acs#create) по `az acs create`.

Эта версия команды автоматически создает ключи SSH RSA и субъект-службу для кластера Kubernetes.



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

Через несколько минут команда будет выполнена, и вы получите рабочий кластер Kubernetes.

> [!IMPORTANT]
> Если у учетной записи нет разрешений на создание субъекта-службы Azure AD, команда вернет похожее сообщение об ошибке: **Недостаточно привилегий для выполнения операции**. Дополнительные сведения см. в статье [о субъекте-службе для кластера Kubernetes](container-service-kubernetes-service-principal.md).
> 



### <a name="connect-to-the-cluster"></a>Подключение к кластеру

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/) — клиент командной строки Kubernetes. 

Установите `kubectl` с помощью команды `az acs kubernetes install-cli`, если вы еще этого не сделали. (Этот клиент также можно скачать с [сайта Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).)

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> По умолчанию эта команда устанавливает двоичный файл `kubectl` в каталоге `/usr/local/bin/kubectl` в системе Linux и macOS или в каталоге `C:\Program Files (x86)\kubectl.exe` в Windows. Чтобы указать другой путь установки, используйте параметр `--install-location`.
>
> После установки файла `kubectl` убедитесь, что его каталог расположен в системном пути или добавьте его в этот путь. 
>


Затем выполните приведенную ниже команду, чтобы скачать конфигурацию главного кластера Kubernetes в файл `~/.kube/config`.

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Дополнительные варианты установки и настройки `kubectl` см. в статье [Создание удаленного подключения к кластеру Kubernetes, DC/OS или Docker Swarm](container-service-connect.md).

На этом этапе все должно быть готово для доступа к кластеру с компьютера. Попробуйте выполнить следующую команду.

```bash
kubectl get nodes
```

Проверьте, отображается ли список компьютеров в кластере.

## <a name="create-your-first-kubernetes-service"></a>Создание первой службы Kubernetes

Выполнив инструкции из этого руководства, вы научитесь:
* развертывать приложение Docker и предоставлять к нему доступ;
* использовать `kubectl exec` для выполнения команд в контейнере; 
* получать доступ к панели мониторинга Kubernetes.

### <a name="start-a-container"></a>Запуск контейнера
Чтобы запустить контейнер (в данном случае веб-сервер Nginx), выполните приведенную ниже команду.

```bash
kubectl run nginx --image nginx
```

Эта команда запускает контейнер Docker Nginx в pod на одном из узлов.

Чтобы просмотреть запущенный контейнер, выполните приведенную команду.

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>Предоставление доступа к службе
Чтобы сделать службу доступной по всему миру, создайте `Service` Kubernetes типа `LoadBalancer`.

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

В результате выполнения этой команды Kubernetes создаст правило Azure Load Balancer с общедоступным IP-адресом. Распространение изменений в подсистеме балансировки нагрузки занимает несколько минут. Дополнительные сведения см. в разделе [Балансировка нагрузки контейнеров в кластере Kubernetes в Службе контейнеров Azure](container-service-kubernetes-load-balancing.md).

Выполните следующую команду, чтобы посмотреть, как изменится состояние службы `pending` и отобразится внешний IP-адрес.

```bash
watch 'kubectl get svc'
```

  ![Изображение смены состояния ожидания на использование внешнего IP-адреса](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Полученный внешний IP-адрес можно ввести в браузере.

  ![Изображение перехода на страницу Nginx](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Обзор пользовательского интерфейса Kubernetes
Чтобы просмотреть веб-интерфейс Kubernetes, можно использовать:

```bash
kubectl proxy
```
Эта команда запускает проверенный прокси-сервер на локальном узле. Его можно использовать для просмотра пользовательского веб-интерфейса Kubernetes здесь: [http://localhost:8001/ui](http://localhost:8001/ui). Дополнительные сведения см. в разделе [Использование веб-интерфейса Kubernetes со Службой контейнеров Azure](container-service-kubernetes-ui.md).

![Изображение панели мониторинга Kubernetes](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Удаленные сеансы в контейнерах
Kubernetes позволяет выполнять команды в удаленном контейнере Docker, запущенном в кластере.

```bash
# Get the name of your nginx pods
kubectl get pods
```

Используя имя модуля, можно запустить удаленную команду в модуле. Например:

```bash
kubectl exec <pod name> date
```

Кроме того, можно получить полностью интерактивный сеанс с помощью флагов `-it`:

```bash
kubectl exec <pod name> -it bash
```

![Удаленный сеанс внутри контейнера](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Дальнейшие действия

Чтобы расширить возможности работы с кластером Kubernetes, воспользуйтесь приведенными ниже ресурсами.

* В [учебном курсе по Kubernetes](https://katacoda.com/embed/kubernetes-bootcamp/1/) описано, как выполнять развертывание, масштабирование, обновление и отладку контейнерных приложений.
* В [руководстве по Kubernetes](http://kubernetes.io/docs/user-guide/) показано, как выполнять команды в существующем кластере Kubernetes.
* На странице с [примерами Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples) представлены примеры запуска реальных приложений с использованием Kubernetes.

