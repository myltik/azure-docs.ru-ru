---
title: "Использование Azure CLI 2.0 со службой хранилища Azure | Документация Майкрософт"
description: "Узнайте, как использовать интерфейс командной строки Azure (Azure CLI) версии 2.0 для создания учетных записей хранения и управления ими, а также для работы с большими двоичными объектами и файлами Azure в службе хранилища Azure. Azure CLI 2.0 — это кроссплатформенное средство, написанное на языке Python."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 02/18/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: be44ca9d14d6dbb7a50d5c42c163bc66531bb90f
ms.lasthandoff: 03/30/2017


---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Использование Azure CLI 2.0 со службой хранилища Azure

Кроссплатформенный Azure CLI 2.0 с открытым кодом представляет собой набор команд для работы с платформой Azure. Он предоставляет практически те же функции, что и [портал Azure](https://portal.azure.com), а также различные возможности доступа к данным.

В этом руководстве рассказывается о том, как использовать [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) для выполнения нескольких задач при работе с ресурсами в учетной записи хранилища Azure. Рекомендуем скачать и установить (или обновить до последней версии) CLI 2.0, прежде чем продолжать работу с этим руководством.

В примерах, приведенных в этом руководстве, используется оболочка Bash на Ubuntu. Другие платформы должны работать аналогично. 

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Предварительные требования
В этом руководстве предполагается, что вам знакомы основные понятия службы хранилища Azure. Также предполагается, что вы можете выполнить требования для создания учетной записи и службы хранилища Azure. Эти требования перечислены ниже.

### <a name="accounts"></a>Учетные записи
* **Учетная запись Azure.** Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/).
* **Учетная запись хранения**. См. раздел [Создание учетной записи хранения](../storage/storage-create-storage-account.md#create-a-storage-account) в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Установка Azure CLI 2.0

Скачайте и установите Azure CLI 2.0 согласно инструкциям в статье [Install Azure CLI 2.0](/cli/azure/install-az-cli2) (Установка Azure CLI 2.0).

> [!TIP]
> Если у вас возникают проблемы с установкой, см. сведения об устранении неполадок в [этом разделе](/cli/azure/install-az-cli2#installation-troubleshooting) и в руководстве по [устранению неполадок при установке](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) на GitHub.
>

## <a name="working-with-the-cli"></a>Работа с интерфейсом командной строки

После установки Azure CLI для доступа к соответствующим функциям вы можете использовать команду `az` в интерфейсе командной строки (Bash, терминал, командная строка). Введите команду `az`, после чего должен отобразиться примерно такой результат:

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account   : Commands to manage subscriptions.
    acr       : Commands to manage Azure container registries.
    acs       : Commands to manage Azure container services.
    ad        : Synchronize on-premises directories and manage Azure Active Directory (AAD)
                resources.
    appservice: Commands to manage your Azure web apps and App Service plans.
    cloud     : Manage the Azure clouds registered.
    component : Commands to manage and update Azure CLI 2.0 components.
    configure : Configure Azure CLI 2.0 or view your configuration. The command is
                interactive, so just type `az configure` and respond to the prompts.
    container : Set up automated builds and deployments for multi-container Docker applications.
    disk      : Commands to manage 'Managed Disks'.
    feature   : Commands to manage resource provider features, such as previews.
    feedback  : Loving or hating the CLI?  Let us know!
    group     : Commands to manage resource groups.
    image     : Commands to manage custom virtual machine images based on managed disks/snapshots.
    lock
    login     : Log in to access Azure subscriptions.
    logout    : Log out to remove access to Azure subscriptions.
    network   : Manages Network resources.
    policy    : Commands to manage resource policies.
    provider  : Manage resource providers.
    resource  : Generic commands to manage Azure resources.
    role      : Use role assignments to manage access to your Azure resources.
    snapshot  : Commands to manage snapshots.
    storage   : Durable, highly available, and massively scalable cloud storage.
    tag       : Manage resource tags.
    vm        : Provision Linux and Windows virtual machines in minutes.
    vmss      : Create highly available, auto-scalable Linux or Windows virtual machines.
```

В интерфейсе командной строки выполните команду `az storage -h`, чтобы вывести список команд группы `storage` и ее подгруппы. Описания подгрупп дают общие сведения о функциональных возможностях, предоставляемых Azure CLI для работы с ресурсами хранилища.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Orgin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Effectively scale apps according to traffic using queues.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Подключение интерфейса командной строки к своей подписке Azure

Для работы с ресурсами в подписке Azure необходимо сначала войти в учетную запись Azure с помощью `az login`. Для этого существует несколько способов.

* **Интерактивный вход**: `az login`.
* **Вход с использованием имени пользователя SSH и пароля**: `az login -u johndoe@contoso.com -p VerySecret`.
  * Этот способ не работает с учетными записями Майкрософт или с учетными записями, которые используют многофакторную идентификацию.
* **Вход с использованием субъекта-службы**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`.

## <a name="azure-cli-20-sample-script"></a>Пример скрипта Azure CLI 2.0

Далее мы создадим небольшой сценарий оболочки, который запускает несколько базовых команд Azure CLI 2.0 для взаимодействия с ресурсами службы хранилища Azure. Сначала скрипт создает новый контейнер в учетной записи хранения, а затем отправляет существующий файл (в виде большого двоичного объекта) в этот контейнер. После этого он выводит список всех больших двоичных объектов в контейнере и скачивает файл в заданное вами место на локальном компьютере.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

echo "Done"
```

**Настройка и запуск скрипта**

1. Откройте текстовый редактор по выбору, а затем скопируйте и вставьте в него предыдущий скрипт.

2. После этого обновите переменные скрипта в соответствии с параметрами конфигурации. Замените следующие значения соответствующим образом.

   * **\<storage_account_name\>**: имя вашей учетной записи хранения.
   * **\<storage_account_key\>**: первичный или вторичный ключ доступа к вашей учетной записи хранения.
   * **\<container_name\>**: имя нового контейнера, который необходимо создать, например azure-cli-sample-container.
   * **\<blob_name\>**: имя целевого большого двоичного объекта в контейнере.
   * **\<file_to_upload\>**: путь к небольшому файлу на локальном компьютере, например ~/images/HelloWorld.png.
   * **\<destination_file\>**: путь к конечному файлу, например ~/downloadedImage.png.

3. После обновления необходимых переменных сохраните скрипт и выйдите из редактора. В описанных ниже действиях предполагается, что скрипту присвоено имя **my_storage_sample.sh**.

4. При необходимости пометьте скрипт как исполняемый файл: `chmod +x my_storage_sample.sh`

5. Выполните скрипт, например в Bash: `./my_storage_sample.sh`

Вы увидите результат, аналогичный приведенному ниже. Указанный в скрипте **\<destination_file\>** появится на локальном компьютере.

```
Creating the container...
Success
---------
True
Uploading the file...                                           Percent complete: %100.0
Listing the blobs...
Name           Blob Type      Length  Content Type              Last Modified
-------------  -----------  --------  ------------------------  -------------------------
test_blob.txt  BlockBlob         771  application/octet-stream  2016-12-21T15:35:30+00:00
Downloading the file...
Name
-------------
test_blob.txt
Done
```

> [!TIP]
> Предыдущие выходные данные отображаются в **табличном** формате. Можно указать, какой формат выходных данных следует использовать. Для этого задайте аргумент `--output` в командах интерфейса командной строки или задайте его глобально с помощью `az configure`.
>

## <a name="manage-storage-accounts"></a>Управление учетными записями хранения

### <a name="create-a-new-storage-account"></a>Создание новой учетной записи хранения
Для использования службы хранилища Azure вам потребуется учетная запись хранения. После настройки компьютера для [подключения к подписке](#connect-to-your-azure-subscription) можно создать новую учетную запись хранения Azure.

```azurecli
az storage account create -l <location> -n <account_name> -g <resource_group> --sku <account_sku>
```

* `-l` — расположение (обязательный параметр). Например, "западная часть США".
* `-n` — имя учетной записи хранения (обязательный параметр). Имя должно быть не меньше 3 и не больше 24 символов и содержать только буквенно-цифровые символы.
* `-g` — имя группы ресурсов (обязательный параметр).
* `--sku` — номер SKU учетной записи хранения (обязательный параметр). Допустимые значения:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Установка учетной записи хранения Azure по умолчанию в переменные среды
В подписке может быть несколько учетных записей хранения Azure. Чтобы выбрать одну из них для использования для всех последующих команд службы хранилища, задайте эти переменные среды:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Установить учетную запись хранения по умолчанию можно также с помощью строки подключения. Сначала получите строку подключения с помощью команды `show-connection-string`:

```azurecli
az storage account show-connection-string -n <account_name> -g <resource_group>
```

Затем скопируйте полученную строку подключения и задайте переменную среды `AZURE_STORAGE_CONNECTION_STRING` (строки подключения может понадобиться заключить в кавычки):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

> [!NOTE]
> Во всех примерах в следующих разделах этой статьи предполагается, что вы задали переменные среды `AZURE_STORAGE_ACCOUNT` и `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Создание больших двоичных объектов (BLOB-объектов) и управление ими
Хранилище BLOB-объектов Azure — это служба хранения большого количества неструктурированных данных, таких как текстовые или бинарные файлы, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. В этом разделе предполагается, что вы уже знакомы с понятиями службы хранилища BLOB-объектов Azure. Дополнительные сведения см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](/rest/api/storageservices/fileservices/blob-service-concepts).

### <a name="create-a-container"></a>Создание контейнера
Каждый BLOB-объект в хранилище Azure должен находиться в контейнере. Вы можете создать контейнер с помощью команды `az storage container create`:

```azurecli
az storage container create -n <container_name>
```

Для нового контейнера можно задать один из трех уровней доступа на чтение с помощью необязательного аргумента `--public-access`.

* `off`: данные контейнера являются личными данными владельца учетной записи (значение по умолчанию).
* `blob`: общий доступ на чтение для больших двоичных объектов.
* `container`: общий доступ на чтение и создание списков для всего контейнера.

Дополнительные сведения см. в статье [Управление анонимным доступом на чтение к контейнерам и большим двоичным объектам](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Отправка большого двоичного объекта в контейнер
Хранилище BLOB-объектов Azure поддерживает блочные, добавочные и страничные BLOB-объекты. Отправьте большие двоичные объекты в контейнер с помощью команды `blob upload`:

```azurecli
az storage blob upload -f <local_file_path> -c <container_name> -n <blob_name>
```

 По умолчанию команда `blob upload` отправляет файлы с расширением *.vhd в страничные или блочные BLOB-объекты. Чтобы указать другой тип при отправке большого двоичного объекта, можно использовать аргумент `--type`. Допустимые значения: `append`, `block`, и `page`.

 Дополнительные сведения о различных больших двоичных объектах см. в статье [Understanding Block Blobs, Append Blobs, and Page Blobs](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) (Основные сведения о блочных, добавочных и страничных BLOB-объектах).

### <a name="download-blobs-from-a-container"></a>Загрузка BLOB-объектов из контейнера
Следующий пример демонстрирует скачивание больших двоичных объектов из контейнера:

```azurecli
az storage blob download -c mycontainer -n myblob.png -f ~/mydownloadedblob.png
```

### <a name="copy-blobs"></a>Копирование BLOB-объектов
Можно асинхронно копировать BLOB-объекты между учетными записями хранения и областями или внутри них.

Следующий пример демонстрирует копирование BLOB-объектов из одной учетной записи хранения в другую. Сначала создадим контейнер в другой учетной записи с общим анонимным доступом к большим двоичным объектам. Далее мы отправим файл в контейнер и, наконец, скопируем большой двоичный объект из этого контейнера в контейнер **mycontainer** текущей учетной записи.

```azurecli
az storage container create -n mycontainer2 --account-name <accountName2> --account-key <accountKey2> --public-access blob

az storage blob upload -f ~/Images/HelloWorld.png -c mycontainer2 -n myBlockBlob2 --account-name <accountName2> --account-key <accountKey2>

az storage blob copy start -u https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2 -b myBlobBlob -c mycontainer
```

URL-адрес исходного большого двоичного объекта (определенный с помощью `-u`) должен быть общедоступным или содержать маркер подписанного URL-адреса (SAS).

### <a name="delete-a-blob"></a>Удаление большого двоичного объекта
Чтобы удалить большой двоичный объект, используйте команду `blob delete`:

```azurecli
az storage blob delete -c <container_name> -n <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Создание общих папок и управление ими
Хранилище файлов Azure предоставляет общее хранилище для приложений с помощью протокола SMB. Виртуальные машины Microsoft Azure и облачные службы, а также локальные приложения, могут совместно использовать данные через монтированные ресурсы. Вы можете управлять общими папками и файловыми данными через интерфейс командной строки Azure. Дополнительные сведения о хранилище файлов Azure см. в статье [Приступая к работе с хранилищем файлов Azure в Windows](storage-dotnet-how-to-use-files.md) или [Использование хранилища файлов Azure с Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Создайте общую папку
Общая папка Azure представляет собой общую папку с файлами SMB в Azure. Все каталоги и файлы должны быть созданы в общей папке. Учетная запись может содержать любое количество совместно используемых ресурсов, а ресурс может содержать любое количество файлов, насколько это позволяет емкость учетной записи хранения. В следующем примере создается общая папка с именем **myshare**.

```azurecli
az storage share create -n myshare
```

### <a name="create-a-directory"></a>Создайте каталог
Каталог обеспечивает дополнительную иерархическую структуру для общей папки Azure. В следующем примере в общей папке создается каталог с именем **myDir** .

```azurecli
az storage directory create -n myDir -s myshare
```

Обратите внимание, что путь к каталогу может включать несколько уровней, *например***a/b**. Однако необходимо убедиться в наличии всех родительских каталогов. Например, для пути **a/b** вам потребуется сначала создать каталог **a**, а затем — каталог **b**.

### <a name="upload-a-local-file-to-a-share"></a>Отправка локального файла в общую папку
В следующем примере выполняется отправка файла из **~/temp/samplefile.txt** в корень общей папки **myshare**. Аргумент `--source` указывает существующий локальный файл для отправки.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Как и при создании каталога, вы можете указать путь к каталогу в общей папке, чтобы отправить файл в существующий каталог внутри общей папки:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Размер файла в общей папке может составлять до 1 ТБ.

### <a name="list-the-files-in-a-share"></a>Вывод списка файлов в общей папке
Вывести список файлов и каталогов в общей папке можно с помощью команды `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list -s myshare

# List the files in a directory within a share
az storage file list -s myshare/myDir

# List the files in a path within a share
az storage file list -s myshare -p myDir/mySubDir/MySubDir2
```

### <a name="copy-files"></a>Копирование файлов        
Вы можете скопировать файл в другой файл, файл в большой двоичный объект или большой двоичный объект в файл. Например, чтобы скопировать файл в каталог в другой общей папке, выполните следующее:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt        
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о работе с Azure CLI 2.0 см. в следующих ресурсах:

* [Get started with Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) (Приступая к работе с Azure CLI 2.0)
* [Azure CLI 2.0 command reference](/cli/azure) (Справочник по командам Azure CLI 2.0)
* [Статья об Azure CLI 2.0 на сайте GitHub](https://github.com/Azure/azure-cli)

