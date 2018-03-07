---
title: "Вход через SSH в узлы кластера Службы контейнеров Azure (AKS)"
description: "Создание SSH-соединения с узлами кластера Службы контейнеров Azure (AKS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>Вход через SSH в узлы кластера Службы контейнеров Azure (AKS)

В некоторых случаях может потребоваться доступ к узлу Службы контейнеров Azure (AKS) для обслуживания, сбора данных журналов или других операций по устранению неполадок. Узлы Службы контейнеров Azure (AKS) не доступны через Интернет. Чтобы создать SSH-соединение с узлом AKS, выполните действия, описанные в этом документе.

## <a name="configure-ssh-access"></a>Настройка доступа SSH

 Для входа через SSH в конкретный узел создается pod с доступом `hostNetwork`. Также создается служба для доступа к pod. Эта конфигурация является привилегированной и после использования ее следует удалить.

Создайте файл с именем `aks-ssh.yaml` и скопируйте в него следующий манифест. В качестве имени узла (nodeName) укажите имя целевого узла AKS.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

Запустите манифест, чтобы создать pod и службу.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

Получите внешний IP-адрес предоставленной службы. Завершение настройки IP-адреса может занять до минуты. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Создайте SSH-соединение. 

Имя пользователя по умолчанию для кластера AKS — `azureuser`. Если эта учетная запись изменена во время создания кластера, укажите правильное имя пользователя-администратора. 

Если ключ находится не в `~/ssh/id_rsa`, укажите правильное расположение с помощью аргумента `ssh -i`.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Отключение доступа по протоколу SSH

По завершении удалите pod и службу для доступа по протоколу SSH.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```