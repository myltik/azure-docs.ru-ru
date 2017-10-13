---
title: "Руководство по службе контейнеров Azure — мониторинг Kubernetes | Документация Майкрософт"
description: "Руководство по службе контейнеров Azure — мониторинг Kubernetes с помощью Microsoft Operations Management Suite (OMS)"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: ebf35877dcd6f980af75f46b437070a6a29b7c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Мониторинг кластера Kubernetes с помощью Operations Management Suite

Мониторинг кластера Kubernetes и контейнеров крайне важен, особенно в том случае, если вы управляете масштабируемым рабочим кластером с несколькими приложениями. 

Вы можете воспользоваться преимуществами нескольких решений для мониторинга Kubernetes от корпорации Майкрософт или других поставщиков. В этом руководстве выполняется мониторинг кластера Kubernetes с помощью решения "Контейнеры" в [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md). Это облачное решение Майкрософт для управления ИТ-средой. (Решение "Контейнеры" OMS находится в предварительной версии.)

В этом руководстве, (часть 7 из 7) рассматриваются следующие задачи:

> [!div class="checklist"]
> * Получение параметров рабочей области OMS.
> * Настройка агентов OMS на узлах Kubernetes.
> * Доступ к данным мониторинга на портале OMS или на портале Azure.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образы контейнеров, образы были отправлены в реестр контейнеров Azure и был создан кластер Kubernetes. 

Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="get-workspace-settings"></a>Получение параметров рабочей области

На [портале OMS](https://mms.microsoft.com) последовательно выберите **Параметры** > **Подключенные источники** > **Серверы Linux**. Здесь можно найти *идентификатор рабочей области*, а также основной и дополнительный *ключ рабочей области*. Запишите эти значения, так как они понадобятся для настройки агентов OMS в кластере.

## <a name="set-up-oms-agents"></a>Настройка агентов OMS

Ниже приведен файл YAML для настройки агентов OMS на узлах кластера Linux. Он создает [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes, который запускает идентичный модуль на каждом узле кластера. Ресурс DaemonSet идеально подходит для развертывания агента мониторинга. 

Сохраните следующий текст в файл с именем `oms-daemonset.yaml` и замените значения заполнителей *myWorkspaceID* и *myWorkspaceKey* на идентификатор и ключ рабочей области OMS. (В рабочей среде можно закодировать эти значения в виде секретов.)

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP 
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log 
          name: host-log
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
```

Создайте DaemonSet с помощью следующей команды:

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

Чтобы убедиться, что DaemonSet был создан, выполните следующую команду:

```azurecli-interactive
kubectl get daemonset
```

Результат аналогичен приведенному ниже:

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

Через несколько минут после запуска агентов OMS начнет принимать и обрабатывать данные.

## <a name="access-monitoring-data"></a>Доступ к данным мониторинга

Просматривать и анализировать данные мониторинга контейнера OMS можно с помощью [решения "Контейнер"](../../log-analytics/log-analytics-containers.md) на портале Azure или на портале OMS. 

Для установки решения "Контейнер" с помощью [портала OMS](https://mms.microsoft.com) перейдите в **Коллекция решений**. Затем добавьте **решение "Контейнер"**. Кроме того, можно добавить решение "Контейнеры" из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

На портале OMS найдите плитку сводки **Контейнеры** на панели мониторинга OMS. Щелкните плитку, чтобы просмотреть дополнительные сведения, в том числе события контейнера, ошибки, состояние, список образов, использование ЦП и памяти. Более детализированные сведения можно получить, щелкнув строку на одной из плиток или выполнив [поиск по журналам](../../log-analytics/log-analytics-log-searches.md).

![Панель мониторинга "Контейнеры" на портале OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

На портале Azure перейдите к **Log Analytics** и выберите имя рабочей области. Чтобы увидеть плитку сводки **Контейнеры**, щелкните **Решения** > **Контейнеры**. Чтобы просмотреть сведения, щелкните плитку.

Подробные сведения о создании запросов и анализе данных мониторинга см. в [документации по Log Analytics](../../log-analytics/index.yml).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили мониторинг кластера Kubernetes с помощью OMS, в частности такие задачи:

> [!div class="checklist"]
> * Получение параметров рабочей области OMS.
> * Настройка агентов OMS на узлах Kubernetes.
> * Доступ к данным мониторинга на портале OMS или на портале Azure.


Чтобы увидеть предварительно созданные примеры сценариев для службы контейнеров, перейдите по ссылке ниже.

> [!div class="nextstepaction"]
> [Примеры Azure CLI для службы контейнеров Azure](cli-samples.md)
