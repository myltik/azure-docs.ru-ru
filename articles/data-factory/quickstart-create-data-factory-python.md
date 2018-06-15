---
title: Создание фабрики данных Azure с помощью Python | Документация Майкрософт
description: Создайте фабрику данных Azure для копирования данных из одного расположения в хранилище BLOB-объектов Azure в другое.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: python
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 83367d85e70a1a83850388c4cb786e93ae194223
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30173272"
---
# <a name="create-a-data-factory-and-pipeline-using-python"></a>Создание фабрики данных и конвейера с помощью Python
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-python.md)

Фабрика данных Azure — это облачная служба интеграции данных, которая позволяет создавать управляемые данными рабочие процессы в облаке для оркестрации и автоматизации перемещения и преобразования данных. С помощью фабрики данных Azure можно создавать и включать в расписание управляемые данными рабочие процессы (конвейеры), которые могут принимать данные из разнородных хранилищ данных, обрабатывать и преобразовывать эти данные с помощью служб вычислений (например, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics и машинного обучения Azure), а также публиковать выходные данные в хранилища данных (например, хранилище данных SQL Azure) для использования приложениями бизнес-аналитики.

В этом кратком руководстве описано создание фабрики данных Azure с помощью Python. В этой фабрике данных конвейер копирует данные из одной папки в другую в хранилище BLOB-объектов Azure.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

* **Учетная запись хранения Azure.** Хранилище BLOB-объектов используется как хранилище данных **источник** и **приемник**. Если у вас нет учетной записи хранения Azure, ознакомьтесь с разделом [Создание учетной записи хранения](../storage/common/storage-create-storage-account.md#create-a-storage-account).
* [Используйте следующие инструкции](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application), **чтобы создать приложение в Azure Active Directory**. Запишите следующие значения, которые вы используете в следующих шагах: **идентификатор приложения**, **ключ аутентификации** и **идентификатор клиента**. Назначьте приложению роль **Участник**, следуя указаниям в той же статье.

### <a name="create-and-upload-an-input-file"></a>Создание и отправка входного файла

1. Запустите Блокнот. Скопируйте следующий текст и сохраните его в файл **input.txt** на диске.

    ```
    John|Doe
    Jane|Doe
    ```
2.  При помощи таких средств, как [обозреватель службы хранилища Azure](http://storageexplorer.com/), создайте контейнер **adfv2tutorial** с папкой **input**. Затем отправьте файл **input.txt** в папку **input**.

## <a name="install-the-python-package"></a>Установка пакета Python
1. Откройте терминал или командную строку с правами администратора. 
2. Сначала установите пакет Python для ресурсов управления Azure:

    ```
    pip install azure-mgmt-resource
    ```
3. Чтобы установить пакет Python для фабрики данных, выполните следующую команду:

    ```
    pip install azure-mgmt-datafactory
    ```

    [Пакет SDK Python для фабрики данных](https://github.com/Azure/azure-sdk-for-python) поддерживает Python 2.7, 3.3, 3.4, 3.5 и 3.6.

## <a name="create-a-data-factory-client"></a>Создание клиента фабрики данных

1. Создайте файл с именем **datafactory.py**. Добавьте следующие инструкции, чтобы добавить ссылки на пространства имен.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Добавьте следующие инструкции, которые выводят сведения.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))    
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))       
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))           
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))           
        else:
            print("\tErrors: {}".format(activity_run.error['message']))

    ```
3. Добавьте в метод **Main** приведенный ниже код, создающий экземпляр класса DataFactoryManagementClient. Этот объект используется не только для создания фабрики данных, связанной службы, наборов данных и конвейера, но и для отслеживания подробностей выполнения конвейера. Задайте переменную **subscription_id** для идентификатора вашей подписки Azure. Сейчас фабрика данных версии 2 позволяет создавать фабрики данных только в восточной части США, восточной части США 2 и Западной Европе. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других регионах.

    ```python   
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}    
    ```

## <a name="create-a-data-factory"></a>Создать фабрику данных

Добавьте следующий код, создающий **фабрику данных**, в метод **Main**. Если группа ресурсов уже существует, закомментируйте первую инструкцию `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Создание связанной службы

