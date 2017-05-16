---
title: "Кластер Azure Kubernetes для Windows | Документация Майкрософт"
description: "Развертывание кластера Kubernetes для контейнеров Windows в Службе контейнеров Azure и начало работы с ним."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: ru-ru
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Приступая к работе с кластером Kubernetes и контейнерами Windows в службе контейнеров Azure


В этой статье показано, как в службе контейнеров Azure создать кластер Kubernetes, содержащий узлы Windows для запуска контейнеров Windows. Начните с команды Azure CLI 2.0 `az acs`, чтобы создать кластер Kubernetes в службе контейнеров Azure. Затем с помощью программы командной строки Kubernetes `kubectl` начните работу с контейнерами Windows, созданными из образов Docker. 

> [!NOTE]
> Поддержка контейнеров Windows для Kubernetes в Службе контейнеров Azure находится в на этапе предварительной версии. 
>



На следующем рисунке показана архитектура кластера Kubernetes в Службе контейнеров Azure с одним главным узлом Linux и двумя узлами агента Windows. 

![Схема кластера Kubernetes в Аzure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* Главный узел Linux обслуживает REST API Kubernetes. К нему можно получить доступ по протоколу SSH через порт 22 или с помощью `kubectl` через порт 443. 
* Узлы агента Windows группируются в группы доступности Azure. На них запускаются контейнеры. Доступ к узлам Windows можно получить с помощью туннеля SSH (RDP) через главный узел. Правила Azure Load Balancer добавляются в кластер динамически, в зависимости от предоставляемых служб.



Все виртуальные машины размещены в одной частной виртуальной сети и полностью доступны друг для друга. На всех виртуальных машинах выполняются kubelet, Docker и прокси-сервер.

Дополнительные сведения см. в статье [Общие сведения о решениях для размещения контейнера Docker с помощью службы контейнеров Azure](container-service-intro.md) и [документации Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Предварительные требования
Чтобы создать кластер Службы контейнеров Azure с помощью Azure CLI 2.0, необходимо следующее:
* учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/));
* вход в установленный интерфейс [Azure CLI 2.0](/cli/azure/install-az-cli2).

Кроме того, вам понадобятся следующие компоненты для кластера Kubernetes. Эти компоненты можно подготовить заранее или использовать параметры команды `az acs create`, чтобы создать их автоматически во время развертывания кластера. 

