<properties
	pageTitle="Руководство. Приступая к работе с клиентом Python пакетной службы Azure | Microsoft Azure"
	description="Основные понятия пакетной службы Azure и использование пакетной службы в простом сценарии"
	services="batch"
	documentationCenter="python"
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/27/2016"
	ms.author="marsma"/>

# Приступая к работе с клиентом Python пакетной службы Azure

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Здесь представлены основные сведения о [пакетной службе Azure][azure_batch] и клиенте [Python пакетной службы][py_azure_sdk] в рамках обсуждения небольшого приложения пакетной службы Azure, написанного на языке Python. Мы рассмотрим, как два примера скрипта используют пакетную службу для обработки параллельной рабочей нагрузки на виртуальных машинах Linux в облаке и взаимодействуют со [службой хранилища Azure](./../storage/storage-introduction.md) при промежуточном хранении и извлечении файлов. Вы узнаете об общем рабочем процессе приложения пакетной службы и получите базовые знания о главных компонентах пакетной службы, например заданиях, задачах, пулах и вычислительных узлах.

![Рабочий процесс решения пакетной службы (основной)][11]<br/>

## Предварительные требования

В этой статье предполагается, что вы уже работали с Python и знаете, как работать в Linux. Также предполагается, что вы можете выполнить требования к созданию учетной записи для службы хранилища и пакетной службы Azure. Эти требования перечислены ниже.

### Учетные записи

