---
title: Использование дисков Azure с AKS.
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b790213e19b9f2aaef74a3f670c89246f54fd6d7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="volumes-with-azure-disks"></a>Тома с дисками Azure

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. В качестве этого внешнего хранилища данных можно использовать диски Azure. Из этой статьи вы узнаете, как использовать диск Azure в качестве тома Kubernetes в кластере службы Azure Kubernetes.

Дополнительные сведения о томах Kubernetes см. в разделе [Volumes][kubernetes-volumes] (Тома).

## <a name="create-an-azure-disk"></a>Создание диска Azure

Прежде чем подключить управляемый диск Azure в качестве тома Kubernetes, убедитесь, что диск будет существовать в той же группе ресурсов, что и ресурсы кластера AKS. Чтобы найти эту группу ресурсов, используйте команду [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Вы ищете группу ресурсов приблизительно с таким именем: `MC_clustername_clustername_locaton`. Здесь clustername — имя кластера AKS, а location — регион Azure, в котором развернут кластер.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Чтобы создать диск Azure, используйте команду [az disk create][az-disk-create].

Используя этот пример, замените `--resource-group` именем группы ресурсов, а `--name` — любым именем.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Когда диск будет создан, вы увидите результат, аналогичный приведенному ниже. Это значение является идентификатором диска, который используется при подключении диска к pod Kubernetes.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Подключите диска в качестве тома

Подключите диск Azure к pod, настроив том в спецификации контейнера.

Создайте файл `azure-disk-pod.yaml` со следующим содержимым. Замените `diskName` именем созданного диска, а `diskURI` — идентификатором диска. Кроме того, запишите `mountPath`. Это путь, по которому диск Azure подключен в pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Чтобы создать pod, используйте команду kubectl.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Теперь у вас есть запущенная группа pod с подключенным диском Azure в папке `/mnt/azure`.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о томах Kubernetes, использующих диски Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для дисков Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
