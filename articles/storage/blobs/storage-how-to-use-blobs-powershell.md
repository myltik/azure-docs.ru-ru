---
title: "Выполнение операций в хранилище BLOB-объектов Azure (хранилище объектов) с помощью PowerShell | Документация Майкрософт"
description: "Руководство по выполнению операций в хранилище BLOB-объектов Azure (хранилище объектов) с помощью PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 7429de05ba1d583348b0b03b69135c2bbab0be45
ms.openlocfilehash: 565bcba848de1c518b25ff4c55a9a47aaa45bfb4
ms.contentlocale: ru-ru
ms.lasthandoff: 09/15/2017

---

# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Выполнение операций в хранилище BLOB-объектов Azure с помощью Azure PowerShell

Хранилище BLOB-объектов Azure — это служба хранения большого количества неструктурированных данных объектов, таких как текстовые или двоичные данные, к которым можно получить доступ практически из любой точки мира по протоколу HTTP или HTTPS. В этой статье описываются основные операции в хранилище BLOB-объектов Azure, например отправка, загрузка и удаление больших двоичных объектов. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание контейнера 
> * Отправка больших двоичных объектов
> * Перечисление BLOB-объектов в контейнере 
> * Скачивание больших двоичных объектов
> * Копирование BLOB-объектов
> * Удаление blob-объектов
> * Просмотр и указание свойств и метаданных большого двоичного объекта
> * Управление безопасностью с помощью подписанных URL-адресов

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 3.6. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в контейнер. Контейнеры аналогичны каталогам на компьютере. Они позволяют структурировать группы больших двоичных объектов в контейнере подобно упорядочиванию файлов в папках на компьютере. Учетная запись хранения может иметь любое количество контейнеров. Она ограничивается только объемом свободного пространства (до 500 ТБ). 

При создании контейнера можно задать уровень доступа, чтобы определить, кому предоставлен доступ к большим двоичным объектам в этом контейнере. Например, эти большие двоичные объекты могут быть закрыты (уровень доступа `Off`), что означает, что никто не может получить доступ к ним без подписанного URL-адреса или ключей доступа учетной записи хранения. Если при создании контейнера вы не укажете уровень доступа, по умолчанию будет использоваться закрытый уровень доступа.

Вы можете активировать общий доступ к изображениям в контейнере. Например, если вы хотите, чтобы хранимые изображения отображались на веб-сайте, вы можете предоставить к ним общий доступ. В этом случае вам необходимо задать уровень доступа к контейнеру `blob`, и любой пользователь, у которого есть URL-адрес, который указывает на большой двоичный объект в этом контейнере, может получить к нему доступ.

