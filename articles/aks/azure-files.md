---
title: "Использование службы файлов Azure с AKS"
description: "Использование дисков Azure с AKS."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b6267dd2bc1b29229b2e8016e2429ed88b7bf676
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2018
---
# <a name="using-azure-files-with-kubernetes"></a>Использование службы файлов Azure с Kubernetes

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. В качестве этого внешнего хранилища данных можно использовать службу файлов Azure. В этой статье описывается, как использовать службу файлов Azure в качестве тома Kubernetes в службе контейнеров Azure.

Дополнительные сведения о томах Kubernetes см. в разделе [Volumes][kubernetes-volumes] (Тома).

## <a name="create-an-azure-file-share"></a>Создание файлового ресурса Azure

Перед использованием файлового ресурса Azure в качестве тома Kubernetes необходимо создать учетную запись хранения Azure и файловый ресурс. Для выполнения этих задач можно использовать приведенный ниже сценарий. Запишите или измените значения параметров. Некоторые из них понадобятся при создании тома Kubernetes.

```azurecli-interactive
# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)
```

## <a name="create-kubernetes-secret"></a>Создание секрета Kubernetes

Kubernetes требуются учетные данные для доступа к файловому ресурсу. Эти учетные данные хранятся в [секрете Kubernetes][kubernetes-secret], который указывается при создании pod Kubernetes.

При создании секрета Kubernetes его значения должны быть закодированы в формате base64.

Сначала закодируйте имя учетной записи хранения. При необходимости замените `$AKS_PERS_STORAGE_ACCOUNT_NAME` именем своей учетной записи хранения Azure.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

Затем закодируйте ключ учетной записи хранения. При необходимости замените `$STORAGE_KEY` именем ключа учетной записи хранения Azure.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

Создайте файл `azure-secret.yaml` и скопируйте в него следующий код YAML. Измените значения `azurestorageaccountname` и `azurestorageaccountkey`, указав значения в кодировке base64, полученные на предыдущем шаге.

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

Выполните команду [kubectl create][kubectl-create], чтобы создать секрет.

```azurecli-interactive
kubectl create -f azure-secret.yaml
```

## <a name="mount-file-share-as-volume"></a>Подключение файлового ресурса в качестве тома

Можно подключить общий ресурс службы файлов Azure к pod, настроив том в его спецификации. Создайте файл `azure-files-pod.yaml` со следующим содержимым. Измените `aksshare`, указав имя, присвоенное общему ресурсу службы файлов Azure.

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
      shareName: aksshare
      readOnly: false
```

Используйте команду kubectl для создания pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Теперь у вас есть работающий контейнер с общим ресурсом службы файлов Azure, подключенным к каталогу `/mnt/azure`. Подключенный том можно увидеть, изучив pod с помощью команды `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о томах Kubernetes, использующих службу файлов Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для службы файлов Azure][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
