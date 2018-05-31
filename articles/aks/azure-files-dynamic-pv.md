---
title: Использование службы файлов Azure с AKS
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/17/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 991db1fc32ae89ab04ca040cfb6e8d59ffe5262f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356449"
---
# <a name="persistent-volumes-with-azure-files"></a>Использование постоянных томов со службой файлов Azure

Постоянный том — это часть хранилища, которая создана для использования в кластере Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть создан динамически или статически. В этом документе описано **динамическое создание** файлового ресурса Azure в качестве постоянного тома.

Дополнительные сведения о постоянных томах Kubernetes, в том числе о статическом создании см. в [этой статье][kubernetes-volumes].

## <a name="create-storage-account"></a>Создать учетную запись хранения

При динамическом создании файлового ресурса Azure в качестве тома Kubernetes можно использовать любую учетную запись хранения, если она содержится в той же группе ресурсов, что и кластер AKS. При необходимости создайте учетную запись хранения в той же группе ресурсов, что и кластер AKS.

Чтобы идентифицировать нужную группу ресурсов, воспользуйтесь командой [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Найдите группу ресурсов с именем `MC_clustername_clustername_locaton`.

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

Выполните команду [kubectl apply][kubectl-apply], чтобы создать класс хранения.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Создание утверждения постоянного тома

Утверждение постоянного тома (PVC) использует объект класса хранения для динамической подготовки файлового ресурса Azure.

Приведенный ниже код YAML позволяет создать утверждение постоянного тома в размере `5GB` с доступом `ReadWriteOnce`. Дополнительные сведения о режимах доступа см. в документации [по постоянным томам Kubernetes][access-modes].

Создайте файл `azure-file-pvc.yaml` и скопируйте в него следующий код YAML. Убедитесь, что `storageClassName` соответствует классу хранения, созданному на предыдущем шаге.

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

Создайте утверждение постоянного тома с помощью команды [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

После ее выполнения будет создан файловый ресурс. Также будет создан секрет Kubernetes, содержащий сведения о подключении и учетные данные.

## <a name="using-the-persistent-volume"></a>Использование постоянного тома

Приведенный ниже код YAML создает группу pod, использующую утверждение постоянного тома `azurefile` для подключения файлового ресурса Azure по пути `/mnt/azure`.

Создайте файл `azure-pvc-files.yaml` и скопируйте в него следующий код YAML. Убедитесь, что `claimName` соответствует утверждению постоянного тома, созданному на последнем шаге.

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

Выполните команду [kubectl apply][kubectl-apply], чтобы создать pod.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
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

При динамическом создании постоянного тома с классом хранилища и использовании кластера версии 1.8.5 или выше параметры подключения можно указать в объекте класса хранения. В следующем примере задается значение `0777`.

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

При статическом создании постоянного тома и использовании кластера версии 1.8.5 или выше параметры подключения необходимо указать в объекте `PersistentVolume`. Дополнительные сведения о статическом создании постоянного тома см. в [этом разделе][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

При использовании кластера версии 1.8.0–1.8.4 контекст безопасности можно указать, задав для `runAsUser` значение `0`. Дополнительные сведения о контексте безопасности Pod см. в разделе [Configure a Security Context][kubernetes-security-context] (Настройка контекста безопасности).

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих службу файлов Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для службы файлов Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
