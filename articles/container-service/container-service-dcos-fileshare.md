---
title: "Использование файловых ресурсов для кластера DC/OS Azure | Документация Майкрософт"
description: "Создание и подключение файлового ресурса к кластеру DC/OS в Службе контейнеров Azure."
services: container-service
documentationcenter: 
author: julienstroheker
manager: dcaro
editor: 
tags: acs, azure-container-service
keywords: "Docker, контейнеры, микрослужбы, Mesos, Azure, FileShare, cifs"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2017
ms.author: juliens
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 6d40821327a9df47bb85ea12ecd33e4a0f49e39e
ms.lasthandoff: 03/22/2017


---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>Создание и подключение файлового ресурса к кластеру DC/OS
В этой статье мы рассмотрим создание файлового ресурса в Azure и его подключение к каждому узлу агента и главному узлу кластера DC/OS. Настройка файлового ресурса упрощает обмен файлами в кластере, в том числе настройку, доступ, использование журналов и многое другое.

Для выполнения этого примера вам потребуется кластер DC/OS, настроенный в Службе контейнеров Azure. Ознакомьтесь с разделом [Развертывание кластера службы контейнеров Azure](container-service-deployment.md).

## <a name="create-a-file-share-on-microsoft-azure"></a>Создание файлового ресурса в Microsoft Azure
### <a name="using-the-portal"></a>С помощью портала

1. Войдите на портал.
2. Создайте учетную запись хранения.
   
  ![Создание учетной записи хранения в Службе контейнеров Azure](media/container-service-dcos-fileshare/createSA.png)

3. После ее создания щелкните **Файлы** в разделе **Службы**.
   
  ![Раздел файлов Службы контейнеров Azure](media/container-service-dcos-fileshare/filesServices.png)

4. Щелкните **+ File share** (+ Файловый ресурс) и введите имя этого нового файлового ресурса (**квота** не является обязательной).
   
  ![Создание файлового ресурса в Службе контейнеров Azure](media/container-service-dcos-fileshare/newFileShare.png)  

### <a name="using-azure-cli-20"></a>Использование Azure CLI 2.0

Если необходимо, [установите и настройте Azure CLI](/cli/azure/install-azure-cli.md).

```azurecli
################# Change these four parameters ##############
DCOS_PERS_STORAGE_ACCOUNT_NAME=anystorageaccountname
DCOS_PERS_RESOURCE_GROUP=AnyResourceGroupName
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=demoshare
#############################################################

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>Подключение ресурса к кластеру

Далее необходимо подключить этот ресурс к каждой виртуальной машине в кластере с помощью инструмента или протокола cifs. Для этого воспользуйтесь командой `mount -t cifs`.

Ниже приведен пример, использующий:
* имя учетной записи хранения **`anystorageaccountname`**;
* вымышленный ключ учетной записи **`P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==`**; 
* точку подключения **`/mnt/share/demoshare`**.

```bash
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```

Эту команду следует выполнить на каждой виртуальной машине в кластере (на главных узлах и узлах агента). Если используется большое количество агентов, мы рекомендуем автоматизировать этот процесс, создав сценарии.  

### <a name="set-up-scripts"></a>Настройка сценариев

1. Сначала установите SSH-подключение к главному узлу (или первому главному узлу) кластера DC/OS. Например, выполните команду `ssh userName@masterFQDN –A –p 22`, где masterFQDN — полное доменное имя главного узла.

2. Скопируйте закрытый ключ в основной рабочий каталог (~) на главном узле.

3. Измените разрешения для него с помощью команды `chmod 600 yourPrivateKeyFile`.

4. Импортируйте закрытый ключ, используя команду `ssh-add yourPrivateKeyFile`. Может потребоваться выполнить команду `eval ssh-agent -s`, если не удастся выполнить эту операцию с первого раза.

5. На главном узле создайте два файла, используя текстовый редактор на свой выбор, например vi, nano или vim. 
  
  * Первый файл — сценарий для выполнения на каждой виртуальной машине, **cifsMount.sh**. 
  * Второй файл, **mountShares.sh**, — сценарий для инициирования всех SSH-подключений, которые будет вызывать первый сценарий.


```bash
# cifsMount.sh

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777
```
  
```bash
# mountShares.sh

# Install jq used for the next command
sudo apt-get install jq

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/demoshare" ]; then sudo mkdir -p "/mnt/share/demoshare" ; fi

# Mount the share on the current vm (master)
sudo mount -t cifs //anystorageaccountname.file.core.windows.net/demoshare /mnt/share/demoshare -o vers=3.0,username=anystorageaccountname,password=P/GuXXXuoRtIVsV+faSfLhuNyZDrTzPmZDm3RyCL4XS6ghyiHYriN12gl+w5JMN2gXGtOhCzxFf2JuGqXXXX1w==,dir_mode=0777,file_mode=0777

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes
  
# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
  do
    ssh `whoami`@$line -o StrictHostKeyChecking=no -i yourPrivateKeyFile < ./cifsMount.sh
    done
```  
> [!IMPORTANT]
> Необходимо изменить команду **mount**, указав собственные параметры, такие как имя учетной записи хранения и пароль.
>  

Теперь в папке, в которой были созданы предыдущие сценарии, имеются 3 файла:  

* **cifsMount.sh**
* **mountShares.sh**
* **файл_вашего_закрытого_ключа** 

### <a name="run-the-scripts"></a>Выполнение сценариев

Выполните файл **mountShares.sh** с помощью команды `sh mountShares.sh`.

Вы должны увидеть результат в окне терминала. После выполнения сценариев можно использовать файловый ресурс в кластере.

Сценарии можно оптимизировать, но этот пример прост и предоставляется для ознакомления.

> [!NOTE] 
> Описанный метод не рекомендуется для сценариев, требующих высокой скорости операций ввода-вывода, но он очень удобен для совместного использования документов и информации в кластере.
>

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте больше об [управлении контейнерами DC/OS](container-service-mesos-marathon-ui.md).
* Управление контейнерами DC/OS с помощью [REST API Marathon](container-service-mesos-marathon-rest.md).
