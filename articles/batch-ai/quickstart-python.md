---
title: Краткое руководство Azure по выполнению задания обучения CNTK в Batch AI с помощью Python | Документация Майкрософт
description: Научитесь быстро выполнять задание обучения CNTK в Batch AI, используя пакет SDK для Python
services: batch-ai
documentationcenter: na
author: lliimsft
manager: Vaman.Bedekar
editor: tysonn
ms.assetid: ''
ms.custom: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: Python
ms.topic: quickstart
ms.date: 10/06/2017
ms.author: lili
ms.openlocfilehash: da5c1181f9c4d311bdeabe837435ae4e0eb3dc1a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31513254"
---
# <a name="run-a-cntk-training-job-using-the-azure-python-sdk"></a>Выполнение задания обучения CNTK с использованием пакета Azure SDK для Python

Это краткое руководство содержит сведения об использовании пакета Azure SDK для Python для выполнения задания обучения Microsoft Cognitive Toolkit (CNTK) в службе Batch AI.

В этом примере используются фотографии с рукописным текстом из базы данных MNIST, чтобы обучить сверточную нейронную сеть (CNN) в кластере GPU с одним узлом.

## <a name="prerequisites"></a>предварительным требованиям

* Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

* Пакет Azure SDK для Python. См. [инструкции по установке](/python/azure/python-sdk-azure-install).

* Учетная запись хранения Azure. См. сведения о [создании учетных записей хранения Azure](../storage/common/storage-create-storage-account.md).

* Учетные данные субъекта-службы Azure Active Directory. См. сведения в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md).

* С помощью Azure CLI или Azure Cloud Shell зарегистрируйте поставщики ресурсов Batch AI в своей подписке (это делается один раз). Это может занять до 15 минут.

```azurecli
az provider register -n Microsoft.BatchAI
```

## <a name="configure-credentials"></a>Настройка учетных данных
Добавьте следующий код в файл скрипта и замените `FILL-IN-HERE` соответствующими значениями:

```Python
# credentials used for authentication
aad_client_id = 'FILL-IN-HERE'
aad_secret = 'FILL-IN-HERE'
aad_tenant = 'FILL-IN-HERE'
subscription_id = 'FILL-IN-HERE'

# credentials used for storage
storage_account_name = 'FILL-IN-HERE'
storage_account_key = 'FILL-IN-HERE'

# specify the credentials used to remote login your GPU node
admin_user_name = 'FILL-IN-HERE'
admin_user_password = 'FILL-IN-HERE'
```

Обратите внимание, что ввод учетных данных в исходный код не рекомендуется. Здесь это делается для упрощения.
Попробуйте вместо этого использовать переменные среды или отдельный файл конфигурации.

## <a name="create-batch-ai-client"></a>Создание клиента Batch AI

Следующий код создает объект учетных данных субъекта-службы и клиента Batch AI:

```Python
from azure.common.credentials import ServicePrincipalCredentials
import azure.mgmt.batchai as batchai
import azure.mgmt.batchai.models as models

creds = ServicePrincipalCredentials(
        client_id=aad_client_id, secret=aad_secret, tenant=aad_tenant)

batchai_client = batchai.BatchAIManagementClient(
    credentials=creds, subscription_id=subscription_id)
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Кластеры и задания Batch AI являются ресурсами Azure, которые необходимо поместить в группу ресурсов Azure. В следующем фрагменте кода создается группа ресурсов.

```Python
from azure.mgmt.resource import ResourceManagementClient

resource_group_name = 'myresourcegroup'
resource_management_client = ResourceManagementClient(
        credentials=creds, subscription_id=subscription_id)
resource = resource_management_client.resource_groups.create_or_update(
        resource_group_name, {'location': 'eastus'})