Чтобы создать контейнер, задайте имя контейнера, а затем создайте сам контейнер, задав уровень доступа blob (большой двоичный объект). Имена контейнеров должны начинаться с буквы или цифры и могут содержать только буквы, цифры и знак дефиса (-). Дополнительные сведения об именовании больших двоичных объектов и контейнеров см. в статье [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

Создайте контейнер с помощью командлета [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Установите для уровня доступа значение public. В этом примере контейнеру присвоено имя *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Отправка больших двоичных объектов в контейнер

Хранилище BLOB-объектов Azure поддерживает блочные, добавочные и страничные BLOB-объекты.  VHD-файлы, используемые для резервных виртуальных машин IaaS, являются страничными BLOB-объектами. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Большинство файлов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты. 

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер, а затем получите ссылку на блочный BLOB-объект в этом контейнере. При наличии ссылки на большой двоичный объект вы можете отправлять в него данные с помощью командлета [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Эта операция создает большой двоичный объект, если он еще не существует, или заменяет его, если он уже создан.

Ниже рассматривается, как передать большой двоичный объект в контейнер. Сначала следует задать переменные, которые указывают на каталог на локальном компьютере, где расположены файлы, а также задать переменную для имени файла для отправки. Это эффективно при повторном выполнении какой-либо операции. Отправьте несколько файлов, чтобы увидеть несколько записей при получении списка больших двоичных объектов в контейнере.

В следующих примерах показана отправка файлов Image001.jpg и Image002.png из папки D:\\_TestImages на локальном диске в только что созданный контейнер.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Отправьте следующий файл. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Прежде чем продолжить, отправьте нужное количество файлов.

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Получите список больших двоичных объектов в контейнере с помощью командлета [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) и выберите имя большого двоичного объекта для отображения.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Скачивание больших двоичных объектов

Скачайте большие двоичные объекты на локальный диск. Сначала задайте переменную, указывающую на локальную папку, в которую вы хотите их скачать. Для каждого скачиваемого большого двоичного объекта задайте имя и вызовите командлет [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent).

В этом примере показано копирование больших двоичных объектов на локальный диск D:\\_TestImages\Downloads. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Копирование BLOB-объектов

При копировании больших двоичных объектов следует учитывать следующее. Вы можете просто скопировать большой двоичный объект в папку с новым именем в том же расположении. Вы можете скопировать большой двоичный объект в отдельную учетную запись хранения. Вы можете скопировать крупный большой двоичный объект (например, VHD-файл) в другую учетную запись хранения. Способ копирования будет при этом различаться.

### <a name="simple-blob-copy"></a>Копирование простого большого двоичного объекта

Вы можете скопировать один из больших двоичных объектов в контейнер, скопировав его в новый большой двоичный объект. В этом примере выполняется копирование в тот же контейнер с другим именем, но можно легко создать другой контейнер для копирования. В этом примере показано, как использовать командлет [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) для копирования большого двоичного объекта. Вам необходимо указать источник и имя целевого большого двоичного объекта, а также имя контейнера.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Копирование большого двоичного объекта в другую учетную запись хранения 

Вы можете скопировать большой двоичный объект в отдельную учетную запись хранения. Один из вариантов — скопировать VHD-файл, который поддерживает одну из виртуальных машин, в другую учетную запись хранения, чтобы сделать его резервную копию. 

Настройте вторую учетную запись хранения, получите контекст, настройте контейнер в этой учетной записи хранения и выполните копирование. Эта часть сценария почти аналогична сценарию выше, за исключением использования второй учетной записи хранения.

```powershell
#create new storage acount, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Асинхронное копирование крупных больших двоичных объектов

При копировании крупного большого двоичного объекта в несколько ГБ можно запустить асинхронное копирование и проверять состояние копирования, пока оно не будет завершено. При этом вы можете выполнять и другие операции.

Копирование в пределах учетной записи хранения выполняется быстро. Копирование между двумя учетными записями хранения в одном регионе также не затянется. Однако, если источник и назначение не в одном регионе, копирование может занять несколько часов (в зависимости от расстояния и размера файла). 

В этом сценарии используются переменные, определенные в последнем примере копирования. Разница в том, что при таком копировании регистрируется состояние копирования и ход его выполнения проверяется каждые 10 секунд, пока оно не будет завершено. Вы можете отправить крупный большой двоичный объект в учетную запись хранения, и для завершения копирования понадобится несколько итераций.

Используйте командлет [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate), чтобы запросить состояние копирования. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Удаление blob-объектов

Чтобы удалить большие двоичные объекты из учетной записи хранения, используйте командлет [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Чтение и запись свойств и метаданных большого двоичного объекта

Для доступа к набору свойств и методов возвращаемого объекта **IListBlobItem** необходимо преобразовать его в объект **CloudBlockBlob**, **CloudPageBlob** или **CloudBlobDirectory**. Если тип неизвестен, можно использовать проверку типов, чтобы определить нужный тип. В примере кода ниже показано извлечение и вывод свойств одного из больших двоичных объектов, отправленных ранее, с помощью командлета [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob), а также преобразование результата в объект CloudBlockBlob.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Заполните свойства системы, вызвав FetchAttributes, а затем просмотрите их. Некоторые свойства являются записываемыми, и их значения можно изменить. В этом примере показано, как изменить тип содержимого, известный также как тип MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Каждый большой двоичный объект имеет собственные метаданные, которые можно задать. Например, вы можете сохранить имя пользователя, который отправил большой двоичный объект, а также дату и время, когда он был впервые отправлен. Метаданные состоят из пар "ключ — значение". Это можно изменить с помощью PowerShell следующим образом.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Управление системой безопасности больших двоичных объектов 

По умолчанию служба хранилища Azure обеспечивает безопасность данных, ограничивая доступ к учетной записи владельца, у которого есть ключи доступа к учетной записи хранения. Если вы хотите предоставить доступ к данным больших двоичных объектов в своей учетной записи хранения, важно сделать это без ущерба для безопасности ключей доступа к учетной записи. Для этого можно использовать подписанный URL-адрес. Он является URL-адресом ресурса, содержащим параметры запроса и маркер безопасности, который позволяет задать определенный уровень доступа в течение определенного времени. Например, вы можете разрешить доступ на чтение к закрытому большому двоичному объекту в течение 5 минут, и тогда его смогут просмотреть. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Назначение закрытого уровня доступа к контейнеру и его большим двоичным объектам

Сначала необходимо задать уровень доступа контейнера `Off`. Это значит, что без подписанного URL-адреса или ключа доступа ни у кого не будет доступа к контейнеру и его объектам. Используйте командлет [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Проверка закрытого доступа

Чтобы проверить отсутствие доступа к большим двоичным объектам в этом контейнере, создайте URL-адрес к одному из больших двоичных объектов без подписанного URL-адреса и попробуйте просмотреть большой двоичный объект. При использовании протокола HTTPS у URL-адреса будет следующий формат:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Ниже показано, как создать URL-адрес большого двоичного объекта.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Скопируйте URL-адрес большого двоичного объекта и вставьте его в окно браузера в конфиденциальном режиме. Вы получите ошибку авторизации, так как большой двоичный объект является закрытым и вы не использовали подписанный URL-адрес. 

### <a name="create-the-sas-uri"></a>Создание универсального кода ресурса SAS

Создайте универсальный код ресурса SAS. Это ссылка на большой двоичный объект, которая содержит параметры запроса и маркер безопасности, которые и формируют подписанный URL-адрес. Вставьте этот код URI в окно браузера в конфиденциальном режиме — появится изображение. 

Сразу задайте время, в течение которого доступ будет действительным, т. е. дату и время начала и окончания срока действия доступа. В примере ниже используется срок действия 2 минуты. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Создайте универсальный код ресурса SAS с помощью командлета [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). Вам нужны имя контейнера, имя большого двоичного объекта, контекст учетной записи хранения, предоставляемые разрешения (в этом примере на чтение, запись и удаление), а также срок действия доступа. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Скопируйте универсальный код ресурса SAS и вставьте его в конфиденциальное окно браузера. Появится изображение.

Дождитесь истечения срока действия URL-адреса (2 минуты в этом примере), а затем вставьте этот URL-адрес в другое конфиденциальное окно браузера. В этот раз вы получите ошибку авторизации, а изображение будет недоступно из-за истечения срока действия универсального кода ресурса SAS.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите все созданные ресурсы. Для этого можно удалить группу ресурсов, что приведет к удалению всех ресурсов в группе. В этом случае происходит удаление всех учетных записей хранения и самой группы ресурсов.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В рамках этого руководства вы изучили основы управления хранилищем BLOB-объектов и узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание контейнера 
> * Отправка больших двоичных объектов
> * Перечисление BLOB-объектов в контейнере 
> * Скачивание больших двоичных объектов
> * Копирование BLOB-объектов
> * Удаление blob-объектов
> * Чтение и запись свойств и метаданных большого двоичного объекта
> * Управление безопасностью с помощью подписанных URL-адресов

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Командлеты Microsoft Azure PowerShell для службы хранилища
* [Командлеты PowerShell для службы хранилища](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Обозреватель службы хранилища Microsoft Azure
* [Обозреватель хранилищ Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
