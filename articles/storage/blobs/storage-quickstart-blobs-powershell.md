---
title: Краткое руководство Azure. Создание большого двоичного объекта в хранилище объектов с помощью Azure PowerShell | Документация Майкрософт
description: В рамках этого краткого руководства вы используете Azure PowerShell в хранилище объектов (больших двоичных объектов). отправите большой двоичный объект в службу хранилища Azure, скачаете его и составите список больших двоичных объектов в контейнере с помощью Azure PowerShell.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: f028d37a98cecf14706773a2eb7cb601481435d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure PowerShell

Модуль PowerShell используется для создания ресурсов Azure и управления ими с помощью командной строки PowerShell или сценариев. В этом руководстве рассматривается использование PowerShell для передачи файлов между локальным диском и хранилищем BLOB-объектов Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в контейнер. Вы можете упорядочивать группы больших двоичных объектов аналогично организации файлов в папках на компьютере.

Задайте имя контейнера, а затем создайте контейнер с помощью командлета [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), задав разрешения на открытый доступ к файлам. В этом примере контейнеру присвоено имя *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. VHD-файлы, используемые для резервных виртуальных машин IaaS, являются страничными BLOB-объектами. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Большинство файлов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты. 

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер, а затем получите ссылку на блочный BLOB-объект в этом контейнере. При наличии ссылки на BLOB-объект вы можете отправлять в него данные с помощью командлета [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Эта операция создает BLOB-объект, если он еще не существует, или заменяет его, если он существует.

В следующих примерах показана отправка файлов Image001.jpg и Image002.png из папки D:\\_TestImages на локальном диске в созданный контейнер.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Прежде чем продолжить, отправьте нужное количество файлов.

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Получите список BLOB-объектов в контейнере с помощью командлета [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). В этом примере показаны только имена отправленных BLOB-объектов.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте большие двоичные объекты на локальный диск. Для каждого скачиваемого BLOB-объекта задайте имя и вызовите командлет [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent).

В этом примере показано скачивание BLOB-объектов на локальный диск D:\\_TestImages\Downloads. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Передача данных с помощью AzCopy

Программа [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) является дополнительным способом высокопроизводительной передачи данных с поддержкой сценариев для хранилища Azure. AzCopy можно использовать для передачи данных из хранилища BLOB-объектов, таблиц и файлов и обратно.

В качестве краткого примера здесь приводится команда AzCopy для отправки файла *myfile.txt* в контейнере *mystoragecontainer* контейнера в окне PowerShell.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите все созданные ресурсы. Самым простым способом сделать это является удаление группы ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление учетной записи хранения и самой группы ресурсов.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Справочник по командлетам PowerShell для службы хранилища Microsoft Azure
* [Командлеты PowerShell для службы хранилища](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Обозреватель службы хранилища Microsoft Azure
* [Обозреватель хранилищ Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.