---
title: "Мониторинг кластера Azure Kubernetes с помощью Operations Management | Документация Майкрософт"
description: "Мониторинг кластера Kubernetes в Службе контейнеров Azure с помощью Microsoft Operations Management Suite"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 4e4a4f4e299dc2747eb48bbd2e064cd80783211c
ms.openlocfilehash: 46240f3dc99a8c8a103a1e7919ad4f5e7a8ea62a
ms.lasthandoff: 04/04/2017


---

# <a name="monitor-an-azure-container-service-cluster-with-microsoft-operations-management-suite-oms"></a>Мониторинг кластера Службы контейнеров Azure с помощью Microsoft Operations Management Suite (OMS)

## <a name="prerequisites"></a>Предварительные требования
В этом пошаговом руководстве предполагается, что вы [создали кластер Kubernetes с помощью службы контейнеров Azure](container-service-kubernetes-walkthrough.md).

Также предполагается, что у вас установлен интерфейс командной строки Azure `az` и инструменты `kubectl`.

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

## <a name="monitoring-containers-with-operations-management-suite-oms"></a>Мониторинг контейнеров с помощью Operations Management Suite (OMS)

Microsoft Operations Management (OMS) — это облачное решение Майкрософт для управления ИТ-средой, которое помогает управлять локальной и облачной инфраструктурой и защищать ее. Контейнер OMS — это решение в OMS Log Analytics, которое помогает просматривать сведения, касающиеся инвентаризации и производительности контейнеров, а также соответствующие журналы в одном расположении. Оно позволяет выполнять аудит и устранять неполадки контейнеров, просматривая журналы в централизованном расположении, а также находить контейнеры с высоким уровнем потребления ресурсов на узле.

![](media/container-service-monitoring-oms/image1.png)

Дополнительные сведения об этом решении см. в статье [Решение "Контейнеры" (предварительная версия) в Log Analytics](../log-analytics/log-analytics-containers.md).

## <a name="installing-oms-on-kubernetes"></a>Установка OMS в Kubernetes

### <a name="obtain-your-workspace-id-and-key"></a>Получение идентификатора и ключа рабочей области
Чтобы агент OMS мог взаимодействовать со службой, для него нужно настроить идентификатор и ключ рабочей области. Чтобы получить идентификатор и ключ рабочей области, необходимо создать учетную запись OMS, перейдя по адресу <https://mms.microsoft.com>.
Следуйте инструкциям, чтобы создать учетную запись. После создания учетной записи необходимо получить идентификатор и ключ, щелкнув **Параметры**, **Подключенные источники**, а затем — **Linux Servers** (Серверы Linux), как показано ниже.

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-oms-agent-using-a-daemonset"></a>Установка агента OMS с помощью DaemonSet
Kubernetes использует наборы DaemonSet для выполнения отдельного экземпляра контейнера на каждом узле в кластере.
Они идеально подходят для выполнения агентов мониторинга.

Ниже приведен [YAML-файл DaemonSet](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes). Сохраните его под именем `oms-daemonset.yaml` и замените в нем значения заполнителей `WSID` и `KEY` идентификатором и ключом рабочей области.

После добавления идентификатора и ключа рабочей области в конфигурацию DaemonSet вы можете установить агент OMS в кластер, воспользовавшись программой командной строки `kubectl`.

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="conclusion"></a>Заключение
Вот и все! Через несколько минут можно будет увидеть, как в панель мониторинга OMS поступает поток данных.

