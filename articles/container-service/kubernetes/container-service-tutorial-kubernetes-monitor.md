---
title: Руководство по службе контейнеров Azure. Мониторинг Kubernetes
description: Руководство по службе контейнеров Azure. Мониторинг Kubernetes с помощью Log Analytics
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 17398a9f74e40a7d513912d654fa609d9837d805
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165414"
---
# <a name="monitor-a-kubernetes-cluster-with-log-analytics"></a>Мониторинг кластера Kubernetes с помощью Log Analytics

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Мониторинг кластера Kubernetes и контейнеров крайне важен, особенно в том случае, если вы управляете масштабируемым рабочим кластером с несколькими приложениями.

Вы можете воспользоваться преимуществами нескольких решений для мониторинга Kubernetes от корпорации Майкрософт или других поставщиков. В этом руководстве выполняется мониторинг кластера Kubernetes с помощью решения "Контейнеры" в [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md). Это облачное решение Майкрософт для управления ИТ-средой. (Решение "Контейнеры" OMS доступно в режиме предварительной версии.)

В этом руководстве, (часть 7 из 7) рассматриваются следующие задачи:

> [!div class="checklist"]
> * Получение параметров рабочей области Log Analytics
> * настройка агентов Log Analytics на узлах Kubernetes;
> * доступ к данным мониторинга на портале Log Analytics или на портале Azure.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образы контейнеров, образы были отправлены в реестр контейнеров Azure и был создан кластер Kubernetes.

Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./container-service-tutorial-kubernetes-prepare-app.md).

## <a name="get-workspace-settings"></a>Получение параметров рабочей области

На [портале Log Analytics](https://mms.microsoft.com) последовательно выберите **Параметры** > **Подключенные источники** > **Серверы с Linux**. Здесь можно найти *идентификатор рабочей области*, а также основной и дополнительный *ключ рабочей области*. Запишите эти значения, так как они понадобятся для настройки агентов Log Analytics в кластере.

## <a name="create-kubernetes-secret"></a>Создание секрета Kubernetes

Сохраните параметры рабочей области Log Analytics в секрете Kubernetes `omsagent-secret` с помощью команды [kubectl create secret][kubectl-create-secret]. Укажите для `WORKSPACE_ID` идентификатор рабочей области Log Analytics, а для `WORKSPACE_KEY` — ключ рабочей области.

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Настройка агентов Log Analytics

Следующий файл манифеста Kubernetes можно использовать для настройки агентов мониторинга контейнеров в кластере Kubernetes. Он создает [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes, который запускает идентичный модуль на каждом узле кластера.

Сохраните следующий текст в файле с именем `oms-daemonset.yaml`.

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
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
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
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
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

Через несколько минут после запуска агентов Log Analytics начнет принимать и обрабатывать данные.

## <a name="access-monitoring-data"></a>Доступ к данным мониторинга

Просматривать и анализировать данные мониторинга контейнера можно с помощью [решения "Контейнер"](../../log-analytics/log-analytics-containers.md) на портале Azure или на портале Log Analytics.

Для установки решения "Контейнер" с помощью [портала Log Analytics](https://mms.microsoft.com) перейдите к разделу **Коллекция решений**. Затем добавьте **решение "Контейнер"**. Кроме того, можно добавить решение "Контейнеры" из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview).

На портале Log Analytics найдите плитку сводки **Контейнеры** на панели мониторинга. Щелкните плитку, чтобы просмотреть дополнительные сведения, в том числе события контейнера, ошибки, состояние, список образов, использование ЦП и памяти. Более детализированные сведения можно получить, щелкнув строку на одной из плиток или выполнив [поиск по журналам](../../log-analytics/log-analytics-log-searches.md).

![Панель мониторинга "Контейнеры" на портале OMS](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

На портале Azure перейдите к **Log Analytics** и выберите имя рабочей области. Чтобы увидеть плитку сводки **Контейнеры**, щелкните **Решения** > **Контейнеры**. Чтобы просмотреть сведения, щелкните плитку.

Подробные сведения о создании запросов и анализе данных мониторинга см. в [документации по Log Analytics](../../log-analytics/index.yml).

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили мониторинг кластера Kubernetes с помощью Log Analytics, выполнив в частности такие задачи:

> [!div class="checklist"]
> * Получение параметров рабочей области Log Analytics
> * настройка агентов Log Analytics на узлах Kubernetes;
> * доступ к данным мониторинга на портале Log Analytics или на портале Azure.


Чтобы увидеть предварительно созданные примеры сценариев для службы контейнеров, перейдите по ссылке ниже.

> [!div class="nextstepaction"]
> [Примеры Azure CLI для службы контейнеров Azure](cli-samples.md)
