---
title: "Подключение тома файлов Azure в Экземплярах контейнеров Azure"
description: "Узнайте, как подключить том файлов Azure для сохранения состояния с помощью Экземпляров контейнеров Azure"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: c68f0239bcb95aa5e9d8194f7b358f30588ea600
ms.contentlocale: ru-ru
ms.lasthandoff: 09/01/2017

---

# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Подключение файлового ресурса Azure с помощью Экземпляров контейнеров Azure

По умолчанию в Экземплярах контейнеров Azure не отслеживается состояние. Если происходит сбой в контейнере или он завершает работу, все сведения о состоянии будут утеряны. Чтобы сохранить состояние после истечения времени существования контейнера, необходимо подключить том из внешнего хранилища. В этой статье показано, как подключить файловый ресурс Azure для использования с Экземплярами контейнеров Azure.

## <a name="create-an-azure-file-share"></a>Создание файлового ресурса Azure

Перед использованием файлового ресурса Azure с Экземплярами контейнеров Azure необходимо создать его. Выполните следующий сценарий, чтобы создать учетную запись хранения для размещения файлового ресурса и общего ресурса. Имя учетной записи хранения должно быть глобально уникальным, поэтому скрипт добавляет случайное значение к базовой строке.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Получение данных для доступа к учетной записи хранения

Для подключения файлового ресурса Azure в качестве тома в Экземплярах контейнеров Azure требуется три значения: имя учетной записи хранения, имя общего ресурса и ключ доступа к хранилищу.

Если вы использовали сценарий выше, имя учетной записи хранения создано со случайным значением в конце. Для выполнения запроса к последней строке (включая часть со случайным значением) используйте следующие команды:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group myResourceGroup --query "[?contains(name,'mystorageaccount')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

Имя общего ресурса уже известно (это *acishare* в сценарии выше), так что остается только ключ учетной записи хранения, который можно найти с помощью следующей команды:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group myResourceGroup --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Сохранение сведений о доступе к учетной записи хранения с помощью хранилища ключей Azure

Ключи учетной записи хранения обеспечивают защиту при доступе к данным, поэтому мы рекомендуем хранить их в хранилище ключей Azure.

Создайте хранилище ключей с помощью Azure CLI:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g myResourceGroup
```

Параметр `enabled-for-template-deployment` позволяет Azure Resource Manager извлекать секреты из хранилища ключей во время развертывания.

Сохраните ключ учетной записи хранения как новый секрет в хранилище ключей:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>Подключение тома

Подключение файлового ресурса Azure как тома в контейнере состоит из двух этапов. Сначала укажите сведения об общем ресурсе при определении группы контейнеров, а затем укажите способ подключения тома к одному или к нескольким контейнерам в группе.

Чтобы определить тома, которые требуется сделать доступными для подключения, добавьте массив `volumes` к определению группы контейнеров в шаблоне Azure Resource Manager, а затем укажите его в определении отдельных контейнеров.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

Шаблон включает имя учетной записи хранения и ключ как параметры, которые можно указать в отдельном файле параметров. Чтобы заполнить файл параметров, необходимо три значения: имя учетной записи хранения, идентификатор ресурса хранилища ключей Azure и имя секрета хранилища ключей, использованного для хранения ключа к хранилищу данных. Если предыдущие шаги выполнены, узнать эти значения можно с помощью следующего скрипта:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Вставьте значения в файл параметров:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>Развертывание контейнера и управление файлами

С определенным шаблоном можно создать контейнер и подключить его том с помощью Azure CLI. Предположим, что файл шаблона называется *azuredeploy.json*, а файл параметров — *azuredeploy.parameters.json*. Командная строка будет выглядеть следующим образом:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

После запуска контейнера можно использовать простое веб-приложение, развернутое с помощью образа **seanmckenna/aci-hellofiles**, чтобы управлять файлами в файловом ресурсе Azure по указанному вами пути подключения. Получите IP-адрес веб-приложения, выполнив приведенную ниже команду.

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

С помощью такого средства, как [обозреватель хранилищ Microsoft Azure](http://storageexplorer.com), можно извлечь и проверить файл, записанный в файловый ресурс.

>[!NOTE]
> Дополнительные сведения об использовании шаблонов Azure Resource Manager, файлов параметров и развертывании с помощью Azure CLI см. в разделе [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

- Разверните первый контейнер, используя сведения из руководства по[быстрому запуску](container-instances-quickstart.md) для Экземпляров контейнеров Azure.
- Дополнительные сведения о [связи между Экземплярами контейнеров Azure и оркестраторам контейнеров](container-instances-orchestrator-relationship.md).

