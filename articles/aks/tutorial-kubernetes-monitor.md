---
title: "Руководство по Kubernertes в Azure. Мониторинг Kubernetes"
description: "Руководство AKS. Мониторинг Kubernetes с помощью Microsoft Operations Management Suite (OMS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 084c6bf3855bdc757c3f2926b35eaf7bba0ef389
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-azure-container-service-aks"></a>Мониторинг Службы контейнеров Azure (AKS)

Мониторинг кластера Kubernetes и контейнеров крайне важен, особенно в том случае, если вы управляете рабочим кластером в нужном масштабе с несколькими приложениями.

В этом руководстве описывается настройка мониторинга кластера AKS с помощью [решения "Контейнеры" для Log Analytics](../log-analytics/log-analytics-containers.md).

В этом руководстве (часть семь из восьми) рассматриваются следующие задачи:

> [!div class="checklist"]
> * настройка решения мониторинга контейнеров;
> * настройка агентов мониторинга;
> * доступ к данным мониторинга на портале Azure.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образы контейнеров, образы были отправлены в реестр контейнеров Azure и был создан кластер Kubernetes.

Если вы не выполнили эти действия, вы можете ознакомиться со статьей [Создание образов контейнеров для использования со службой контейнеров Azure](./tutorial-kubernetes-prepare-app.md).

## <a name="configure-the-monitoring-solution"></a>Настройка решения для мониторинга

На портале Azure щелкните **Создать** и выполните поиск `Container Monitoring Solution`. Найдя это решение, щелкните **Создать**.

![Добавление решения](./media/container-service-tutorial-kubernetes-monitor/add-solution.png)

Создайте новую рабочую область OMS или выберите существующую. Выполнить этот процесс поможет форма рабочей области OMS.

При создании рабочей области выберите **Закрепить на панели мониторинга**, чтобы упростить получение информации.

![Рабочая область OMS](./media/container-service-tutorial-kubernetes-monitor/oms-workspace.png)

Когда все будет готово, нажмите кнопку **ОК**. После завершения проверки щелкните **Создать**, чтобы создать решение мониторинга контейнеров.

После создания рабочей области она появится на портале Azure.

## <a name="get-workspace-settings"></a>Получение параметров рабочей области

Идентификатор и ключ рабочей области Log Analytics необходимы для настройки агента решения на узлах Kubernetes.

Чтобы получить эти значения, выберите **Рабочая область OMS** в меню слева для решений контейнеров. Выберите **Дополнительные параметры** и запишите значения **Идентификатор рабочей области** и **Первичный ключ**.

## <a name="configure-monitoring-agents"></a>Настройка агентов мониторинга

Следующий файл манифеста Kubernetes можно использовать для настройки агентов мониторинга контейнеров в кластере Kubernetes. Он создает [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) Kubernetes, который запускает отдельный pod на каждом узле кластера.

Сохраните следующий текст в файл `oms-daemonset.yaml` и замените значения заполнителей `WSID` и `KEY` идентификатором и ключом рабочей области Log Analytics.

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
    agentVersion: 1.4.0-12
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: <WSID>
       - name: KEY
         value: <KEY>
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
        - mountPath: /var/opt/microsoft/omsagent/state/containerhostname
          name: container-hostname
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
    - name: container-hostname
      hostPath:
       path: /etc/hostname
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

```
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR                 AGE
omsagent   3         3         3         3            3           beta.kubernetes.io/os=linux   8m
```

Через несколько минут после запуска агентов OMS начнет принимать и обрабатывать данные.

## <a name="access-monitoring-data"></a>Доступ к данным мониторинга

На портале Azure выберите рабочую область Log Analytics, закрепленную на панели мониторинга. Щелкните элемент **Решение мониторинга контейнеров**. Здесь можно найти сведения о кластере AKS и контейнерах в этом кластере.

![Панель мониторинга](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

Подробные сведения о создании запросов и анализе данных мониторинга см. в [документации по Log Analytics](../log-analytics/index.yml).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили мониторинг кластера Kubernetes с помощью OMS, в частности такие задачи:

> [!div class="checklist"]
> * настройка решения мониторинга контейнеров;
> * настройка агентов мониторинга;
> * доступ к данным мониторинга на портале Azure.

Перейдите к следующему руководству, чтобы узнать об обновлении Kubernetes до новой версии.

> [!div class="nextstepaction"]
> [Обновление Kubernetes в Службе контейнеров Azure (AKS)](./tutorial-kubernetes-upgrade-cluster.md)