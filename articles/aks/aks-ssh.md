---
title: Вход через SSH в узлы кластера Службы Azure Kubernetes (AKS)
description: Создание SSH-соединения с узлами кластера Службы Azure Kubernetes (AKS)
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 95b385e9847a7809492bbb74bd1eba616df90d72
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164328"
---
# <a name="ssh-into-azure-kubernetes-service-aks-cluster-nodes"></a>Вход через SSH в узлы кластера Службы Azure Kubernetes (AKS)

В некоторых случаях может потребоваться получить доступ к узлу Службы Azure Kubernetes (AKS) для обслуживания, сбора данных журналов или других операций по устранению неполадок. Узлы AKS недоступны через Интернет. Чтобы создать SSH-соединение с узлом AKS, выполните действия, описанные в этом документе.

## <a name="reset-ssh-keys"></a>Сброс ключей SSH

Если вы развернули AKS без ключей SSH или не имеете доступа к имеющимся ключам SSH, их можно сбросить с помощью портала Azure.

Перейдите к кластеру AKS, выберите узел AKS (виртуальную машину) и нажмите кнопку **Сброс пароля**, чтобы сбросить открытый ключ SSH.

![Кнопка сброса пароля в виртуальной машине AKS](media/aks-ssh/reset-password.png)

Выберите **Сбросить открытый ключ SSH**, введите имя кластера AKS (по умолчанию **azueruser**) и скопируйте его в открытый ключ SSH. По завершении выберите **Обновить**.

![Кнопка сброса пароля в виртуальной машине на портале AKS](media/aks-ssh/reset-password-2.png)

После сброса ключа SSH вы можете создать SSH-подключение с помощью соответствующего закрытого ключа.

## <a name="get-aks-node-address"></a>Получение адреса узла AKS

Получите IP-адрес узла кластера AKS с помощью команды `az vm list-ip-addresses`. Замените имя группы ресурсов на имени своей группы ресурсов AKS.

```console
$ az vm list-ip-addresses --resource-group MC_myAKSCluster_myAKSCluster_eastus -o table

VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-42032720-0  10.240.0.6
aks-nodepool1-42032720-1  10.240.0.5
aks-nodepool1-42032720-2  10.240.0.4
```

## <a name="create-ssh-connection"></a>Создание SSH-соединения

Запустите образ контейнера `debian` и свяжите с ним сеанс терминала. Контейнер можно использовать для создания сеанса SSH с любым узлом в кластере AKS.

```console
kubectl run -it --rm aks-ssh --image=debian
```

Установите клиент SSH в контейнере.

```console
apt-get update && apt-get install openssh-client -y
```

Откройте второй терминалов и отобразите список всех модулей, чтобы получить имя только что созданной группы pod.

```console
$ kubectl get pods

NAME                       READY     STATUS    RESTARTS   AGE
aks-ssh-554b746bcf-kbwvf   1/1       Running   0          1m
```

Скопируйте закрытый ключ SSH в группу pod и замените имя pod правильным значением.

```console
kubectl cp ~/.ssh/id_rsa aks-ssh-554b746bcf-kbwvf:/id_rsa
```

Настройте для файла `id_rsa` разрешение только для чтения.

```console
chmod 0600 id_rsa
```

Теперь создайте SSH-подключение к узлу AKS. Имя пользователя по умолчанию для кластера AKS — `azureuser`. Если эта учетная запись изменена во время создания кластера, укажите правильное имя пользователя-администратора.

```console
$ ssh -i id_rsa azureuser@10.240.0.6

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

Закончив, выйдите из сеанса SSH и интерактивного сеанса контейнера. Это действие удаляет группу pod, используемую для SSH-доступа из кластера AKS.