```


## <a name="prepare-azure-file-share"></a>Подготовка файлового ресурса Azure
Для демонстрации в этом кратком руководстве данные для обучения и скрипты будут размещены в файловом ресурсе Azure.

1. Создайте файловый ресурс с именем `batchaiquickstart`.

```Python
from azure.storage.file import FileService
azure_file_share_name = 'batchaiquickstart'
service = FileService(storage_account_name, storage_account_key)
service.create_share(azure_file_share_name, fail_on_exist=False)
```

2. Создайте каталог в файловом ресурсе с именем `mnistcntksample`.

```Python
mnist_dataset_directory = 'mnistcntksample'
service.create_directory(azure_file_share_name, mnist_dataset_directory,
                         fail_on_exist=False)
```
3. Скачайте [пример пакета](https://batchaisamples.blob.core.windows.net/samples/BatchAIQuickStart.zip?st=2017-09-29T18%3A29%3A00Z&se=2099-12-31T08%3A00%3A00Z&sp=rl&sv=2016-05-31&sr=b&sig=hrAZfbZC%2BQ%2FKccFQZ7OC4b%2FXSzCF5Myi4Cj%2BW3sVZDo%3D) и распакуйте его в текущий каталог. Следующий код передает необходимые файлы в файловый ресурс службы "Файлы Azure":

```Python
for f in ['Train-28x28_cntk_text.txt', 'Test-28x28_cntk_text.txt',
          'ConvNet_MNIST.py']:
     service.create_file_from_path(
             azure_file_share_name, mnist_dataset_directory, f, f)
```

## <a name="create-gpu-cluster"></a>Создание кластера GPU

Создайте кластер Batch AI. В этом примере кластер будет состоять из одного узла виртуальной машины STANDARD_NC6. Виртуальная машина такого размера имеет один GPU типа NVIDIA K80. Подключите файловый ресурс к папке с именем `azurefileshare`. Полный путь к этой папке, которая расположена на вычислительном узле GPU, — `$AZ_BATCHAI_MOUNT_ROOT/azurefileshare`.

```Python
cluster_name = 'mycluster'

relative_mount_point = 'azurefileshare'

parameters = models.ClusterCreateParameters(
    # Location where the cluster will physically be deployed
    location='eastus',
    # VM size. Use NC or NV series for GPU
    vm_size='STANDARD_NC6',
    # Configure the ssh users
    user_account_settings=models.UserAccountSettings(
        admin_user_name=admin_user_name,
        admin_user_password=admin_user_password),
    # Number of VMs in the cluster
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=1)
    ),
    # Configure each node in the cluster
    node_setup=models.NodeSetup(
        # Mount shared volumes to the host
        mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=storage_account_key),
                    azure_file_url='https://{0}/{1}'.format(
                        service.primary_endpoint, azure_file_share_name),
                    relative_mount_path=relative_mount_point)],
        ),
    ),
)
batchai_client.clusters.create(resource_group_name, cluster_name,
                               parameters).result()
```

## <a name="get-cluster-status"></a>Получение сведений о состоянии кластера

Отслеживайте состояние кластера, используя следующую команду:

```Python
cluster = batchai_client.clusters.get(resource_group_name, cluster_name)
print('Cluster state: {0} Target: {1}; Allocated: {2}; Idle: {3}; '
      'Unusable: {4}; Running: {5}; Preparing: {6}; Leaving: {7}'.format(
    cluster.allocation_state,
    cluster.scale_settings.manual.target_node_count,
    cluster.current_node_count,
    cluster.node_state_counts.idle_node_count,
    cluster.node_state_counts.unusable_node_count,
    cluster.node_state_counts.running_node_count,
    cluster.node_state_counts.preparing_node_count,
    cluster.node_state_counts.leaving_node_count))
