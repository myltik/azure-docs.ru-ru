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
ms.openlocfilehash: d468944883cca80946001724c38dd5ec9ba0d94f
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="persistent-volumes-with-azure-files---dynamic-provisioning"></a>Постоянные тома с файлами Azure - динамической инициализации

Постоянные тома представляет часть памяти, который был инициализирован для использования в кластере Kubernetes. Постоянные тома может использоваться один или несколько модулей и могут быть динамически или статически подготовлены. В этом документе описаны динамические подготовки Azure файловый ресурс как постоянные Kubernetes том в кластере AKS. 

Дополнительные сведения о Kubernetes постоянные тома см. в разделе [постоянные тома Kubernetes][kubernetes-volumes].

## <a name="prerequisites"></a>Технические условия

Динамически подготовить Azure файловый ресурс как Kubernetes том, при условии, что они хранятся в той же группе ресурсов кластера AKS можно использовать любой учетной записи хранилища. При необходимости создайте учетную запись хранения в той же группе ресурсов кластера AKS. 

Чтобы идентифицировать группу ресурсов, используйте [список групп az] [ az-group-list] команды.

```azurecli-interactive
az group list --output table
```

В следующем примере вывод показывает группы ресурсов, связаны с AKS кластера. Группа ресурсов с именем, например *MC_myAKSCluster_myAKSCluster_eastus* содержит AKS ресурсы кластера, и где необходимо создать учетную запись хранения. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

После определения группы ресурсов, создать учетную запись хранения с [создания учетной записи хранилища az] [ az-storage-account-create] команды.

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Создайте класс хранения

Класс хранения используется для определения того, как настроена динамически созданные постоянные тома. Элементы, такие как имя учетной записи хранилища Azure, SKU и области определяются в объекте класса хранилища. Дополнительные сведения о Kubernetes классы хранения см. в разделе [классы хранения Kubernetes][kubernetes-storage-classes].

Следующий пример указывает, что тип учетной записи хранения, все номера SKU `Standard_LRS` в `eastus` область может использоваться при запросе хранилища. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Чтобы использовать учетную запись конкретного хранилища, `storageAccount` может быть использован.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Создание постоянных тома утверждения

Постоянные тома утверждения использует объект класса хранения для динамически подготавливать часть хранилища. При использовании файлов Azure, Azure файловый ресурс создается в учетной записи хранения выбирается или указан в объекте класса хранения.

>  [!NOTE]
>   Убедитесь, что в той же группе ресурсов кластера AKS предварительно создана соответствующая учетная запись хранения. Утверждение постоянные тома не подготовки Azure общая папка, если учетная запись хранилища недоступна. 

Приведенный ниже манифест может использоваться для создания постоянного тома утверждения `5GB` емкости `ReadWriteOnce` доступа. Дополнительные сведения о режимах доступа PVC см. в разделе [режимы доступа][access-modes].

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

## <a name="using-the-persistent-volume"></a>Использование постоянных тома

После утверждения постоянные тома будет создана и типом pod хранилища успешно подготовлена, можно создать с доступом к тому. Приведенный ниже манифест создает pod, использующего утверждения постоянные тома `azurefile` подключить Azure общей папке по `/var/www/html` пути. 

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
| V1.6.x, v1.7.x | 0777 |
| V1.8.0 v1.8.5 | 0700 |
| V1.8.6 или более поздней версии | 0755 |
| V1.9.0 | 0700 |
| V1.9.1 или более поздней версии | 0755 |

При использовании кластера версии 1.8.5 или выше, подключения, параметры могут быть указаны в объекте класса хранилища. В следующем примере задается `0777`. 

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

Если с помощью кластера версии 1.8.0 - 1.8.4, контекст безопасности может быть указан с `runAsUser` значение `0`. Дополнительные сведения о контексте безопасности Pod см. в разделе [настроить контекст безопасности][kubernetes-security-context].

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о Kubernetes постоянные тома с помощью службы файлов Azure.

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