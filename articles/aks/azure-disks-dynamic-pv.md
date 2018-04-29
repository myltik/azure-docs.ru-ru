---
title: Использование дисков Azure с AKS
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: a6bc79d0556299634a78c5232bbab4e20810172c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>Постоянные тома с дисками Azure

Постоянный том — это часть хранилища, которая подготовлена к использованию для модулей pod Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть подготовлен динамически или статически. Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes).

В этом документе описываются использование постоянных томов с дисками Azure в кластере Службы контейнеров Azure (AKS).

> [!NOTE]
> Диск Azure можно подключить только с в режиме доступа ReadWriteOnce, который предоставляет доступ только к одному узлу AKS. Если вам нужно использовать постоянный том на нескольких узлах, попробуйте использовать [службу файлов Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Встроенные классы хранения

Класс хранения используется для определения того, как единица хранения создается динамически с помощью постоянного тома. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Каждый кластер AKS включает два предварительно созданных класса хранения, настроенных для работы с дисками Azure. Класс хранения `default` подготавливает диск Azure уровня "Стандартный". Класс хранения `managed-premium` подготавливает диск Azure уровня "Премиум". Если узлы AKS в кластере используют хранилище уровня "Премиум", выберите класс `managed-premium`.

Используйте команду [kubectl get sc][kubectl-get], чтобы просмотреть предварительно созданные классы хранения.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>Создание утверждения постоянного тома

Утверждение постоянного тома (PVC) используется для автоматической подготовки хранилища на основе класса хранения. В этом случае утверждение постоянного тома может использовать один из предварительно созданных классов хранения для создания управляемого диска Azure уровня "Стандартный" или "Премиум".

Создайте файл `azure-premimum.yaml` и скопируйте в него следующий манифест.

Обратите внимание на то, что класс хранения `managed-premium` указан в заметке и утверждение запрашивает диск емкостью `5GB` с правами доступа `ReadWriteOnce`.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Создайте утверждение постоянного тома командой [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>Использование постоянного тома

После создания утверждения постоянного тома и успешной подготовки диска можно создать группу pod с доступом к диску. Приведенный ниже манифест создает группу pod, использующую утверждение постоянного тома `azure-managed-disk` для подключения диска Azure по пути `/mnt/azure`.

Создайте файл `azure-pvc-disk.yaml` и скопируйте в него следующий манифест.

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
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Выполните команду [kubectl create][kubectl-create], чтобы создать pod.

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

Теперь у вас есть работающий pod с диском Azure, подключенным к каталогу `/mnt/azure`. Эта конфигурация может отображаться при проверке вашего модуля через `kubectl describe pod mypod`.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих диски Azure.

> [!div class="nextstepaction"]
> [Storage Classes][kubernetes-disk] (Классы хранения)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md