- **Учетная запись Azure**. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure][azure_free_account].
- **Учетная запись пакетной службы**. Если у вас есть подписка Azure, [создайте учетную запись пакетной службы Azure](batch-account-create-portal.md).
- **Учетная запись хранения**. См. раздел [Создание учетной записи хранения](../storage/storage-create-storage-account.md#create-a-storage-account) в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

### Пример кода

[Пример кода][github_article_samples] Python для руководства — это один из многих примеров кода пакетной службы в репозитории [azure-batch-samples][github_samples] на сайте GitHub. Чтобы скачать все примеры, на домашней странице репозитория последовательно выберите **Clone or download (Клонировать или скачать) > Download ZIP (Скачать ZIP-файл)** или щелкните ссылку [azure-batch-samples-master.zip][github_samples_zip] и скачайте их напрямую. После извлечения содержимого ZIP-файла оба сценария для этого руководства будут находиться в каталоге `article_samples`:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/> `/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### Среда Python

Чтобы запустить пример скрипта *python\_tutorial\_client.py* на локальной рабочей станции, необходим **интерпретатор Python**, совместимый с версией **2.7** или **3.3+**. Сценарий прошел испытания в Linux и Windows.

### Зависимости шифрования

Для библиотеки [шифрования][crypto], которая требуется пакетам Python `azure-batch` и `azure-storage`, необходимо установить зависимости. Выполните одну из следующих операций, подходящих для вашей платформы, или обратитесь к сведениям об [установке шифрования][crypto_install]\:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`

* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`

* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`

* Windows

    `pip install cryptography`

>[AZURE.NOTE] При установке для версии Python 3.3+ в Linux используйте эквиваленты python3 для зависимостей Python. Например, в Ubuntu: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`

### Пакеты Azure

Далее установите пакеты Python **пакетной службы Azure** и **службы хранилища Azure**. Это можно сделать с помощью команды **pip** и файла *requirements.txt*, который находится здесь:

.`/azure-batch-samples/Python/Batch/requirements.txt`

Выполните команду **pip**, чтобы установить пакеты службы хранения и пакетной службы.

`pip install -r requirements.txt`

Кроме того, пакеты Python [azure-batch][pypi_batch] и [azure-storage][pypi_storage] можно установить вручную.

`pip install azure-batch`<br/> `pip install azure-storage`

> [AZURE.TIP] Если вы используете непривилегированную учетную запись, может понадобиться добавить к команде префикс `sudo`. Например, `sudo pip install -r requirements.txt`. Дополнительные сведения об установке пакетов Python см. в статье [Installing Packages][pypi_install] (Установка пакетов) на сайте readthedocs.io.

## Пример кода Python для руководства по пакетной службе

Пример кода Python для руководства по пакетной службе состоит из двух сценариев Python и нескольких файлов данных:

- **python\_tutorial\_client.py** взаимодействует с пакетной службой и службой хранилища, чтобы выполнять параллельную рабочую нагрузку на вычислительных узлах (виртуальных машинах). Скрипт *python\_tutorial\_client.py* выполняется на локальной рабочей станции.

- **python\_tutorial\_task.py** — это скрипт, который запускается на вычислительных узлах в Azure, чтобы выполнять фактическую работу. В этом примере *python\_tutorial\_task.py* анализирует текст во входном файле, скачанном из службы хранилища Azure. Затем он создает текстовый файл (выходной файл), который содержит список из трех наиболее часто употребляемых слов во входном файле. После создания выходного файла *python\_tutorial\_task.py* отправляет его в службу хранилища Azure. После этого он станет доступен для скачивания в сценарий клиента, запущенного на рабочей станции. Скрипт *python\_tutorial\_task.py* выполняется параллельно на нескольких вычислительных узлах в пакетной службе.

- Три текстовых файла **./data/taskdata*.txt** обеспечивают ввод данных для задач, которые выполняются на вычислительных узлах.

На следующей схеме показаны основные операции, выполняемые с помощью сценариев клиента и задач. Этот основной рабочий процесс является типичным для многих вычислительных решений, созданных с помощью пакетной службы. Хотя он не демонстрирует все возможности, доступные в пакетной службе, почти все ее сценарии включают в себя части этого рабочего процесса.

![Пример рабочего процесса пакетной службы][8]<br/>

[**Шаг 1.**](#step-1-create-storage-containers) Создайте **контейнеры** в хранилище BLOB-объектов Azure.<br/> [**Шаг 2.**](#step-2-upload-task-script-and-data-files) Отправьте скрипт задач и входные файлы в контейнеры.<br/> [**Шаг 3.**](#step-3-create-batch-pool) Создайте **пул** пакетной службы.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**3.1.** Задача **StartTask** пула скачивает скрипт задач (python\_tutorial\_task.py) на узлы во время их присоединения к пулу.<br/> [**Шаг 4.**](#step-4-create-batch-job) Создайте **задание** пакетной службы.<br/> [**Шаг 5.**](#step-5-add-tasks-to-job) Добавьте **задачи** в задание.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5.1.** Планируется выполнение задач на узлах.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**5.2.** Каждая задача скачивает свои входные данные из службы хранилища Azure, а затем начинает выполнение.<br/> [**Шаг 6.**](#step-6-monitor-tasks) Отслеживайте задачи.<br/> &nbsp;&nbsp;&nbsp;&nbsp;**6.1.** После выполнения задач их выходные данные отправляются в службу хранилища Azure.<br/> [**Шаг 7.**](#step-7-download-task-output) Скачайте выходные данные задачи из службы хранилища.

Как уже упоминалось, не каждое решение пакетной службы будет выполнять именно эти шаги. Некоторые решения могут выполнять больше действий, но этот пример демонстрирует общие процессы в решении пакетной службы.

## Подготовка сценария клиента

Прежде чем запустить пример, добавьте учетные данные учетных записей пакетной службы и службы хранилища в скрипт *python\_tutorial\_client.py*. Откройте файл в своем редакторе и обновите следующие строки, используя учетные данные, если еще не сделали этого.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

Учетные данные учетных записей пакетной службы и службы хранилища можно найти в колонке учетной записи каждой службы на [портале Azure][azure_portal].

![Учетные данные пакетной службы на портале][9] ![Учетные данные службы хранилища на портале][10]<br/>

В следующих разделах мы проанализируем шаги, выполняемые в скриптах для обработки рабочей нагрузки в пакетной службе. Во время работы с оставшейся частью статьи рекомендуем регулярно просматривать сценарии в редакторе.

Перейдите на следующую строку в скрипте **python\_tutorial\_client.py**, чтобы выполнить шаг 1.

```python
if __name__ == '__main__':
```

## Шаг 1. Создание контейнеров службы хранилища

![Создание контейнеров в службе хранилища Azure][1] <br/>

Пакетная служба включает встроенную поддержку для взаимодействия со службой хранилища Azure. Контейнеры в учетной записи хранения предоставляют файлы, которые нужны для выполнения задач, запускаемых в вашей учетной записи пакетной службы. Контейнеры также предоставляют место для хранения выходных данных, создаваемых задачами. Сначала скрипт *python\_tutorial\_client.py* создает три контейнера в [хранилище BLOB-объектов Azure](../storage/storage-introduction.md#blob-storage).

- **application** — в этом контейнере будет храниться скрипт Python *python\_tutorial\_task.py*, выполняемый задачами.
- **input** — задачи будут скачивать файлы данных, которые они должны обрабатывать, из контейнера *input*.
- **output** — после завершения обработки входных файлов задачи будут отправлять результаты обработки в контейнер *output*.

Чтобы скрипт мог взаимодействовать с учетной записью службы хранилища и создавать контейнеры, мы используем пакет [azure-storage][pypi_storage] для создания объекта [BlockBlobService][py_blockblobservice], клиента службы BLOB-объектов. Затем с помощью клиента BLOB-объектов мы создадим три контейнера в учетной записи службы хранилища.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

Когда контейнеры будут созданы, приложение сможет отправлять файлы, которые будут использоваться задачами.

> [AZURE.TIP] [How to use Azure Blob storage from Python]Статья "Приступая к работе с хранилищем BLOB-объектов Azure с помощью Python" содержит хороший обзор работы с контейнерами службы хранилища Azure и большими двоичными объектами. Вы должны ознакомиться с этой статьей, прежде чем приступать к работе с пакетной службой.

## Шаг 2. Отправка сценария задач и файлов данных

![Отправка файлов приложения и входных данных в контейнеры][2] <br/>

Во время отправки файлов скрипт *python\_tutorial\_client.py* сначала определяет коллекции путей к файлам **application** и **input** на локальном компьютере. Затем оно отправляет эти файлы в контейнеры, созданные в рамках предыдущего шага.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

Списковое выражение вызывает функцию `upload_file_to_container` для каждого файла в коллекциях, а затем заполняются две коллекции [ResourceFile][py_resource_file]. Функция `upload_file_to_container` показана ниже.

```
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### ResourceFiles

Объект [ResourceFile][py_resource_file] передает задачи в пакетную службу с URL-адресом файла в службе хранилища Azure, который будет скачан на вычислительный узел перед выполнением этой задачи. Свойство [ResourceFile][py_resource_file].**blob\_source** указывает полный URL-адрес файла, по которому его можно найти в службе хранилища Azure. URL-адрес может также включать подписанный URL-адрес (SAS), который обеспечивает безопасный доступ к файлу. Большинство типов задач в пакетной службе, в том числе перечисленные ниже, содержат свойство *ResourceFiles*.

- [CloudTask][py_task]
- [StartTask][py_starttask]
- [JobPreparationTask][py_jobpreptask]
- [JobReleaseTask][py_jobreltask]

В этом примере не используются типы задач JobPreparationTask или JobReleaseTask, но о них можно узнать больше из статьи [Выполнение задач подготовки и завершения заданий на вычислительных узлах пакетной службы Azure](batch-job-prep-release.md).

### Подписанный URL-адрес (SAS)

Подписанные URL-адреса — это строки, которые предоставляют безопасный доступ к контейнерам и большим двоичным объектам в службе хранилища Azure. Скрипт *python\_tutorial\_client.py* использует подписанные URL-адреса (SAS) как контейнеров, так и больших двоичных объектов. Он демонстрирует, как получить эти строки подписанных URL-адресов из службы хранилища.

- **Подписанные URL-адреса больших двоичных объектов**. Задача StartTask пула использует подписанные URL-адреса больших двоичных объектов во время скачивания скрипта заданий и файлов входных данных из службы хранилища (см. [шаг 3](#step-3-create-batch-pool) ниже). Функция `upload_file_to_container` в *python\_tutorial\_client.py* содержит код, который получает подписанный URL-адрес каждого большого двоичного объекта. Для этого в модуле службы хранилища вызывается [BlockBlobService.make\_blob\_url][py_make_blob_url].

- **Подписанный URL-адрес контейнера**. Когда каждая задача завершает работу на вычислительном узле, она отправляет свой выходной файл в контейнер *output* в службе хранилища Azure. Для этого *python\_tutorial\_task.py* использует подписанный URL-адрес контейнера, который предоставляет доступ на запись в контейнер. Функция `get_container_sas_token` в *python\_tutorial\_client.py* получает подписанный URL-адрес контейнера, который затем передается в задачи в качестве аргумента командной строки. На шаге 5 [Добавление задач в задание](#step-5-add-tasks-to-job) описывается использование SAS контейнера.

> [AZURE.TIP] Дополнительные сведения о предоставлении безопасного доступа к данным в своей учетной записи службы хранилища см. в серии из двух статей о подписанных URL-адресах: [Часть 1: общие сведения о модели SAS](../storage/storage-dotnet-shared-access-signature-part-1.md) и [Часть 2: создание и использование подписанного URL-адреса в службе BLOB-объектов](../storage/storage-dotnet-shared-access-signature-part-2.md).

## Шаг 3. Создание пула пакетной службы

![Создание пула пакетной службы][3] <br/>

**Пул** пакетной службы — это коллекция вычислительных узлов (виртуальных машин), на которых пакетная служба выполняет задачи задания.

После отправки скрипта задач и файлов данных в учетную запись службы хранилища *python\_tutorial\_client.py* начинает взаимодействие с пакетной службой, используя ее модуль Python. Для этого создается [BatchServiceClient][py_batchserviceclient].

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Затем в учетной записи пакетной службы нужно создать пул вычислительных узлов, вызвав `create_pool`.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Во время создания пула необходимо определить [PoolAddParameter][py_pooladdparam], который указывает несколько свойств пула.

- **Идентификатор** пула (*id*, обязательное).<p/>Как и у большинства сущностей в пакетной службе, у нового пула должен быть идентификатор, уникальный в учетной записи этой службы. Код ссылается на этот пул, используя его идентификатор, по которому пул также можно найти на [портале][azure_portal] Azure.

- **Количество вычислительных узлов** (*target\_dedicated*, обязательное).<p/>Указывает, сколько виртуальных машин следует развернуть в пуле. Стоит отметить, что для всех учетных записей пакетной службы установлена **квота** по умолчанию, которая ограничивает количество **ядер** (и, следовательно, вычислительных узлов) в учетной записи. Дополнительные сведения о квотах по умолчанию и инструкцию по [увеличению квоты](batch-quota-limit.md#increase-a-quota) (например, максимального количества ядер в учетной записи пакетной службы) см. в статье [Квоты и ограничения пакетной службы Azure](batch-quota-limit.md). Если возник вопрос о том, почему пул не использует больше определенного количества узлов, причиной может быть квота на ядра.

- **Операционная система** для узлов (*virtual\_machine\_configuration* **или** *cloud\_service\_configuration*, обязательно).<p/>В *python\_tutorial\_client.py* мы создадим пул узлов Linux с использованием [VirtualMachineConfiguration][py_vm_config]. Функция `select_latest_verified_vm_image_with_node_agent_sku` в `common.helpers` упрощает работу с образами из [магазина виртуальных машин Azure][vm_marketplace]. Дополнительные сведения об использовании образов из магазина см. в статье [Подготовка вычислительных узлов Linux в пулах пакетной службы Azure](batch-linux-nodes.md).

- **Размер вычислительных узлов**(*vm\_size*, обязательное).<p/>Так как мы указываем узлы Linux для [VirtualMachineConfiguration][py_vm_config], необходимо указать их размер (в этом примере — `STANDARD_A1`), как описано в статье [Размеры виртуальных машин в Azure](../virtual-machines/virtual-machines-linux-sizes.md). Дополнительные сведения см. в статье [Подготовка вычислительных узлов Linux в пулах пакетной службы Azure](batch-linux-nodes.md).

- **Задача запуска** (*start\_task*, необязательное).<p/>Вместе со свойствами физических узлов выше можно также указать [StartTask][py_starttask] для пула (необязательно). Задача StartTask выполняется на каждом узле по мере его присоединения к пулу, а также при каждом перезапуске узла. Она особенно полезна для подготовки вычислительных узлов к выполнению таких операций, как установка приложений, которые будут использовать задачи.<p/>В этом примере приложения задача StartTask копирует файлы, которые она скачивает из *рабочего каталога* StartTask службы хранилища (эти файлы указаны в свойстве **resource\_files** задачи StartTask) в *общий* каталог, к которому есть доступ у всех задач, выполняемых на узле. По сути, это обеспечивает копирование `python_tutorial_task.py` в общий каталог на каждом узле, когда узел присоединяется к пулу, чтобы к нему был доступ у всех задач, запускаемых на узле.

Вы могли заметить вызов вспомогательной функции `wrap_commands_in_shell`. Она использует коллекцию отдельных команд и создает одну соответствующую командную строку для свойства командной строки задачи.

Обратите также внимание, что во фрагменте кода выше в свойстве **command\_line** задачи StartTask используются две переменные среды: `AZ_BATCH_TASK_WORKING_DIR` и `AZ_BATCH_NODE_SHARED_DIR`. На каждом вычислительном узле в пуле пакетной службы автоматически настраивается несколько переменных среды, характерных для пакетной службы. Любой процесс, выполняемый задачей, имеет доступ к этим переменным среды.

> [AZURE.TIP] Дополнительные сведения о переменных среды, доступных на вычислительных узлах в пуле пакетной службы, а также сведения о рабочих каталогах задач см. в разделах **Параметры среды для задач** и **Файлы и каталоги** статьи [с обзором функций пакетной службы Azure](batch-api-basics.md).

## Шаг 4. Создание задания пакетной службы

![Создание задания пакетной службы][4]<br/>

**Задание** пакетной службы — это коллекция задач, связанных с пулом вычислительных узлов. Задачи задания выполняются на вычислительных узлах связанного пула.

Вы можете использовать задание не только для упорядочивания и отслеживания задач в соответствующих рабочих нагрузках, но и для установления определенных ограничений, включая максимальное время выполнения задания (а следовательно, и его задач). При этом назначается приоритет задания относительно других заданий в учетной записи пакетной службы. Но в этом примере задание связано только с пулом, который был создан на шаге 3. Дополнительные свойства не настроены.

Все задания пакетной службы связаны с конкретным пулом. Эта связь указывает, в каких узлах выполняются задачи задания. Вам необходимо указать пул, используя свойство [PoolInformation][py_poolinfo], как показано в следующем фрагменте кода.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

В созданное задание добавляются задачи для выполнения работы.

## Шаг 5. Добавление задач в задание

![Добавление задач в задание][5]<br/> *(1) Задачи добавляются в задание, (2) планируется запуск задач на узлах, (3) задачи скачивают файлы данных для обработки*

**Задачи** пакетной службы представляют собой отдельные рабочие единицы, выполняемые на вычислительных узлах. У задачи есть командная строка, она запускает сценарии или исполняемые файлы, указанные в этой строке.

Для фактического выполнения работы необходимо добавить задачи в задание. Каждая задача [CloudTask][py_task] (как и задача StartTask пула) настраивается с помощью свойства командной строки и объекта [ResourceFiles][py_resource_file], который она скачивает на узел до автоматического выполнения ее командной строки. В этом примере каждая задача обрабатывает только один файл. Поэтому его коллекция ResourceFiles содержит один элемент.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [AZURE.IMPORTANT] При получении доступа к переменным среды, таким как `$AZ_BATCH_NODE_SHARED_DIR`, или выполнении приложения, которое не находится в `PATH` на узле, командные строки задачи должны явным образом вызвать оболочку, например с помощью `/bin/sh -c MyTaskApplication $MY_ENV_VAR`. Это требование не является обязательным, если задачи выполняют приложение в `PATH` и не ссылаются на переменные среды.

В пределах цикла `for` в приведенном выше фрагменте кода видно, что командная строка задачи построена с использованием пяти аргументов командной строки, передаваемых в *python\_tutorial\_task.py*:

1. **filepath** — это локальный путь к файлу, по которому его можно найти на узле. Когда объект ResourceFile создавался в `upload_file_to_container` на шаге 2 выше, для этого свойства использовалось имя файла (в конструкторе ResourceFile в параметре `file_path`). Это значит, что файл находится в том же каталоге на узле, что и *python\_tutorial\_task.py*.

2. **numwords** указывает *N* наиболее часто используемых слов, которые должны быть записаны в выходной файл.

3. **storageaccount** — имя учетной записи службы хранилища, которой принадлежит контейнер, куда следует передать выходные данные задач.

4. **storagecontainer** — имя контейнера службы хранилища, в который следует передать выходные файлы.

5. **sastoken** — это подписанный URL-адрес (SAS), который предоставляет доступ на запись в контейнер **output** в службе хранилища Azure. Скрипт *python\_tutorial\_task.py* использует подписанный URL-адрес при создании ссылки на BlockBlobService. Это обеспечивает доступ на запись в контейнер без ключа доступа к учетной записи хранения.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## Шаг 6. Мониторинг задач

![Мониторинг задач][6]<br/> *Скрипт (1) выполняет мониторинг задач и состояние их выполнения, а задачи (2) отправляют данные результатов в службу хранилища Azure*

Добавленные в задание задачи автоматически выстраиваются в очередь и планируются для выполнения на вычислительных узлах пула, связанного с заданием. Пакетная служба обрабатывает постановку задач в очередь, их планирование извлечение и другие задачи администрирования с учетом указанных вами параметров.

Есть несколько подходов к отслеживанию выполнения задач. Функция `wait_for_tasks_to_complete` в *python\_tutorial\_client.py* является простым примером мониторинга определенного состояния задач, в этом случае — [выполненного][py_taskstate] состояния.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## Шаг 7. Загрузка выходных данных задачи

![Загрузка выходных данных задачи из службы хранилища][7]<br/>

Теперь, когда задание выполнено, можно загрузить выходные данные задач из службы хранилища Azure. Для этого нужно вызвать `download_blobs_from_container` в *python\_tutorial\_client.py*.

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [AZURE.NOTE] При вызове `download_blobs_from_container` в *python\_tutorial\_client.py* указывается, что файлы необходимо скачивать в корневой каталог. Вы можете изменить это расположение выходных данных.

## Шаг 8. Удаление контейнеров

Так как вы платите за данные, которые находятся в службе хранилища Azure, рекомендуется всегда удалять все большие двоичные объекты, которые больше не нужны для выполнения заданий пакетной службы. В *python\_tutorial\_client.py* это можно сделать, вызвав [BlockBlobService.delete\_container][py_delete_container] трижды.

```
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## Шаг 9. Удаление задания и пула

На последнем шаге появляется запрос на удаление пула и задания, созданных скриптом *python\_tutorial\_client.py*. Хотя вы и не платите за задания и задачи, *взимается* плата за используемые вычислительные узлы. Поэтому рекомендуется выделять узлы только при необходимости. Удаление неиспользуемых пулов может быть частью процесса обслуживания.

Свойства [JobOperations][py_job] и [PoolOperations][py_pool] BatchServiceClient предусматривают соответствующие методы удаления, которые вызываются, если подтвердить удаление.

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [AZURE.IMPORTANT] Помните, что вы платите за использование вычислительных ресурсов, поэтому удаление неиспользуемых пулов позволит сократить затраты. Также не забывайте, что при удалении пула удаляются все вычислительные узлы этого пула, после чего все данные на узлах уже нельзя будет восстановить.

## Запуск примера сценария

При запуске скрипта *python\_tutorial\_client.py* из [примера кода][github_article_samples] в руководстве консоль будет выглядеть следующим образом. Во время создания и запуска вычислительных узлов пула, а также выполнения команд в рамках его задачи запуска выполнение приостанавливается на `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...`. Используйте [портал Azure][azure_portal] для мониторинга пула, вычислительных узлов, заданий и задач во время и после выполнения. Используйте [портал Azure][azure_portal] или [обозреватель хранилищ Microsoft Azure][storage_explorer], чтобы просматривать ресурсы службы хранилища (контейнеры и большие двоичные объекты), созданные приложением.

>[AZURE.TIP] Выполните скрипт *python\_tutorial\_client.py* в каталоге `azure-batch-samples/Python/Batch/article_samples`. Он использует относительный путь для импорта модуля `common.helpers`, поэтому, если не выполнить скрипт в этом каталоге, может возникнуть ошибка `ImportError: No module named 'common'`.

Обычное время выполнения — **примерно 5–7 минут**, если для примера задана конфигурация по умолчанию.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## Дальнейшие действия

Вы можете внести изменения в *python\_tutorial\_client.py* и *python\_tutorial\_task.py*, чтобы поэкспериментировать с различными вычислительными сценариями. Например, попробуйте добавить задержку выполнения в *python\_tutorial\_task.py*, чтобы сымитировать длительно выполняемые задачи и следить за ними на портале. Попробуйте добавить дополнительные задачи или изменить количество вычислительных узлов. Добавьте логику для проверки и разрешите использование существующего пула, чтобы сократить время выполнения.

Теперь, когда вы знакомы с основным рабочим процессом решения пакетной службы, пришло время подробно изучить дополнительные возможности пакетной службы.

- Если вы недавно используете пакетную службу, рекомендуем прочитать статью [с обзором функций пакетной службы Azure](batch-api-basics.md).
- Ознакомьтесь с другими статьями на тему разработки в пакетной службе. См. раздел **Подробные сведения о разработке** на схеме обучения [Пакетная служба][batch_learning_path].
- Ознакомьтесь с другими способами обработки рабочей нагрузки "N часто употребляемых слов" с помощью пакетной службы. См. пример [TopNWords][github_topnwords].

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage

[pypi_install]: http://python-packaging-user-guide.readthedocs.io/en/latest/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Создание контейнеров в службе хранилища Azure"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Отправка файлов приложения и входных данных в контейнеры"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Создание пула пакетной службы"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Создание задания пакетной службы"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Добавление задач в задание"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Мониторинг задач"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Загрузка выходных данных задачи из службы хранилища"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Рабочий процесс решения пакетной службы (полная схема)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "Учетные данные пакетной службы на портале"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "Учетные данные службы хранилища на портале"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Рабочий процесс решения пакетной службы (сокращенная схема)"

<!---HONumber=AcomDC_0928_2016-->