---
title: "Мониторинг кластера Kubernetes в Azure с помощью DataDog | Документация Майкрософт"
description: "Мониторинг кластера Kubernetes в Службе контейнеров Azure с помощью DataDog."
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 40b34457447a8f80d8cdf77579750e0c42df22d0
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Мониторинг кластера службы контейнеров Azure с помощью Datadog

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

## <a name="datadog"></a>Datadog
Datadog представляет собой службу мониторинга, которая собирает данные мониторинга из контейнеров в кластере службы контейнеров Azure. Datadog имеет панель мониторинга интеграции с Docker, в которой вы можете увидеть некоторые метрики своих контейнеров. Метрики контейнеров собраны в несколько групп: ЦП, память, сеть и ввод-вывод. Datadog разделяет метрики по контейнерам и образам.

Необходимо сначала [создать учетную запись](https://www.datadoghq.com/lpg/).

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Установка агента DataDog с помощью DaemonSet
Kubernetes использует наборы DaemonSet для выполнения отдельного экземпляра контейнера на каждом узле в кластере.
Они идеально подходят для выполнения агентов мониторинга.

После входа в DataDog можно следовать [инструкциям к DataDog](https://app.datadoghq.com/account/settings#agent/kubernetes), чтобы установить на кластер агенты DataDog с помощью DaemonSet.

## <a name="conclusion"></a>Заключение
Вот и все! Через несколько минут после того, как агенты будут запущены и начнут работать, вы увидите данные в консоли. Вы можете посетить интегрированную [панель мониторинга Kubernetes](https://app.datadoghq.com/screen/integration/kubernetes), чтобы просмотреть сводку по кластеру.
