---
title: "Использование дисков Azure с AKS"
description: "Использование дисков Azure с AKS."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/12/2018
ms.author: nepeters
ms.openlocfilehash: a4e4ce6a23f9f8a99d8ae5f9e4e2084e3b749017
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Постоянные тома с дисками Azure — динамическая подготовка

Постоянный том — это часть хранилища, которая подготовлена к использованию в кластере Kubernetes. Постоянный том может использоваться одним или несколькими модулями и может быть подготовлен динамически или статически. В этом документе описана динамическая подготовка дисков Azure в качестве постоянного тома Kubernetes в кластере AKS. 

Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes).

## <a name="built-in-storage-classes"></a>Встроенные классы хранения

Класс хранения используется для определения способа настройки динамически создаваемого постоянного тома. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Каждый кластер AKS включает два предварительно созданных класса хранения, настроенных для работы с дисками Azure. Выполните команду `kubectl get storageclass`, чтобы просмотреть классы.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

Если они подходят для работы, создавать дополнительный класс не нужно.

## <a name="create-storage-class"></a>Создание класса хранения

Если вы хотите создать класс хранения, настроенный для дисков Azure, это можно сделать с помощью примера манифеста ниже. 

Значение `storageaccounttype` свойства `Standard_LRS` означает, что создан стандартный диск. Это значение можно изменить на `Premium_LRS`, чтобы создать [диск категории "Премиум"][premium-storage]. Чтобы использовать такие диски, размер виртуальной машины на узле AKS должен быть совместимым с размером дисков категории "Премиум". Ознакомиться со списком совместимых размеров можно в [этом документе][premium-storage].

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```



## <a name="create-persistent-volume-claim"></a>Создание утверждения постоянного тома

Утверждение постоянного тома использует объект класса хранения для динамической подготовки части хранилища. При использовании диска Azure диск создается в той же группе ресурсов, что и ресурсы AKS.

В этом примере манифеста создается утверждение постоянного тома с использованием класса хранения `azure-managed-disk` для создания диска емкостью `5GB` с доступом `ReadWriteOnce`. Дополнительные сведения о режимах доступа PVC см. в разделе [Access Modes][access-modes] (Режимы доступа).

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Использование постоянного тома

После создания утверждения постоянного тома и успешной подготовки диска можно создать группу pod с доступом к диску. Приведенный ниже манифест создает группу pod, использующую утверждение постоянного тома `azure-managed-disk` для подключения диска Azure по пути `/var/www/html`. 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих диски Azure.

> [!div class="nextstepaction"]
> [Storage Classes][kubernetes-disk] (Классы хранения)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[premium-storage]: ../virtual-machines/windows/premium-storage.md