---
title: Использование файловых ресурсов для кластера DC/OS Azure
description: Создание и подключение файлового ресурса к кластеру DC/OS в Службе контейнеров Azure.
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2017
ms.locfileid: "26331208"
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Создание и подключение файлового ресурса к кластеру DC/OS

В этом руководстве рассматривается создание файлового ресурса в Azure и его подключение к каждому узлу агента и главному узлу кластера DC/OS. Настройка файлового ресурса упрощает обмен файлами в кластере, в том числе настройку, доступ, использование журналов и многое другое. В этом руководстве выполняются следующие задачи:

> [!div class="checklist"]
> * Создание учетной записи хранения Azure
> * Создайте общую папку
> * Подключение ресурса к кластеру DC/OS

Для выполнения действий, описанных в этом руководстве, потребуется кластер ACS DC/OS. При необходимости его можно создать с помощью следующего [примера сценария](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Создание файлового ресурса в Microsoft Azure

Перед использованием файлового ресурса Azure с кластером ACS DC/OS необходимо создать учетную запись хранения и файловый ресурс. Чтобы создать хранилище и файловый ресурс, выполните следующий сценарий. Обновите параметры данными из вашей среды.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Подключение ресурса к кластеру

Затем файловый ресурс необходимо подключить на каждой виртуальной машине кластера. Эта задача выполняется с помощью средства/протокола cifs. Операцию подключения можно выполнить вручную на каждом узле кластера либо запустить сценарий на каждом узле кластера.

В следующем примере выполняются два сценария: первый — для подключения файлового ресурса Azure, а второй — для запуска этого сценария на каждом узле кластера DC/OS.

Для начала требуются имя учетной записи хранения Azure и ключ доступа. Чтобы получить эти сведения, выполните следующие команды. Запомните или запишите эти значения — они будут использоваться в последующих шагах.

Имя учетной записи хранения:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Ключ доступа к учетной записи хранения:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

Затем получите полное доменное имя главного кластера DC/OS и сохраните его в переменной.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

Скопируйте свой закрытый ключ на главный узел. Этот ключ необходим для создания ssh-подключения ко всем узлам кластера. Измените имя пользователя, если при создании кластера использовалось значение не по умолчанию. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

Создайте SSH-подключение к главному узлу (или первому главному узлу) кластера DC/OS. Измените имя пользователя, если при создании кластера использовалось значение не по умолчанию.

```azurecli-interactive
ssh azureuser@$FQDN
```

Создайте файл с именем **cifsMount.sh** и скопируйте в него следующие данные. 

Этот сценарий используется для подключения файлового ресурса Azure. Обновите значения переменных `STORAGE_ACCT_NAME` и `ACCESS_KEY` ранее собранными данными.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
Создайте второй файл с именем **getNodesRunScript.sh** и скопируйте в него следующие данные. 

Этот сценарий обнаруживает все узлы кластера, а затем запускает сценарий **cifsMount.sh** для подключения файлового ресурса на каждом узле.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

Выполните этот сценарий, чтобы подключить файловый ресурс Azure на всех узлах кластера.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

Файловый ресурс теперь доступен по адресу `/mnt/share/dcosshare` на каждом узле кластера.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве была обеспечена доступность файлового ресурса Azure на кластере DC/OS с помощью следующих действий:

> [!div class="checklist"]
> * Создание учетной записи хранения Azure
> * Создайте общую папку
> * Подключение ресурса к кластеру DC/OS

Переходите к следующему руководству, чтобы узнать об интеграции реестра контейнеров Azure с DC/OS в Azure.  

> [!div class="nextstepaction"]
> [Приложения балансировки нагрузки](container-service-dcos-acr.md)
