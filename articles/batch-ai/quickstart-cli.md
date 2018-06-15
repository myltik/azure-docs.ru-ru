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
ms.openlocfilehash: 8d2381f710e87751bd6547c7f435080f185020d5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608551"
---
# <a name="run-a-cntk-training-job-using-the-azure-cli"></a>Выполнение задания обучения CNTK с помощью Azure CLI

Azure CLI 2.0 позволяет создать ресурсы Batch AI и управлять ими — создавать и удалять файловые серверы и кластеры Batch AI, а также отправлять, завершать, удалять и отслеживать задания обучения.

В этом кратком руководстве показано, как создать кластер GPU и выполнить задание обучения с помощью Microsoft Cognitive Toolkit.

Скрипт обучения [ConvNet_MNIST.py](https://github.com/Azure/BatchAI/blob/master/recipes/CNTK/CNTK-GPU-Python/CNTK-GPU-Python.ipynb) доступен на странице GitHub для Batch AI. Этот скрипт обучает сверточную нейронную сеть, используя базу данных образцов рукописного написания цифр MNIST.

Официальный пример CNTK был изменен для приема расположения набора данных для обучения и расположения каталога выходных данных с использованием аргументов командной строки.

## <a name="quickstart-overview"></a>Общие сведения о кратком руководстве

* Создайте кластер GPU с одним узлом (с размером ВМ `Standard_NC6`) с именем `nc6`.
* Создайте учетную запись хранения для входных и выходных данных задания.
* Создайте общий файловый ресурс Azure с двумя папками `logs` и `scripts` для хранения выходных данных задания и скриптов обучения.
* Создайте контейнер больших двоичных объектов Azure `data` для хранения данных для обучения.
* Разверните скрипт обучения и данные для обучения в созданной общей папке и контейнере.
* Настройте задание, чтобы подключить общий файловый ресурс и контейнер больших двоичных объектов Azure на узле кластера и сделать их доступными как обычные файловые системы в `$AZ_BATCHAI_JOB_MOUNT_ROOT/logs`, `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts` и `$AZ_BATCHAI_JOB_MOUNT_ROOT/data`.
`AZ_BATCHAI_JOB_MOUNT_ROOT` является переменной среды, заданной для задания с помощью Batch AI.
* Отслеживайте выполнение задания путем потоковой передачи стандартного потока вывода.
* После завершения задания проверьте его выходные данные и созданные модели.
* В конце удалите все выделенные ресурсы.

# <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* Доступ к Azure CLI 2.0 версии 2.0.31 или более поздней. Вы можете использовать Azure CLI 2.0 в [Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) или установить его локально, следуя [этим инструкциям](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

# <a name="cloud-shell-only"></a>Только Cloud Shell

При использовании Cloud Shell измените рабочую папку на `/usr/$USER/clouddrive`, так как в вашем корневом каталоге нет свободного пространства:

```azurecli
cd /usr/$USER/clouddrive
```

# <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure является логическим контейнером для развертывания ресурсов Azure и управления ими. Следующая команда создает новую группу ресурсов ```batchai.quickstart``` в расположении "Восточная часть США":

```azurecli
az group create -n batchai.quickstart -l eastus
```

# <a name="create-gpu-cluster"></a>Создание кластера GPU

Следующая команда создает кластер GPU с одним узлом (с размером ВМ — Standard_NC6), используя виртуальную машину для обработки и анализа данных под управлением Ubuntu как образ операционной системы:

```azurecli
az batchai cluster create -n nc6 -g batchai.quickstart -s Standard_NC6 -i UbuntuDSVM -t 1 --generate-ssh-keys
```

Виртуальная машина для обработки и анализа данных под управлением Ubuntu позволяет выполнять все задания обучения в контейнерах Docker и запускать наиболее популярные платформы глубокого обучения непосредственно на виртуальной машине.

Параметр `--generate-ssh-keys` запускает Azure CLI для создания открытых и закрытых ключей SSH, если у вас еще их нет. Вы можете получить доступ к узлам кластера, используя текущее имя пользователя и созданный ключ SSH.

Обратите внимание: при использовании Cloud Shell создайте резервную копию папки ~/.ssh в постоянном хранилище.

Выходные данные примера:
```json
{
  "allocationState": "steady",
  "allocationStateTransitionTime": "2018-04-11T21:17:26.345000+00:00",
  "creationTime": "2018-04-11T20:12:10.758000+00:00",
  "currentNodeCount": 0,
  "errors": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
  "location": "eastus",
  "name": "nc6",
  "nodeSetup": null,
  "nodeStateCounts": {
    "additionalProperties": {},
    "idleNodeCount": 0,
    "leavingNodeCount": 0,
    "preparingNodeCount": 0,
    "runningNodeCount": 0,
    "unusableNodeCount": 0
  },
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T20:12:11.445000+00:00",
  "resourceGroup": "batchai.quickstart",
  "scaleSettings": {
    "additionalProperties": {},
    "autoScale": null,
    "manual": {
      "nodeDeallocationOption": "requeue",
      "targetNodeCount": 1
    }
  },
  "subnet": null,
  "tags": null,
  "type": "Microsoft.BatchAI/Clusters",
  "userAccountSettings": {
    "additionalProperties": {},
    "adminUserName": "alex",
    "adminUserPassword": null,
    "adminUserSshPublicKey": "<YOUR SSH PUBLIC KEY HERE>"
  },
  "virtualMachineConfiguration": {
    "additionalProperties": {},
    "imageReference": {
      "additionalProperties": {},
      "offer": "linux-data-science-vm-ubuntu",
      "publisher": "microsoft-ads",
      "sku": "linuxdsvmubuntu",
      "version": "latest",
      "virtualMachineImageId": null
    }
  },
  "vmPriority": "dedicated",
  "vmSize": "STANDARD_NC6"
}
```

# <a name="create-a-storage-account"></a>Создание учетной записи хранения

Следующая команда создает учетную запись хранения в одном регионе с группой ресурсов batchai.repices. Обновите команду, указав уникальное имя учетной записи хранения.

```azurecli
az storage account create -n <storage account name> --sku Standard_LRS -g batchai.quickstart
```

Если выбранное имя учетной записи хранилища недоступно, то приведенная выше команда выдаст соответствующую ошибку. В этом случае выберите другое имя и повторите попытку.

# <a name="data-deployment"></a>Развертывание данных

## <a name="download-the-training-script-and-training-data"></a>Загрузка скрипта обучения и данных для обучения

* Загрузите и извлеките предварительно обработанную базу данных MNIST из [этого расположения](https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D) в текущую папку.

Для GNU/Linux или Cloud Shell:

```azurecli
wget "https://batchaisamples.blob.core.windows.net/samples/mnist_dataset.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=PmhL%2BYnYAyNTZr1DM2JySvrI12e%2F4wZNIwCtf7TRI%2BM%3D" -O mnist_dataset.zip
unzip mnist_dataset.zip
```

Обратите внимание: может потребоваться установить `unzip`, если этой программы нет в дистрибутиве GNU/Linux.

* Загрузите пример скрипта [ConvNet_MNIST.py](https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py) в текущую папку:

Для GNU/Linux или Cloud Shell:

```azurecli
wget https://raw.githubusercontent.com/Azure/BatchAI/master/recipes/CNTK/CNTK-GPU-Python/ConvNet_MNIST.py
```

## <a name="create-azure-file-share-and-deploy-the-training-script"></a>Создание общего файлового ресурса Azure и развертывание скрипта обучения

Следующие команды создают общие файловые ресурсы Azure `scripts` и `logs` и копируют скрипт обучения в папку `cntk` внутри общего ресурса `scripts`:

```azurecli
az storage share create -n scripts --account-name <storage account name>
az storage share create -n logs --account-name <storage account name>
az storage directory create -n cntk -s scripts --account-name <storage account name>
az storage file upload -s scripts --source ConvNet_MNIST.py --path cntk --account-name <storage account name> 
```

## <a name="create-a-blob-container-and-deploy-training-data"></a>Создание контейнера больших двоичных объектов и развертывание данных для обучения

Следующие команды создают контейнер больших двоичных объектов Azure `data` и копируют данные для обучения в папку `mnist_cntk`:
```azurecli
az storage container create -n data --account-name <storage account name>
az storage blob upload-batch -s . --pattern '*28x28_cntk*' --destination data --destination-path mnist_cntk --account-name <storage account name>
```

# <a name="submit-training-job"></a>Отправка задания обучения

## <a name="prepare-job-configuration-file"></a>Подготовка файла конфигурации задания

Создайте файл конфигурации задания обучения `job.json` со следующим содержимым:
```json
{
    "$schema": "https://raw.githubusercontent.com/Azure/BatchAI/master/schemas/2018-03-01/cntk.json",
    "properties": {
        "nodeCount": 1,
        "cntkSettings": {
            "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py",
            "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL"
        },
        "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
        "outputDirectories": [{
            "id": "MODEL",
            "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs"
        }],
        "mountVolumes": {
            "azureFileShares": [
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/logs",
                    "relativeMountPath": "logs"
                },
                {
                    "azureFileUrl": "https://<AZURE_BATCHAI_STORAGE_ACCOUNT>.file.core.windows.net/scripts",
                    "relativeMountPath": "scripts"
                }
            ],
            "azureBlobFileSystems": [
                {
                    "accountName": "<AZURE_BATCHAI_STORAGE_ACCOUNT>",
                    "containerName": "data",
                    "relativeMountPath": "data"
                }
            ]
        }
    }
}
```

Этот файл конфигурации задает:

* `nodeCount` — количество узлов, необходимых для задания (1 для этого краткого руководства).
* `cntkSettings` — указывает путь скрипта обучения и аргументы командной строки. Аргументы командной строки включают путь к данным для обучения и конечный путь для сохранения созданных моделей. `AZ_BATCHAI_OUTPUT_MODEL` является переменной среды, заданной Batch AI в зависимости от конфигурации каталога выходных данных (см. ниже).
* `stdOutErrPathPrefix` — путь, где Batch AI создаст каталоги, содержащие выходные данные задания и журналы.
* `outputDirectories` — коллекция каталогов выходных данных, создаваемая Batch AI. Для каждого каталога Batch AI создает переменную среды с именем `AZ_BATCHAI_OUTPUT_<id>`, где `<id>` — идентификатор каталога.
* `mountVolumes` — список файловых систем для подключения во время выполнения задания. Файловые системы подключаются в пути `AZ_BATCHAI_JOB_MOUNT_ROOT/<relativeMountPath>`. `AZ_BATCHAI_JOB_MOUNT_ROOT` является переменной среды, заданной с помощью Batch AI.
* `<AZURE_BATCHAI_STORAGE_ACCOUNT>` сообщает, что имя учетной записи хранения будет указано во время отправки задания с помощью параметра storage-account-name или переменной среды `AZURE_BATCHAI_STORAGE_ACCOUNT` на компьютере.

## <a name="submit-the-job"></a>Отправка задания

Используйте следующую команду, чтобы отправить задание на кластер:

```azurecli
az batchai job create -n cntk_python_1 -r nc6 -g batchai.quickstart -c job.json --storage-account-name <storage account name>
```

Выходные данные примера:
```
{
  "additionalProperties": {},
  "caffeSettings": null,
  "chainerSettings": null,
  "cluster": {
    "additionalProperties": {},
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/clusters/nc6",
    "resourceGroup": "batchai.quickstart"
  },
  "cntkSettings": {
    "additionalProperties": {},
    "commandLineArgs": "$AZ_BATCHAI_JOB_MOUNT_ROOT/data/mnist_cntk $AZ_BATCHAI_OUTPUT_MODEL",
    "configFilePath": null,
    "languageType": "Python",
    "processCount": 1,
    "pythonInterpreterPath": null,
    "pythonScriptFilePath": "$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/cntk/ConvNet_MNIST.py"
  },
  "constraints": {
    "additionalProperties": {},
    "maxWallClockTime": "7 days, 0:00:00"
  },
  "containerSettings": null,
  "creationTime": "2018-04-11T21:48:10.303000+00:00",
  "customToolkitSettings": null,
  "environmentVariables": null,
  "executionInfo": null,
  "executionState": "queued",
  "executionStateTransitionTime": "2018-04-11T21:48:10.303000+00:00",
  "experimentName": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/batchai.quickstart/providers/Microsoft.BatchAI/jobs/cntk_python_1",
  "inputDirectories": null,
  "jobOutputDirectoryPathSegment": "00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/b9576bae-e878-4fb2-9390-2e962356b5b1",
  "jobPreparation": null,
  "location": null,
  "mountVolumes": {
    "additionalProperties": {},
    "azureBlobFileSystems": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "containerName": "data",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "mountOptions": null,
        "relativeMountPath": "data"
      }
    ],
    "azureFileShares": [
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>,
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/logs",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "logs"
      },
      {
        "accountName": "<YOU STORAGE ACCOUNT NAME>",
        "additionalProperties": {},
        "azureFileUrl": "https://<YOU STORAGE ACCOUNT NAME>.file.core.windows.net/scripts",
        "credentials": {
          "accountKey": null,
          "accountKeySecretReference": null,
          "additionalProperties": {}
        },
        "directoryMode": "0777",
        "fileMode": "0777",
        "relativeMountPath": "scripts"
      }
    ],
    "fileServers": null,
    "unmanagedFileSystems": null
  },
  "name": "cntk_python_1",
  "nodeCount": 1,
  "outputDirectories": [
    {
      "additionalProperties": {},
      "createNew": true,
      "id": "MODEL",
      "pathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
      "pathSuffix": null,
      "type": "custom"
    }
  ],
  "priority": 0,
  "provisioningState": "succeeded",
  "provisioningStateTransitionTime": "2018-04-11T21:48:11.577000+00:00",
  "pyTorchSettings": null,
  "resourceGroup": "batchai.quickstart",
  "secrets": null,
  "stdOutErrPathPrefix": "$AZ_BATCHAI_JOB_MOUNT_ROOT/logs",
  "tags": null,
  "tensorFlowSettings": null,
  "toolType": "cntk",
  "type": "Microsoft.BatchAI/Jobs"
}
```

# <a name="monitor-job-execution"></a>Отслеживание выполнения задания

Скрипт обучения сообщает о ходе выполнения обучения в файл `stderr.txt` внутри каталога стандартного потока вывода. Вы можете отслеживать ход выполнения с помощью следующей команды:

```azurecli
az batchai job file stream -n cntk_python_1 -g batchai.quickstart -f stderr.txt
```

Выходные данные примера:
```
File found with URL "https://<YOU STORAGE ACCOUNT>.file.core.windows.net/logs/00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>/stdouterr/stderr.txt?sv=2016-05-31&sr=f&sig=n86JK9YowV%2BPQ%2BkBzmqr0eud%2FlpRB%2FVu%2FFlcKZx192k%3D&se=2018-04-11T23%3A05%3A54Z&sp=rl". Start streaming
Selected GPU[0] Tesla K80 as the process wide default device.
-------------------------------------------------------------------
Build info:

        Built time: Jan 31 2018 15:03:41
        Last modified date: Tue Jan 30 03:26:13 2018
        Build type: release
        Build target: GPU
        With 1bit-SGD: no
        With ASGD: yes
        Math lib: mkl
        CUDA version: 9.0.0
        CUDNN version: 7.0.4
        Build Branch: HEAD
        Build SHA1: a70455c7abe76596853f8e6a77a4d6de1e3ba76e
        MPI distribution: Open MPI
        MPI version: 1.10.7
-------------------------------------------------------------------
Training 98778 parameters in 10 parameter tensors.

Learning rate per 1 samples: 0.001
Momentum per 1 samples: 0.0
Finished Epoch[1 of 40]: [Training] loss = 0.405960 * 60000, metric = 13.01% * 60000 21.741s (2759.8 samples/s);
Finished Epoch[2 of 40]: [Training] loss = 0.106030 * 60000, metric = 3.09% * 60000 3.638s (16492.6 samples/s);
Finished Epoch[3 of 40]: [Training] loss = 0.078542 * 60000, metric = 2.32% * 60000 3.477s (17256.3 samples/s);
...
Final Results: Minibatch[1-11]: errs = 0.54% * 10000
```

Потоковая передача прекращается в момент завершения задания ("Успешно" или "Сбой").

# <a name="inspect-generated-model-files"></a>Проверка созданных файлов модели

Задание хранит созданные файлы моделей в каталоге выходных данных с атрибутом `id` равным `MODEL`. Вы можете вывести список файлов моделей и получить URL-адреса скачивания, используя следующую команду:

```azurecli
az batchai job file list -n cntk_python_1 -g batchai.quickstart -d MODEL
```

Выходные данные примера:
```
[
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:51+00:00",
    "name": "ConvNet_MNIST_0.dnn"
  },
  {
    "additionalProperties": {},
    "contentLength": 409456,
    "downloadUrl": "https://<YOUR STORAGE ACCOUNT>.file.core.windows.net/...",
    "isDirectory": false,
    "lastModified": "2018-04-11T22:05:55+00:00",
    "name": "ConvNet_MNIST_1.dnn"
  },
...

```

Вы также можете использовать портал и обозреватель службы хранилища Azure, чтобы изучить созданные файлы. Чтобы отличить выходные данные из разных заданий, Batch AI создает уникальную структуру папок для каждого из них. Вы можете найти путь к папке, содержащей выходные данные, с помощью атрибута `jobOutputDirectoryPathSegment` для отправленного задания:

```azurecli
az batchai job show -n cntk_python_1 -g batchai.quickstart --query jobOutputDirectoryPathSegment
```

Выходные данные примера:
```
"00000000-0000-0000-0000-000000000000/batchai.quickstart/jobs/cntk_python_1/<JOB's UUID>"
```

# <a name="delete-resources"></a>Удаление ресурсов

Удалите группу ресурсов и все выделенные ресурсы с помощью следующей команды:

```azurecli
az group delete -n batchai.quickstart -y
```