Добавьте следующий код, создающий **связанную службу хранилища Azure**, в метод **Main**.

Связанная служба в фабрике данных связывает хранилища данных и службы вычислений с фабрикой данных. В этом руководстве необходимо создать одну связанную службу хранилища Azure для копирования хранилища-источника и приемника, который в примере называется AzureStorageLinkedService. Замените `<storageaccountname>` и `<storageaccountkey>` именем и ключом учетной записи хранения Azure.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Создание наборов данных
В этом разделе создайте два набора данных: для источника и приемника.

### <a name="create-a-dataset-for-source-azure-blob"></a>Создание набора данных для исходного большого двоичного объекта Azure
Добавьте следующий код, который создает набор данных большого двоичного объекта Azure, в метод Main. Дополнительные сведения о свойствах набора данных большого двоичного объекта Azure см. в [этом разделе](connector-azure-blob-storage.md#dataset-properties).

Задайте набор данных, представляющий исходные данные в большом двоичном объекте Azure. Этот набор данных большого двоичного объекта относится к связанной службе хранилища Azure, созданной на предыдущем шаге.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Создание набора данных для большого двоичного объекта Azure приемника
Добавьте следующий код, который создает набор данных большого двоичного объекта Azure, в метод Main. Дополнительные сведения о свойствах набора данных большого двоичного объекта Azure см. в [этом разделе](connector-azure-blob-storage.md#dataset-properties).

Задайте набор данных, представляющий исходные данные в большом двоичном объекте Azure. Этот набор данных большого двоичного объекта относится к связанной службе хранилища Azure, созданной на предыдущем шаге.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Создание конвейера

Добавьте в метод **Main** следующий код, создающий **конвейер с действием копирования**.

```python
    # Create a copy activity
    act_name =  'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(ds_name)
    dsOut_ref = DatasetReference(dsOut_name)
    copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name =  'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```


## <a name="create-a-pipeline-run"></a>Создание конвейера

Добавьте в метод **Main** следующий код, **активирующий выполнение конвейера**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name,
        {
        }
    )
```

## <a name="monitor-a-pipeline-run"></a>Мониторинг выполнения конвейера
Для мониторинга работы конвейера добавьте следующий код в метод **Main**:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
    print_activity_run_details(activity_runs_paged[0])
```

Теперь добавьте следующую инструкцию, чтобы метод **main** вызывался при запуске программы:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Полный сценарий
Ниже приведен полный код Python:

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)
    print("\n")        

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))    
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))       
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))           
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))           
    else:
        print("\tErrors: {}".format(activity_run.error['message']))

def main():

    # Azure subscription ID
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'        

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name =  'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(ds_name)
    dsOut_ref = DatasetReference(dsOut_name)
    copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name =  'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name,
        {
        }
    )

    # Monitor the pipeilne run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
    print_activity_run_details(activity_runs_paged[0])

# Start the main method
main()
```

## <a name="run-the-code"></a>Выполнение кода
Создайте и запустите приложение, а затем проверьте выполнение конвейера.

Консоль выведет ход выполнения создания фабрики данных, связанной службы, наборов данных, конвейера и выполнения конвейера. Дождитесь появления сведений о действии копирования с размером записанных и прочитанных данных. Затем воспользуйтесь такими средствами, как [обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/), чтобы проверить, скопирован ли большой двоичный объект в outputBlobPath из inputBlobPath, как указано в переменных.

Пример выходных данных:

```json
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```


## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы удалить фабрику данных, добавьте в программу следующий код:

```python
adf_client.factories.delete(rg_name,df_name)
```

## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-dot-net.md), чтобы узнать об использовании фабрики данных в различных сценариях.
