---
title: "Использование службы файлов Azure с AKS"
description: "Использование дисков Azure с AKS."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4873b98c8ba4f1e574be20baebef3b6860341529
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Постоянные тома с файлами Azure — динамическая подготовка

Постоянный том — это часть хранилища, которая подготовлена к использованию в кластере Kubernetes. Постоянный том может использоваться одним или несколькими модулями и может быть подготовлен динамически или статически. В этом документе описана динамическая подготовка файлового ресурса Azure в качестве постоянного тома Kubernetes в кластере AKS. 

Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes).

## <a name="prerequisites"></a>Необходимые компоненты

При динамической подготовке файлового ресурса Azure в качестве тома Kubernetes можно использовать любую учетную запись хранения, если она содержится в той же группе ресурсов, что и кластер AKS. При необходимости создайте учетную запись хранения в той же группе ресурсов, что и кластер AKS. 

Чтобы идентифицировать нужную группу ресурсов, воспользуйтесь командой [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

В следующем примере выходных данных показаны группы ресурсов, которые связаны с кластером AKS. Группа ресурсов с именем наподобие *MC_myAKSCluster_myAKSCluster_eastus* содержит ресурсы кластера AKS. Именно в ней необходимо создать учетную запись хранения. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Определив группу ресурсов, создайте учетную запись хранения с помощью команды [az storage account create][az-storage-account-create].

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Создание класса хранения

Класс хранения используется для определения способа настройки динамически создаваемого постоянного тома. Элементы, такие как имя учетной записи хранения Azure, SKU и регион, определяются в объекте класса хранения. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Следующий пример указывает, что при запросе к хранилищу может использоваться любая учетная запись хранения с типом SKU `Standard_LRS` в регионе `eastus`. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Чтобы использовать конкретную учетную запись хранения, можно применить параметр `storageAccount`.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Создание утверждения постоянного тома

Утверждение постоянного тома использует объект класса хранения для динамической подготовки части хранилища. При использовании файлов Azure файловый ресурс Azure создается в учетной записи хранения, выбранной или указанной в объекте класса хранения.

>  [!NOTE]
>   Убедитесь, что соответствующая учетная запись хранения предварительно создана в той же группе ресурсов, что и ресурсы кластера AKS. Эта группа ресурсов имеет имя наподобие *MC_myAKSCluster_myAKSCluster_eastus*. Утверждению постоянного тома не удастся подготовить файловый ресурс Azure, если учетная запись хранения недоступна. 

Приведенный ниже манифест позволяет создать утверждение постоянного тома в размере `5GB` с доступом `ReadWriteOnce`. Дополнительные сведения о режимах доступа PVC см. в разделе [Access Modes][access-modes] (Режимы доступа).

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

## <a name="using-the-persistent-volume"></a>Использование постоянного тома

После создания утверждения постоянного тома и успешной подготовки хранилища можно создать группу pod с доступом к тому. Приведенный ниже манифест создает группу pod, использующую утверждение постоянного тома `azurefile` для подключения файлового ресурса Azure по пути `/var/www/html`. 

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
        claimName: azurefile
```

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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create