---
title: Краткое руководство Azure по выполнению задания обучения CNTK в Batch AI с помощью Azure CLI | Документация Майкрософт
description: Научитесь быстро выполнять задание обучения CNTK в Batch AI, используя Azure CLI
services: batch-ai
documentationcenter: na
author: AlexanderYukhanov
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: Alexander.Yukhanov
ms.openlocfilehash: 82e3885021a2f2309dfed456d472e7027b8d6cf2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Выполнение задания обучения CNTK с помощью Azure CLI

Это краткое руководство содержит сведения об использовании интерфейса командной строки Azure для выполнения задания обучения Microsoft Cognitive Toolkit (CNTK) в службе Batch AI. Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов.

В этом примере используются фотографии с рукописным текстом из базы данных MNIST, чтобы обучить сверточную нейронную сеть (CNN) в кластере GPU с одним узлом, управляемым службой Batch AI. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для этого руководства требуется Azure CLI последней версии. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

С помощью Azure CLI или Azure Cloud Shell необходимо единожды зарегистрировать поставщики ресурсов Batch AI в своей подписке. Это может занять до 15 минут.

```azurecli
az provider register -n Microsoft.BatchAI
az provider register -n Microsoft.Batch
```


## <a name="create-a-resource-group"></a>Создание группы ресурсов

