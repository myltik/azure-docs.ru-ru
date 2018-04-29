---
title: Использование службы файлов Azure с AKS
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ab118cd43f1e3e57627d940072e50405cd85ca58
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Использование постоянных томов со службой файлов Azure

Постоянный том — это часть хранилища, которая подготовлена к использованию в кластере Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть подготовлен динамически или статически. В этом документе описана динамическая подготовка файлового ресурса Azure в качестве постоянного тома Kubernetes в кластере AKS.

Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes).

## <a name="create-storage-account"></a>Создать учетную запись хранения

При динамической подготовке файлового ресурса Azure в качестве тома Kubernetes можно использовать любую учетную запись хранения, если она содержится в той же группе ресурсов, что и кластер AKS. При необходимости создайте учетную запись хранения в той же группе ресурсов, что и кластер AKS.

Чтобы идентифицировать нужную группу ресурсов, воспользуйтесь командой [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Вы ищете группу ресурсов приблизительно с таким именем: `MC_clustername_clustername_locaton`. Здесь clustername — имя кластера AKS, а location — регион Azure, в котором развернут кластер.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

С помощью команды [az storage account create][az-storage-account-create] создайте учетную запись хранения.

Используя этот пример, замените `--resource-group` именем группы ресурсов, а `--name` — любым именем.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Создание класса хранения

Класс хранения используется для определения того, как создается файловый ресурс Azure. В этом классе можно указать определенную учетную запись хранения. Если учетная запись хранения не указана, то требуется указать `skuName` и `location`, и все учетные записи хранения в связанной группе ресурсов будут проверены на соответствие.

Дополнительные сведения о классах хранения Kubernetes для файлов Azure см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Создайте файл `azure-file-sc.yaml` и скопируйте в него следующий манифест. Замените `storageAccount` именем целевой учетной записи хранения.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

Выполните команду [kubectl create][kubectl-create], чтобы создать класс хранения.

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Создание утверждения постоянного тома

Утверждение постоянного тома (PVC) использует объект класса хранения для динамической подготовки файлового ресурса Azure.

Приведенный ниже манифест позволяет создать утверждение постоянного тома в размере `5GB` с доступом `ReadWriteOnce`.

Создайте файл `azure-file-pvc.yaml` и скопируйте в него следующий манифест. Убедитесь, что `storageClassName` соответствует классу хранения, созданному на предыдущем шаге.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Создайте утверждение постоянного тома командой [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

После ее выполнения будет создан файловый ресурс. Также будет создан секрет Kubernetes, содержащий сведения о подключении и учетные данные.

## <a name="using-the-persistent-volume"></a>Использование постоянного тома

Приведенный ниже манифест создает группу pod, использующую утверждение постоянного тома `azurefile` для подключения файлового ресурса Azure по пути `/mnt/azure`.

Создайте файл `azure-pvc-files.yaml` и скопируйте в него следующий манифест. Убедитесь, что `claimName` соответствует утверждению постоянного тома, созданному на последнем шаге.

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
        claimName: azurefile
```

Выполните команду [kubectl create][kubectl-create], чтобы создать pod.

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Теперь у вас есть работающий pod с диском Azure, подключенным к каталогу `/mnt/azure`. Эта конфигурация может отображаться при проверке вашего модуля через `kubectl describe pod mypod`.

## <a name="mount-options"></a>Параметры подключения

Значения по умолчанию fileMode и dirMode различаются между версиями Kubernetes, как описано в следующей таблице.

| версия | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 или выше | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 или выше | 0755 |

При использовании кластера версии 1.8.5 или выше параметры подключения можно указать в объекте класса хранения. В следующем примере задается значение `0777`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

При использовании кластера версии 1.8.0–1.8.4 контекст безопасности можно указать, задав для `runAsUser` значение `0`. Дополнительные сведения о контексте безопасности Pod см. в разделе [Configure a Security Context][kubernetes-security-context] (Настройка контекста безопасности).

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих службу файлов Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для службы файлов Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
