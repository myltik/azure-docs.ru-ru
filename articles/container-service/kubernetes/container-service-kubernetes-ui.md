---
title: Управлением кластером Azure Kubernetes с помощью пользовательского веб-интерфейса
description: Использование веб-интерфейса Kubernetes в службе контейнеров Azure
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0680551d3a87c942574a4eac70fa380cc1e9b5d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163254"
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Использование веб-интерфейса Kubernetes со службой контейнеров Azure

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>предварительным требованиям
В этом пошаговом руководстве предполагается, что вы [создали кластер Kubernetes с помощью службы контейнеров Azure](container-service-kubernetes-walkthrough.md).


Также предполагается, что у вас установлен Azure CLI 2.0 и инструменты `kubectl`.

Чтобы проверить наличие средства `az`, выполните такую команду:

```console
$ az --version
```

Если средство `az` не установлено, следуйте инструкциям, приведенным [здесь](https://github.com/azure/azure-cli#installation).

Чтобы проверить наличие средства `kubectl`, выполните такую команду:

```console
$ kubectl version
```

Если средство `kubectl` не установлено, выполните команду:

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>Обзор

### <a name="connect-to-the-web-ui"></a>Подключение к веб-интерфейсу
Для запуска веб-интерфейса Kubernetes используйте такую команду:

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

В результате откроется браузер с настроенным подключением к защищенному прокси-серверу, который установит подключение вашего локального компьютера к веб-интерфейсу Kubernetes.

### <a name="create-and-expose-a-service"></a>Создание и предоставление службы
1. В правом верхнем углу веб-интерфейса Kubernetes нажмите кнопку **Create** (Создать).

    ![Кнопка Create (Создать) в веб-интерфейсе Kubernetes](./media/container-service-kubernetes-ui/create.png)

    При этом откроется диалоговое окно, в котором можно приступить к созданию приложения.

2. Присвойте ему имя `hello-nginx`. Используйте [контейнер `nginx` из Docker](https://hub.docker.com/_/nginx/) и разверните три реплики этой веб-службы.

    ![Диалоговое окно создания модуля Kubernetes](./media/container-service-kubernetes-ui/nginx.png)

3. В разделе **Service** (Служба) выберите вариант **External** (Внешняя) и укажите порт 80.

    Этот параметр балансирует нагрузку между тремя репликами, исходя из трафика.

    ![Диалоговое окно создания службы Kubernetes](./media/container-service-kubernetes-ui/service.png)

4. Щелкните **Deploy** (Развернуть), чтобы развернуть эти контейнеры и службы.

    ![Развертывание Kubernetes](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>Просмотр контейнеров
После нажатия кнопки **Deploy** (Развернуть) в пользовательском интерфейсе отобразится развертывание службы:

![Состояние Kubernetes](./media/container-service-kubernetes-ui/status.png)

В круге в левой части пользовательского интерфейса (в разделе **Pods** (Модули)) отобразится состояние каждого объекта Kubernetes. Если круг не замкнут, развертывание объекта еще не завершено. Когда объект будет полностью развернут, отобразится зеленая галочка:

![Объект Kubernetes развернут](./media/container-service-kubernetes-ui/deployed.png)

Когда все компоненты будут работать, щелкните один из модулей и просмотрите подробные сведения о запущенной веб-службе.

![Модули Kubernetes](./media/container-service-kubernetes-ui/pods.png)

В представлении **Pods** (Модули) можно просмотреть информацию о контейнерах в модуле, а также ресурсах ЦП и памяти, используемых этими контейнерами:

![Ресурсы Kubernetes](./media/container-service-kubernetes-ui/resources.png)

Если ресурсы не отображаются, возможно, следует подождать несколько минут для распространения данных мониторинга.

Чтобы просмотреть журналы для контейнера, щелкните **View logs** (Просмотр журналов).

![Журналы Kubernetes](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>Просмотр службы
Помимо выполнения контейнеров, в пользовательском интерфейсе Kubernetes была создана внешняя служба `Service`, которая подготавливает подсистему балансировки нагрузки для направления трафика на контейнеры в кластере.

Для просмотра всех служб в левой области навигации щелкните элемент **Services** (Службы) (должна отображаться только одна служба).

![Службы Kubernetes](./media/container-service-kubernetes-ui/service-deployed.png)

В этом представлении должна отображаться внешняя конечная точка (IP-адрес), выделенная для службы.
Если щелкнуть этот IP-адрес, то отобразится контейнер Nginx, выполняющийся за подсистемой балансировки нагрузки.

![Представление nginx](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>Изменение размеров службы
Помимо просмотра объектов в пользовательском интерфейсе можно также изменять и обновлять объекты API Kubernetes.

Сначала щелкните в левой области навигации элемент **Deployments** (Развертывания), чтобы просмотреть развертывание службы.

В этом представлении выберите набор реплик и щелкните **Edit** (Изменить) на верхней панели навигации:

![Окно изменения в Kubernetes](./media/container-service-kubernetes-ui/edit.png)

Измените поле `spec.replicas`, указав значение `2`, и щелкните **Update** (Обновить).

В результате один из модулей будет удален, а число реплик уменьшится до двух.

 