Кластеры и задания Batch AI являются ресурсами Azure, которые необходимо поместить в группу ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create).

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*. Затем для установки этой группы ресурсов и расположения в качестве значений по умолчанию используйте команду [az configure](/cli/azure/reference-index#az_configure).

```azurecli
az group create --name myResourceGroup --location eastus

az configure --defaults group=myResourceGroup

az configure --defaults location=eastus
```

>[!NOTE]
>Установка значений по умолчанию для команды `az` является необязательным шагом. Значения по умолчанию можно не задавать. Если вы решили установить значения по умолчанию, то после завершения работы с руководством необходимо их удалить. Для этого используйте следующие команды:
>
>```azurecli
>az configure --defaults group=''
>
>az configure --defaults location=''
>```
>

## <a name="create-a-storage-account"></a>Создайте учетную запись хранения.

В этом кратком руководстве для размещения данных и скриптов для задания обучения используется учетная запись службы хранения Azure. Создайте учетную запись хранения с помощью команды [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli
az storage account create --name mystorageaccount --sku Standard_LRS
```

>[!NOTE]
>У каждой учетной записи хранения должно быть уникальное имя. При использовании предыдущей команды `az` и других аналогичных команд в этом руководстве замените значение параметра `mystorageaccount` на имя вашей учетной записи хранения.

## <a name="prepare-azure-file-share"></a>Подготовка файлового ресурса Azure

Для целей демонстрации в этом кратком руководстве данные для обучения и скрипты будут размещены в файловом ресурсе Azure.

1. Создайте файловый ресурс с именем *batchaiquickstart* с помощью команды [az storage share create](/cli/azure/storage/share#az_storage_share_create).

  ```azurecli
  az storage share create --account-name mystorageaccount --name batchaiquickstart
  ```
2. В общем ресурсе создайте каталог с именем *mnistcntksample* с помощью команды [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create).

  ```azurecli
  az storage directory create --share-name batchaiquickstart  --name mnistcntksample
  ```

3. Загрузите [образец пакета](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) и распакуйте его. Отправьте содержимое в каталог с помощью команды [az storage file upload](/cli/azure/storage/file#az_storage_file_upload).

  ```azurecli
  az storage file upload --share-name batchaiquickstart --source Train-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source Test-28x28_cntk_text.txt --path mnistcntksample

  az storage file upload --share-name batchaiquickstart --source ConvNet_MNIST.py --path mnistcntksample
  ```


## <a name="create-gpu-cluster"></a>Создание кластера GPU
Для создания кластера Batch AI, который состоит из единого узла виртуальной машины с GPU, используйте команду [az batchai cluster create](/cli/azure/batchai/cluster#az_batchai_cluster_create). В этом примере виртуальная машина использует образ Ubuntu LTS по умолчанию. Вместо него укажите `image UbuntuDSVM`, чтобы запустить виртуальную машину глубокого обучение Майкрософт, которая поддерживает дополнительные платформы обучения. Виртуальная машина размера NC6 имеет один GPU типа NVIDIA K80. Подключите файловый ресурс к папке с именем *azurefileshare*. Полный путь к этой папке, которая находится на вычислительном узле GPU, — $AZ_BATCHAI_MOUNT_ROOT/azurefileshare.


```azurecli
az batchai cluster create --name mycluster --vm-size STANDARD_NC6 --image UbuntuLTS --min 1 --max 1 --storage-account-name mystorageaccount --afs-name batchaiquickstart --afs-mount-path azurefileshare --user-name <admin_username> --password <admin_password>
```


После создания кластера выходные данные выглядят так:

```azurecli
{
  "allocationState": "resizing",
  "allocationStateTransitionTime": "2017-10-05T02:09:03.194000+00:00",
  "creationTime": "2017-10-05T02:09:01.998000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
  "location": "eastus",
  "name": "mycluster",
  "nodeSetup": {
    "mountVolumes": {
      "azureBlobFileSystems": null,
      "azureFileShares": [
        {
          "accountName": "batchaisamples",
          "azureFileUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart",
          "credentialsInfo": {
            "accountKey": null,
            "accountKeySecretUrl": null
          },
          "directoryMode": "0777",
          "fileMode": "0777",
          "relativeMountPath": "azurefileshare"
        }
      ],
      "fileServers": null,
      "unmanagedFileSystems": null
    },
    "setupTask": null
  },
  "nodeStateCounts": {
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T02:09:02.857000+00:00",
  "resourceGroup": "myresourcegroup",
  "scaleSettings": {
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": {
    "id": null
  },
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "adminUserName": "demoUser",
    "adminUserPassword": null,
    "adminUserSshPublicKey": null
  },
  "virtualMachineConfiguration": {
    "imageReference": {
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04-LTS",
      "version": "latest"
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
```
## <a name="get-cluster-status"></a>Получение сведений о состоянии кластера

Чтобы получить общие сведения о состоянии кластера, выполните команду [az batchai cluster list](/cli/azure/batchai/cluster#az_batchai_cluster_list).

```azurecli
az batchai cluster list -o table
```

Результат аналогичен приведенному ниже:

```azurecli
Name        Resource Group    VM Size        State     Idle    Running    Preparing    Unusable    Leaving
---------   ----------------  -------------  -------   ------  ---------  -----------  ----------  --------
mycluster   myresourcegroup   STANDARD_NC6   steady    1       0          0            0            0
```

Для получения дополнительных сведений выполните команду [az batchai cluster show](/cli/azure/batchai/cluster#az_batchai_cluster_show). Она возвращает все свойства кластера, показанные после создания кластера.

Когда узлы выделены и подготовка завершена, кластер готов (см. атрибут `nodeStateCounts`). Если что-то пойдет не так, то в атрибуте `errors` будет содержаться описание ошибки.

## <a name="create-training-job"></a>Создание задания обучения

Когда кластер будет готов, настройте и отправьте задание обучения.

1. Создайте файл шаблона JSON "job.json" для созданного задания.

  ```JSON
  {
    "properties": {
        "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
       "inputDirectories": [{
            "id": "SAMPLE",
            "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
        }],
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
            "pathSuffix": "model",
            "type": "custom"
        }],
        "containerSettings": {
            "imageSourceRegistry": {
                "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0"
            }
        },
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL"
        }
    }
  }
  ```
2. Создайте задание *myjob*, которое будет выполняться на кластере, с помощью команды [az batchai job create](/cli/azure/batchai/job#az_batchai_job_create).

  ```azurecli
  az batchai job create --name myjob --cluster-name mycluster --config job.json
  ```

Результат аналогичен приведенному ниже:

```azurecli
{
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.BatchAI/clusters/mycluster",
    "resourceGroup": "myresourcegroup"
  },
  "cntkSettings": {
    "commandLineArgs": "$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py"
  },
  "constraints": {
    "maxTaskRetryCount": null,
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": {
    "imageSourceRegistry": {
      "credentials": null,
      "image": "microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0",
      "serverUrl": null
    }
  },
  "creationTime": "2017-10-05T06:41:42.163000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": {
    "endTime": null,
    "errors": null,
    "exitCode": null,
    "lastRetryTime": null,
    "retryCount": null,
    "startTime": "2017-10-05T06:41:44.392000+00:00"
  },
  "executionState": "running",
  "executionStateTransitionTime": "2017-10-05T06:41:44.953000+00:00",
  "experimentName": null,
  "id": "/subscriptions/10d0b7c6-9243-4713-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.BatchAI/jobs/myjob",
  "inputDirectories": [
    {
      "id": "SAMPLE",
      "path": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare/mnistcntksample"
    }
  ],
  "jobPreparation": null,
  "location": null,
  "name": "cntk_job",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
      "pathSuffix": "model",
      "type": "Custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2017-10-05T06:41:44.238000+00:00",
  "resourceGroup": "demo",
  "stdOutErrPathPrefix": "$AZ_BATCHAI_MOUNT_ROOT/azurefileshare",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "CNTK",
  "type": "Microsoft.BatchAI/Jobs"
}
```

## <a name="monitor-job"></a>Отслеживание задания

Для получения сведений о состоянии задания запустите команду [az batchai job list](/cli/azure/batchai/job#az_batchai_job_list).

```azurecli
az batchai job list -o table
```

Результат аналогичен приведенному ниже:

```azurecli
Name        Resource Group    Cluster    Cluster RG      Nodes  State    Exit code
----------  ----------------  ---------  --------------- -----  -------  -----------
myjob       myresourcegroup   mycluster  myresourcegroup 1      running

```

Для получения дополнительных сведений запустите команду [az batchai job show](/cli/azure/batchai/job#az_batchai_job_show).

`executionState` содержит текущее состояние выполнения задания:

* `queued`: задание ожидает, когда узлы кластера станут доступными.
* `running`: задание выполняется.
*   `succeeded` (или `failed`): задание завершено и `executionInfo` содержит подробную информацию о результатах.


## <a name="list-stdout-and-stderr-output"></a>Вывод выходных данных stdout и stderr
Для отображения ссылок на файлы журналов stdout и stderr используйте команду [az batchai job list-files](/cli/azure/batchai/job#az_batchai_job_list_files).

```azurecli
az batchai job list-files --name myjob --output-directory-id stdouterr
```

Результат аналогичен приведенному ниже:

```azurecli
[
  {
    "contentLength": 733,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stderr.txt?sv=2016-05-31&sr=f&sig=Rh%2BuTg9C1yQxm7NfA9YWiKb%2B5FRKqWmEXiGNRDeFMd8%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:38+00:00",
    "name": "stderr.txt"
  },
  {
    "contentLength": 300,
    "downloadUrl": "https://batchaisamples.file.core.windows.net/batchaiquickstart/10d0b7c6-9243-4713-91a9-2730375d3a1b/demo/jobs/cntk_job/stdout.txt?sv=2016-05-31&sr=f&sig=jMhJfQOGry9jr4Hh3YyUFpW5Uaxnp38bhVWNrTTWMtk%3D&se=2017-10-05T07%3A44%3A38Z&sp=rl",
    "lastModified": "2017-10-05T06:44:29+00:00",
    "name": "stdout.txt"
  }
]
```


## <a name="observe-output"></a>Просмотр выходных данных

Во время выполнения задания вы можете передавать его выходные файлы потоком или просматривать их заключительный фрагмент. Например, для передачи файла журнала stderr.txt можно использовать команду [az batchai job stream-file](/cli/azure/batchai/job#az_batchai_job_stream_file).

```azurecli
az batchai job stream-file --job-name myjob --output-directory-id stdouterr --name stderr.txt
```

Результат аналогичен приведенному ниже. Прервать получение выходных данных можно нажатием комбинации клавиш CTRL+C.

```azurecli
…
Finished Epoch[2 of 40]: [Training] loss = 0.104846 * 60000, metric = 3.00% * 60000 3.849s (15588.5 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.077043 * 60000, metric = 2.23% * 60000 3.902s (15376.7 samples/s);
Finished Epoch[4 of 40]: [Training] loss = 0.063050 * 60000, metric = 1.82% * 60000 3.811s (15743.9 samples/s);
…

```

## <a name="delete-resources"></a>Удаление ресурсов.

Для удаления задания используйте команду [az batchai job delete](/cli/azure/batchai/job#az_batchai_job_delete).

```azurecli
az batchai job delete --name myjob
```
Для удаления кластера используйте команду [az batchai cluster delete](/cli/azure/batchai/cluster#az_batchai_cluster_delete).

```azurecli
az batchai cluster delete --name mycluster
```

Для удаления группы ресурсов, созданной при работе с этим кратким руководством, используйте команду `az group delete`.

```azurecli
az group delete --name myResourceGroup
```

## <a name="restore-azure-cli-20-default-settings"></a>Восстановление параметров по умолчанию в Azure CLI 2.0

Удалите ранее настроенные параметры по умолчанию для расположения и группы ресурсов.

```azurecli
az configure --defaults group=''

az configure --defaults location=''
```

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как запускать задание обучения CNTK в кластере Batch AI с помощью Azure CLI. Дополнительные сведения об использовании Batch AI с разными наборами средств см. в [инструкциях по обучению](https://github.com/Azure/BatchAI).

Дополнительные сведения об использовании Azure CLI 2.0 для управления службой Batch AI см. в [документации на GitHub](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
