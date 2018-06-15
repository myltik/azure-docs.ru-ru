---
title: Управление файловыми ресурсами Azure с помощью Azure CLI
description: Сведения об управлении файловыми ресурсами Azure с помощью Azure CLI.
services: storage
documentationcenter: na
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 66373d5a1ddd210d73fa964322862698f208a2f6
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737954"
---
# <a name="manage-azure-file-shares-using-azure-cli"></a>Управление файловыми ресурсами Azure с помощью Azure CLI
[Файлы Azure](storage-files-introduction.md) — это простая в использовании облачная файловая система от корпорации Майкрософт. Файловые ресурсы Azure можно подключить в Windows, Linux и macOS. В статье рассматриваются основы работы с файловыми ресурсами Azure с помощью Azure CLI. Вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * создать группы ресурсов и учетную запись хранения;
> * создать файловый ресурс Azure; 
> * создать каталог;
> * отправить файл; 
> * скачать файл;
> * создать моментальный снимок общего ресурса и использовать его.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Если вы хотите установить и использовать Azure CLI локально, для выполнения всех действий из данной статьи требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать, как версия Azure CLI установлена, выполните команду **az --version**. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). 

По умолчанию команды Azure CLI возвращают ответ в формате JSON. Это стандартный способ отправки и получения сообщений из REST API. Для облегчения работы с ответами в формате JSON в некоторых примерах в этой статье с командами Azure CLI используется параметр *запроса*. Этот параметр использует [язык запросов JMESPath](http://jmespath.org/) для синтаксического анализа JSON. Дополнительные сведения о том, как обрабатывать результаты выполнения команд Azure CLI с применением языка запросов JMESPath, см. в [руководстве по JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure и управление ими. Если у вас еще нет группы ресурсов Azure, ее можно создать с помощью команды [az group create](/cli/azure/group#create). 

В следующем примере создается группа ресурсов под названием *myResourceGroup* в расположении *Восточная часть США*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Создание учетной записи хранения
Учетная запись хранения представляет собой общий пул носителей, который можно использовать для развертывания файловых ресурсов Azure или других ресурсов хранения, например больших двоичных объектов или очередей. Учетная запись хранения может содержать неограниченное количество файловых ресурсов. В общем ресурсе может храниться любое число файлов, насколько это позволяет емкость учетной записи хранения.

В следующем примере с помощью команды [az storage account create](/cli/azure/storage/account#create) создается учетная запись хранения с именем *mystorageaccount\<случайное число\>*, а затем имя этой учетной записи помещается в переменную `$STORAGEACCT`. Имена учетных записей хранения должны быть уникальными. Используйте `$RANDOM`, чтобы добавить уникальный номер в конец имени учетной записи хранения. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Получение ключа учетной записи хранения
Ключи учетной записи хранения определяют доступ к хранящихся в ней ресурсам. Эти ключи автоматически создаются при создании учетной записи хранения. Ключи учетной записи хранения можно получить с помощью команды [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>создать файловый ресурс Azure;
Теперь можно создать первый файловый ресурс Azure. Используйте для этого команду [az storage share create](/cli/azure/storage/share#create). В этом примере создается файловый ресурс Azure с именем *myshare*. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!IMPORTANT]  
> Имена общих ресурсов должны содержать только строчные буквы, цифры и отдельные дефисы, и они не могут начинаться с дефиса. Дополнительные сведения о присвоении имен общим папкам и файлам см. в статье [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Именование общих ресурсов, каталогов, файлов и метаданных и ссылка на них).

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Использование содержимого, расположенного в файловом ресурсе Azure
Создав файловый ресурс Azure, его можно подключить с помощью SMB к [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) или [macOS](storage-how-to-use-files-mac.md). Кроме того, с файловым ресурсом Azure можно работать с помощью Azure CLI. Преимущество использования Azure CLI вместо подключения файлового ресурса по протоколу SMB заключается в том, что все запросы, созданные с помощью Azure CLI, выполняются через файловый REST API. Файловый REST API можно использовать для создания, изменения и удаления файлов и каталогов в файловом ресурсе из следующих расположений:

- Bash Azure Cloud Shell (которая не может подключать файловые ресурсы через SMB);
- клиентов, которые не могут подключать общие ресурсы SMB, такие как локальные клиенты, в которых не разблокирован порт 445;
- сценариев без сервера, например из службы [Функции Azure](../../azure-functions/functions-overview.md).

### <a name="create-a-directory"></a>создать каталог;
Чтобы создать каталог с именем *myDirectory* в корне файлового ресурса Azure, выполните команду [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create).

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Отправка файла
Чтобы продемонстрировать передачу файла с помощью команды [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload), сначала создайте файл для отправки на временном диске Cloud Shell. В следующем примере создается, а затем отправляется файл.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

При работе в Azure CLI локально следует заменить `~/clouddrive` путем, который имеется на вашем компьютере.

После отправки файла можно использовать команду [`az storage file list`](/cli/azure/storage/file#az_storage_file_list), чтобы убедиться, что файл отправлен в файловый ресурс Azure.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>скачать файл;
С помощью команды [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) можно загрузить копию файла, отправленного на временный диск Cloud Shell.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Копирование файлов
Одна из распространенных задач заключается в копировании файлов из одного файлового ресурса в другой или из контейнера хранилища BLOB-объектов Azure и в него. Для демонстрации этой функциональности создайте новый файловый ресурс. Скопируйте файл, отправленный в только что созданный общий ресурс, с помощью команды [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Если сейчас вывести список файлов в новом файловом ресурсе, вы увидите скопированный файл.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Хотя команда `az storage file copy start` удобна для перемещения файлов между файловыми ресурсами Azure и контейнерами хранилища BLOB-объектов Azure, для массовых перемещений рекомендуется использовать AzCopy. (Массовые с точки зрения количества или размера перемещаемых файлов.) См. дополнительные сведения о [AzCopy для Linux](../common/storage-use-azcopy-linux.md) and [AzCopy для Windows](../common/storage-use-azcopy.md). AzCopy должна быть установлена локально. AzCopy недоступна в Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Создание и изменение моментальных снимков общего ресурса
Еще одна полезная задача, которую можно выполнить с файловым ресурсом Azure, — создать его моментальные снимки. Моментальный снимок сохраняет состояние общего файлового ресурса Azure на момент определенной точки во времени. Моментальные снимки файловых ресурсов аналогичны тем, которые создаются с помощью уже знакомых вам технологий, например:
- [диспетчер логических томов (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) для систем Linux;
- [файловая система Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) для macOS;
- [служба теневого копирования томов (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) для файловых систем Windows, таких как NTFS и ReFS.

Вы можете создать моментальный снимок общих ресурсов с помощью команды [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Просмотр содержимого моментального снимка общих ресурсов
Чтобы просмотреть содержимое моментального снимка общего ресурса, передайте его метку времени команде `az storage file list` в атрибуте переменной `$SNAPSHOT`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Вывод списка моментальных снимков общих ресурсов
Список имеющихся моментальных снимков общего ресурса можно получить с помощью следующей команды.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Восстановление из моментального снимка общего ресурса
Файл можно восстановить с помощью команды `az storage file copy start`, использовавшейся ранее. Сначала удалите отправленный файл SampleUpload.txt, чтобы его можно было восстановить из моментального снимка.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Удаление моментального снимка общего ресурса
Моментальный снимок общего ресурса можно удалить с помощью команды [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete). Воспользуйтесь переменной, содержащей ссылку `$SNAPSHOT` на параметр `--snapshot`.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Закончив работу, удалите группу ресурсов и все связанные с ней ресурсы с помощью команды [`az group delete`](/cli/azure/group#delete). 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Кроме того, ресурсы можно удалить по отдельности.
- Чтобы удалить файловые ресурсы Azure, созданные в ходе выполнения примеров из данной статьи:

    ```azurecli-interactive
    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare" \
        --delete-snapshots include

    az storage share delete \
        --account-name $STORAGEACCT \
        --account-key $STORAGEKEY \
        --name "myshare2" \
        --delete-snapshots include
    ```

- Чтобы удалить саму учетную запись хранения. (При этом опосредованно удаляются созданные файловые ресурсы Azure, а также другие созданные ресурсы хранения, например контейнер службы хранилища BLOB-объектов Azure.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Дополнительная информация
- [Управление файловыми ресурсами Azure с помощью портала Azure](storage-how-to-use-files-portal.md)
- [Управление файловыми ресурсами Azure с помощью Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Управление файловыми ресурсами Azure с помощью Обозревателя службы хранилища Azure](storage-how-to-use-files-storage-explorer.md)
- [Планирование развертывания службы файлов Azure](storage-files-planning.md)