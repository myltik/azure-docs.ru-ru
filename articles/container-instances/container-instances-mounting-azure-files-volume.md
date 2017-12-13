---
title: "Подключение тома службы файлов Azure в службе \"Экземпляры контейнеров Azure\""
description: "Узнайте, как подключить том файлов Azure для сохранения состояния с помощью Экземпляров контейнеров Azure"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/05/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b2e8e27cecb4d1225e378690063b42f5d5242868
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Подключение общего ресурса службы файлов Azure с помощью службы "Экземпляры контейнеров Azure"

По умолчанию в Экземплярах контейнеров Azure не отслеживается состояние. Если происходит сбой в контейнере или он завершает работу, все сведения о состоянии будут утеряны. Чтобы сохранить состояние после истечения времени существования контейнера, необходимо подключить том из внешнего хранилища. В этой статье показано, как подключить файловый ресурс Azure для использования с Экземплярами контейнеров Azure.

## <a name="create-an-azure-file-share"></a>Создание файлового ресурса Azure

Перед использованием файлового ресурса Azure с Экземплярами контейнеров Azure необходимо создать его. Выполните приведенный ниже скрипт, чтобы создать учетную запись хранения для размещения файлового ресурса и общего ресурса. Имя учетной записи хранения должно быть глобально уникальным, поэтому скрипт добавляет случайное значение к базовой строке.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Получение данных для доступа к учетной записи хранения

Для подключения файлового ресурса Azure в качестве тома в Экземплярах контейнеров Azure требуется три значения: имя учетной записи хранения, имя общего ресурса и ключ доступа к хранилищу.

Если вы использовали сценарий выше, имя учетной записи хранения создано со случайным значением в конце. Для выполнения запроса к последней строке (включая часть со случайным значением) используйте следующие команды:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Имя общего ресурса уже известно (определено как *acishare* в сценарии выше), так что остается только ключ учетной записи хранения, который можно найти с помощью следующей команды:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="deploy-the-container-and-mount-the-volume"></a>Развертывание контейнера и подключение тома

Чтобы подключить файловый ресурс Azure в качестве тома в контейнере, укажите точку подключения для общего ресурса и тома при создании контейнера с помощью команды [az container create](/cli/azure/container#az_container_create). Если вы выполнили предыдущие инструкции, можно подключить созданный ранее общий ресурс с помощью следующей команды для создания контейнера:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>Управление файлами в подключенном томе

После запуска контейнера можно использовать простое веб-приложение, развернутое с помощью образа [seanmckenna/aci-hellofiles](https://hub.docker.com/r/seanmckenna/aci-hellofiles/), чтобы управлять файлами в общем ресурсе службы файлов Azure по указанному вами пути подключения. Получите IP-адрес для веб-приложения с помощью команды [az container show](/cli/azure/container#az_container_show).

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

С помощью [портала Azure](https://portal.azure.com) или такого средства, как [обозреватель службы хранилища Microsoft Azure](https://storageexplorer.com), можно извлечь и проверить файл, записанный в файловый ресурс.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [связи между службой "Экземпляры контейнеров Azure" и оркестраторами контейнеров](container-instances-orchestrator-relationship.md).