* **Открытый ключ SSH RSA.** Инструкции по созданию ключей RSA (SSH) см. в соответствующих статьях для [OS X и Linux](../virtual-machines/linux/mac-create-ssh-keys.md), а также [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Секрет и идентификатор клиента субъекта-службы.** Инструкции по созданию субъекта-службы Azure Active Directory см. в статье [Сведения о субъекте-службе Azure Active Directory для кластера Kubernetes в службе контейнеров Azure](container-service-kubernetes-service-principal.md).

Пример команды в этой статье автоматически создает ключи SSH и субъект-службу.
  
## <a name="create-your-kubernetes-cluster"></a>Создание кластера Kubernetes

Ниже приведены команды Azure CLI 2.0 для создания кластера. 

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов в расположении, где будет [доступна](https://azure.microsoft.com/regions/services/) служба контейнеров Azure. Следующая команда создает группу ресурсов *myKubernetesResourceGroup* в расположении *westus*.

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Создание кластера Kubernetes с узлами агента Windows

Создайте кластер Kubernetes в группе ресурсов с помощью команды `az acs create` с `--orchestrator-type=kubernetes` и параметром агента `--windows`. Синтаксис команды см. [документации](/cli/azure/acs#create) по `az acs create`.

Следующая команда создает кластер службы контейнеров с именем *myKubernetesClusterName* и префиксом DNS *myPrefix*, чтобы узел управления и указанные учетные данные могли охватить узлы Windows. Эта версия команды автоматически создает ключи SSH RSA и субъект-службу для кластера Kubernetes.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Через несколько минут команда будет выполнена, и вы получите рабочий кластер Kubernetes.

> [!IMPORTANT]
> Если у вашей учетной записи нет разрешения на создание субъекта-службы Azure AD, команда создаст сообщение об ошибке: `Insufficient privileges to complete the operation.`. Дополнительные сведения см. в статье [Сведения о субъекте-службе Azure Active Directory для кластера Kubernetes в службе контейнеров Azure](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Подключение к кластеру с помощью kubectl

Для подключения к кластеру Kubernetes с клиентского компьютера используйте [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), клиент командной строки Kubernetes. 

Установите `kubectl` с помощью команды `az acs kubernetes install-cli`, если вы еще это не сделали. (Этот клиент также можно скачать с [сайта Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).)

**Linux и macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> По умолчанию эта команда устанавливает двоичный файл `kubectl` в каталоге `/usr/local/bin/kubectl` в системе Linux и macOS или в каталоге `C:\Program Files (x86)\kubectl.exe` в Windows. Чтобы указать другой путь установки, используйте параметр `--install-location`.
>
> После установки клиента `kubectl` убедитесь, что его каталог расположен в системном пути, или добавьте его в этот путь. 


Затем выполните следующую команду, чтобы скачать конфигурацию главного кластера Kubernetes в локальный файл `~/.kube/config`.

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

Теперь все готово для получения доступа к кластеру с компьютера. Попробуйте выполнить следующую команду.

```bash
kubectl get nodes
```

Проверьте, отображается ли список компьютеров в кластере.

![Узлы, работающие в кластере Kubernetes](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Создание первой службы Kubernetes

После создания кластера и подключения к нему с помощью `kubectl` можно попытаться запустить приложение Windows из контейнера Docker и предоставить его через Интернет. В этом простом примере используется JSON-файл, чтобы определить контейнер Microsoft Internet Information Server (IIS), а затем создать его с помощью `kubctl apply`. 

1. Создайте локальный файл `iis.json` и скопируйте в него следующее содержимое. Этот файл дает Kubernetes инструкцию запустить службы IIS в Windows Server 2016 Server Core с помощью открытого образа из [репозитория Docker](https://hub.docker.com/r/microsoft/iis/). Контейнер использует порт 80, но изначально он доступен только в сети кластера.

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
          "image": "microsoft/iis",
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
2. Чтобы запустить приложение, ведите приведенную ниже команду.  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Чтобы отслеживать развертывание контейнера, введите следующую команду:  
  ```bash
  kubectl get pods
  ```
  Во время развертывания контейнер находится в состоянии `ContainerCreating`. 

  ![Состояние ContainerCreating контейнера IIS](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  Из-за размера образа IIS переход контейнера в состояние `Running` может длиться несколько минут.

  ![Контейнер IIS в рабочем состоянии](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Чтобы предоставить общий доступ к контейнеру в Интернете, введите следующую команду:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  В результате выполнения этой команды Kubernetes создаст правило Azure Load Balancer с общедоступным IP-адресом. Распространение изменений в подсистеме балансировки нагрузки занимает несколько минут. Дополнительные сведения см. в статье [Балансировка нагрузки контейнеров в кластере Kubernetes в Службе контейнеров Azure](container-service-kubernetes-load-balancing.md).

5. Выполните следующую команду, чтобы просмотреть состояние службы.

  ```bash
  kubectl get svc
  ```

  Изначально IP-адрес выглядит как `pending`:

  ![Внешний IP-адрес в состоянии ожидания](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  Через несколько минут IP-адрес получает это значение:
  
  ![Внешний IP-адрес для IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. После этого внешний IP-адрес можно ввести в браузере.

  ![Изображение перехода к IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Чтобы удалить pod IIS, введите:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы использовать пользовательский интерфейс Kubernetes, выполните команду `kubectl proxy`. Затем перейдите по адресу http://localhost:8001/ui.

* Инструкции по созданию настраиваемого веб-сайта IIS и его запуску в контейнере Windows см. в руководстве на сайте [репозитория Docker](https://hub.docker.com/r/microsoft/iis/).

* Чтобы получить доступ к узлам Windows через туннель RDP SSH к главному узлу с использованием PuTTy, см. [документацию по обработчику ACS](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