```

Предыдущий код выводит основную информацию о выделении ресурсов в кластере, например:

```
Cluster state: AllocationState.steady Target: 1; Allocated: 1; Idle: 0; Unusable: 0; Running: 0; Preparing: 1; Leaving: 0
```

Когда узлы выделены и подготовка завершена, кластер готов (см. атрибут `nodeStateCounts`). Если что-то пойдет не так, то в атрибуте `errors` будет содержаться описание ошибки.

## <a name="create-training-job"></a>Создание задания обучения

Когда кластер будет готов, настройте и отправьте задание обучения:

```Python
job_name = 'myjob'

parameters = models.job_create_parameters.JobCreateParameters(
    # The job and cluster must be created in the same location
    location=cluster.location,
    # The cluster this job will run on
    cluster=models.ResourceId(id=cluster.id),
    # The number of VMs in the cluster to use
    node_count=1,
    # Write job's standard output and execution log to Azure File Share
    std_out_err_path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(
        relative_mount_point),
    # Configure location of the training script and MNIST dataset
    input_directories=[models.InputDirectory(
        id='SAMPLE',
        path='$AZ_BATCHAI_MOUNT_ROOT/{0}/{1}'.format(
            relative_mount_point, mnist_dataset_directory))],
    # Specify location where generated model will be stored
    output_directories=[models.OutputDirectory(
        id='MODEL',
        path_prefix='$AZ_BATCHAI_MOUNT_ROOT/{0}'.format(relative_mount_point),
        path_suffix="Models")],
    # Container configuration
    container_settings=models.ContainerSettings(
        image_source_registry=models.ImageSourceRegistry(
            image='microsoft/cntk:2.1-gpu-python3.5-cuda8.0-cudnn6.0')),
    # Toolkit specific settings
    cntk_settings=models.CNTKsettings(
        python_script_file_path='$AZ_BATCHAI_INPUT_SAMPLE/ConvNet_MNIST.py',
        command_line_args='$AZ_BATCHAI_INPUT_SAMPLE $AZ_BATCHAI_OUTPUT_MODEL')
)

# Create the job
batchai_client.jobs.create(resource_group_name, job_name, parameters).result()
```

## <a name="monitor-job"></a>Отслеживание задания
С помощью следующего кода можно проверить состояние задания:

```Python
job = batchai_client.jobs.get(resource_group_name, job_name)

print('Job state: {0} '.format(job.execution_state.name))
```

Выходные данные должны быть следующего вида: `Job state: running`.

`executionState` содержит текущее состояние выполнения задания:
* `queued`: задание ожидает, когда узлы кластера станут доступными.
* `running`: задание выполняется.
* `succeeded` (или `failed`): задание завершено и `executionInfo` содержит подробную информацию о результатах.

## <a name="list-stdout-and-stderr-output"></a>Вывод выходных данных stdout и stderr
Используйте следующий код, чтобы отобразить созданные файлы stdout, stderr и файлы журнала:

```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="stdouterr"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="list-generated-model-files"></a>Отображение созданных файлов модели
Используйте следующий код, чтобы отобразить созданные файлы модели:
```Python
files = batchai_client.jobs.list_output_files(
    resource_group_name, job_name,
    models.JobsListOutputFilesOptions(outputdirectoryid="MODEL"))

for file in (f for f in files if f.download_url):
    print('file: {0}, download url: {1}'.format(file.name, file.download_url))
```

## <a name="delete-resources"></a>Удаление ресурсов.

Используйте следующий код, чтобы удалить задание:
```Python
batchai_client.jobs.delete(resource_group_name, job_name)
```

Используйте следующий код, чтобы удалить кластер:
```Python
batchai_client.clusters.delete(resource_group_name, cluster_name)
```

Используйте следующий код, чтобы удалить все выделенные ресурсы:
```Python
resource_management_client.resource_groups.delete('myresourcegroup')
```
## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как запускать задание обучения CNTK в кластере Batch AI, используя пакет Azure SDK для Python. Дополнительные сведения об использовании Batch AI с разными наборами средств см. в [инструкциях по обучению](https://github.com/Azure/BatchAI).
