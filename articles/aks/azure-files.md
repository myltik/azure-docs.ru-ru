---
title: "Использование службы файлов Azure с AKS | Документация Майкрософт"
description: "Использование дисков Azure с AKS."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Использование службы файлов Azure с Kubernetes

Контейнерные приложения часто требуются для доступа к данным и их хранению во внешнем томе данных. В качестве этого внешнего хранилища данных можно использовать службу файлов Azure. В этой статье описывается, как использовать службу файлов Azure в качестве тома Kubernetes в службе контейнеров Azure.

Дополнительные сведения о томах Kubernetes см. в разделе [Volumes][kubernetes-volumes] (Тома).

## <a name="creating-a-file-share"></a>Создание файлового ресурса

Существующий общий ресурс службы файлов Azure можно использовать со службой контейнеров Azure. Если необходимо создать такой файловый ресурс, используйте следующий набор команд.

Создайте группу ресурсов для общего ресурса службы файлов Azure с помощью команды [az group create][az-group-create]. Группа ресурсов, учетная запись хранения и кластер Kubernetes должны находиться в одном регионе.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

С помощью команды [az storage account create][az-storage-create] создайте учетную запись хранения Azure. Имя этой учетной записи хранения должно быть уникальным. Измените значение аргумента `--name`, указав уникальное значение.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Получите ключ к хранилищу данных с помощью команды [az storage account keys list][az-storage-key-list]. Измените значение аргумента `--account-name`, указав уникальное имя учетной записи хранения.

Запишите одно из значений ключа, оно будет использовано в последующих шагах.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Создайте общий ресурс службы файлов Azure, выполнив команду [az storage share create][az-storage-share-create]. Измените значение `--account-key`, указав значение, полученное на предыдущем шаге.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Создание секрета Kubernetes

Kubernetes требуются учетные данные для доступа к файловому ресурсу. Вместо того, чтобы сохранять имя и ключ учетной записи хранения Azure с каждым pod, они сохраняются один раз в [секрете Kubernetes][kubernetes-secret], на который ссылается каждый том службы файлов Azure. 

Значения в манифесте секрета Kubernetes должны быть указаны в кодировке base64. Используйте приведенные ниже команды для возвращения закодированных значений.

Сначала закодируйте имя учетной записи хранения. Замените `storage-account` именем своей учетной записи хранения Azure.

```azurecli-interactive
echo -n <storage-account> | base64
```

Затем нужно указать ключ доступа к учетной записи хранения. Выполните приведенную ниже команду, чтобы получить закодированный ключ. Замените `storage-key` ключом, полученным на предыдущем шаге.

```azurecli-interactive
echo -n <storage-key> | base64
```

Создайте файл `azure-secret.yml` и скопируйте в него следующий код YAML. Измените значения `azurestorageaccountname` и `azurestorageaccountkey`, указав значения в кодировке base64, полученные на предыдущем шаге.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Выполните команду [kubectl apply][kubectl-apply], чтобы создать секрет.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Подключение файлового ресурса в качестве тома

Можно подключить общий ресурс службы файлов Azure к pod, настроив том в его спецификации. Создайте файл `azure-files-pod.yml` со следующим содержимым. Измените `share-name`, указав имя, присвоенное общему ресурсу службы файлов Azure.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

Используйте команду kubectl для создания pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

Теперь у вас есть работающий контейнер с общим ресурсом службы файлов Azure, подключенным к каталогу `/mnt/azure`. Подключенный том можно увидеть, изучив pod с помощью команды `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о томах Kubernetes, использующих службу файлов Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для службы файлов Azure](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create