---
title: Использование службы файлов Azure с AKS
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e0e62d37fbcc8f296deaee8e0f6658c0f72321ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934620"
---
# <a name="volumes-with-azure-files"></a>Тома с файлами Azure

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. В качестве этого внешнего хранилища данных можно использовать службу файлов Azure. В этой статье описывается, как использовать службу файлов Azure в качестве тома Kubernetes в службе Azure Kubernetes.

Дополнительные сведения о томах Kubernetes см. в разделе [Volumes][kubernetes-volumes] (Тома).

## <a name="create-an-azure-file-share"></a>Создание файлового ресурса Azure

Перед использованием файлового ресурса Azure в качестве тома Kubernetes необходимо создать учетную запись хранения Azure и файловый ресурс. Для выполнения этих задач можно использовать приведенный ниже сценарий. Запишите или измените значения параметров. Некоторые из них понадобятся при создании тома Kubernetes.

```azurecli-interactive
#!/bin/bash

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

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Создание секрета Kubernetes

Kubernetes требуются учетные данные для доступа к файловому ресурсу. Эти учетные данные хранятся в [секрете Kubernetes][kubernetes-secret], который указывается при создании pod Kubernetes.

Создайте секрет с помощью следующей команды. Вместо `STORAGE_ACCOUNT_NAME` укажите имя учетной записи хранения, а вместо `STORAGE_ACCOUNT_KEY` — ключ к хранилищу данных.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Подключение файлового ресурса в качестве тома

Подключите общий ресурс службы файлов Azure к pod, настроив том в его спецификации. Создайте файл `azure-files-pod.yaml` со следующим содержимым. Измените `aksshare`, указав имя, присвоенное общему ресурсу службы файлов Azure.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
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

Теперь у вас есть работающий контейнер с общим ресурсом службы файлов Azure, подключенным к каталогу `/mnt/azure`.  Подключенный том можно увидеть, изучив pod с помощью команды `kubectl describe pod azure-files-pod`.

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
