---
title: Мониторинг кластера Azure Kubernetes с помощью Operations Management
description: Мониторинг кластера Kubernetes в Службе контейнеров Azure с помощью Log Analytics
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 3b014ce4c91d1dc9fae744ef4b528c98f9f787b3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32164325"
---
# <a name="monitor-an-azure-container-service-cluster-with-log-analytics"></a>Мониторинг кластера службы контейнеров Azure с помощью Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>предварительным требованиям
В этом пошаговом руководстве предполагается, что вы [создали кластер Kubernetes с помощью службы контейнеров Azure](container-service-kubernetes-walkthrough.md).

Также предполагается, что у вас установлен интерфейс командной строки Azure `az` и инструменты `kubectl`.

Чтобы проверить наличие средства `az`, выполните такую команду:

```console
$ az --version
```

Если средство `az` не установлено, следуйте инструкциям, приведенным [здесь](https://github.com/azure/azure-cli#installation).
Кроме того, можно использовать [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), имеющий средства Azure CLI `az` и `kubectl`, которые уже установлены.

Чтобы проверить наличие средства `kubectl`, выполните такую команду:

```console
$ kubectl version
```

Если средство `kubectl` не установлено, выполните команду:
```console
$ az acs kubernetes install-cli
```

Чтобы проверить, установлены ли ключи kubernetes в средстве kubectl, выполните следующую команду:
```console
$ kubectl get nodes
```

Если появились ошибки команды, необходимо установить ключи кластера kubernetes в средство kubectl. Это можно сделать с помощью следующей команды:
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Мониторинг контейнеров с помощью Log Analytics

Log Analytics — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее. В Log Analytics реализовано решение для контейнеров, которое помогает просматривать сведения, касающиеся инвентаризации и производительности контейнеров, а также соответствующие журналы в одном расположении. Оно позволяет выполнять аудит и устранять неполадки контейнеров, просматривая журналы в централизованном расположении, а также находить контейнеры с высоким уровнем потребления ресурсов на узле.

![](media/container-service-monitoring-oms/image1.png)

Дополнительные сведения об этом решении см. в статье [Решение "Контейнеры" (предварительная версия) в Log Analytics](../../log-analytics/log-analytics-containers.md).

## <a name="installing-log-analytics-on-kubernetes"></a>Установка Log Analytics в Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Получение идентификатора и ключа рабочей области
Чтобы агент Log Analytics мог взаимодействовать со службой, для него нужно настроить идентификатор и ключ рабочей области. Чтобы получить идентификатор и ключ рабочей области, необходимо создать учетную запись OMS по адресу <https://mms.microsoft.com>.
Следуйте инструкциям, чтобы создать учетную запись. После создания учетной записи необходимо получить идентификатор и ключ, щелкнув **Параметры**, **Подключенные источники**, а затем — **Серверы с Linux**, как показано ниже.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>Установка агента Log Analytics с помощью DaemonSet
Kubernetes использует наборы DaemonSet для выполнения отдельного экземпляра контейнера на каждом узле в кластере.
Они идеально подходят для выполнения агентов мониторинга.

Ниже приведен [YAML-файл DaemonSet](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Сохраните его под именем `oms-daemonset.yaml` и замените в нем значения заполнителей `WSID` и `KEY` идентификатором и ключом рабочей области.

После добавления идентификатора и ключа рабочей области в конфигурацию DaemonSet вы можете установить агент Log Analytics в кластере, воспользовавшись программой командной строки `kubectl`.

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Установка агента Log Analytics с помощью секрета Kubernetes
Для защиты идентификатора и ключа рабочей области Log Analytics можно использовать секрет Kubernetes как часть YAML-файла DaemonSet.

 - Скопируйте скрипт, файл шаблона секретов и YAML-файл DaemonSet (из [репозитория](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)) и убедитесь, что они находятся в одном и том же каталоге.
      - Сценарий создания секретов — secret-gen.sh.
      - Шаблон секретов — secret-template.yaml.
   - YAML-файл DaemonSet — omsagent-ds-secrets.yaml.
 - Выполните скрипт. Сценарий будет запрашивать идентификатор и первичный ключ рабочей области Log Analytics. Вставьте их, и скрипт создаст YAML-файл секрета, который можно запустить.
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - Создайте модуль секретов, выполнив следующую команду: ``` kubectl create -f omsagentsecret.yaml ```

   - Чтобы проверить, выполните следующую команду:

   ```
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes
   ```

  - Создайте набор daemon-set omsagent, выполнив команду ``` kubectl create -f omsagent-ds-secrets.yaml ```

### <a name="conclusion"></a>Заключение
Вот и все! Через несколько минут можно будет увидеть, как в панель мониторинга Log Analytics поступает поток данных.
