---
title: "Использование моментального снимка общих файловых ресурсов Azure (предварительная версия) | Документация Майкрософт"
description: "Использование моментального снимка общих файловых ресурсов Azure. Моментальный снимок общих файловых ресурсов Azure является версией только для чтения общих файловых ресурсов Azure, сделанной в определенный момент времени. После создания моментального снимка общего ресурса его можно читать, копировать или удалять, но нельзя изменять. Моментальные снимки общих ресурсов обеспечивают способ резервного копирования общих ресурсов в том виде, в котором они находятся в данный момент времени."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 6643dad5ea3ba703e26f5708cdd2e925f702847f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>Работа с моментальными снимками общих файловых ресурсов Azure (предварительная версия)
Моментальный снимок общих файловых ресурсов Azure (предварительная версия) является версией только для чтения общих файловых ресурсов Azure, сделанной в определенный момент времени. После создания моментального снимка общего ресурса его можно читать, копировать или удалять, но нельзя изменять. Моментальные снимки общих ресурсов обеспечивают способ резервного копирования общих ресурсов в том виде, в котором они находятся в данный момент времени. В этой статье будет рассказано о том, как создавать и удалять моментальные снимки общих файловых ресурсов Azure, а также как управлять ими. Дополнительные сведения о моментальных снимках общего ресурса см. в статье [Обзор моментального снимка общих файловых ресурсов Azure (предварительная версия)](storage-snapshots-files.md) или [Часто задаваемые вопросы о службе "Файлы Azure"](storage-files-faq.md).

## <a name="create-azure-files-share-snapshots"></a>Создание моментальных снимков общих файловых ресурсов Azure

Моментальный снимок общих файловых ресурсов можно создать с помощью портала, Powershell, интерфейса командной строки, REST или любого пакета SDK службы хранилища. Следующие несколько разделов содержат сведения о том, как создать моментальный снимок общих ресурсов с помощью портала, интерфейса командной строки и PowerShell. 

Моментальный снимок общих файловых ресурсов можно создать во время его использования. Тем не менее, моментальные снимки общих ресурсов позволяют записывать только те данные, которые уже были записаны в общий файловый ресурс Azure в момент выдачи команды моментального снимка общего ресурса. Таким образом можно исключить все данные, кэшированные любыми приложениями или операционной системой.

### <a name="create-share-snapshot-using-portal"></a>Создание моментального снимка общих ресурсов с помощью портала  
Просто перейдите к файловому ресурсу на портал и нажмите кнопку `Create a Snapshot`, чтобы создать снимок общего ресурса до точки во времени.

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>Создание моментального снимка общих ресурсов с помощью CLI 2.0
Вы можете создать моментальный снимок общих ресурсов с помощью команды `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Пример выходных данных
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-share-snapshot-using-powershell"></a>Создание моментального снимка общих ресурсов с помощью Powershell
Вы можете создать моментальный снимок общих ресурсов с помощью команды `$share.Snapshot()`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```
## <a name="list-share-snapshots-browse-share-snapshot-contents-and-restore-from-snapshots"></a>Вывод списка моментальных снимков общих ресурсов, просмотр их содержимого и восстановление с помощью моментальных снимков

Вы можете перечислить моментальные снимки общих ресурсов, связанные с вашим файловым ресурсом, используя интеграцию "Предыдущие версии" в ОС Windows, с помощью REST, клиентской библиотеки, PowerShell и портала. После подключения общих файловых ресурсов Azure можно просмотреть все предыдущие версии файла с помощью интеграции SMB "Предыдущие версии". После подключения общих файловых ресурсов Azure можно просмотреть все предыдущие версии каталога с помощью интеграции SMB "Предыдущие версии". В следующих разделах вы узнаете, как использовать портал Azure, Windows и Azure CLI 2.0 для вывода списка, просмотра и восстановления моментальных снимков общего ресурса.

### <a name="share-snapshot-operations-in-portal"></a>Операции с моментальными снимками общих ресурсов на портале

Вы можете просмотреть все моментальные снимки общих ресурсов для общих файловых ресурсов на портале, а также просматривать их содержимое.

#### <a name="view-share-snapshot"></a>Просмотр моментального снимка общих ресурсов
В общих файловых ресурсах на вкладке "Моментальный снимок" выберите **View snapshots** (Просмотреть моментальные снимки).

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>Вывод списка и просмотр содержимого моментального снимка общих ресурсов
Просмотрите список моментальных снимков общих ресурсов, а затем просмотрите их содержимое напрямую, выбрав моментальный снимок общих ресурсов с требуемой меткой времени.

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Вы также можете нажать кнопку **Подключение** в представлении списка моментальных снимков, чтобы получить команду `net use` и путь к каталогу определенного моментального снимка, который вы можете напрямую просмотреть.


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>Скачивание и восстановление с помощью моментальных снимков общих ресурсов
На портале скачайте или восстановите нужный файл из моментального снимка.

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Операции с моментальными снимками общих файловых ресурсов в Windows
Если вы уже сделали моментальные снимки общих файловых ресурсов, вы можете просматривать предыдущие версии общих ресурсов, каталога или определенного файла из подключенного вами моментального снимка общих файловых ресурсов Azure в Windows. Например, вот как можно использовать функцию **Предыдущие версии**, чтобы просматривать и восстанавливать предыдущую версию конкретного каталога в Windows:

> [!Note]  
> Аналогичные операции можно выполнить на уровне папок, а также на уровне файлов. В списке отображается только версия, содержащая изменения для этого каталога или файла. Если конкретный каталог или файл не изменился между созданием двух моментальных снимков общих ресурсов, моментальный снимок отображается только в списке предыдущих версий уровня общих ресурсов, но не в списке предыдущей версии каталога или файла.

#### <a name="mount-file-share"></a>Подключение общих файловых ресурсов
Сначала подключите общие файловые ресурсы с помощью команды net use.

#### <a name="open-mounted-share-in-explorer"></a>Открытие подключенных общих файловых ресурсов в проводнике
Перейдите в проводник файлов и найдите подключенные общие файловые ресурсы.

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Вывод списка предыдущих версий
 Перейдите к элементу или родительскому элементу, который требуется восстановить. Дважды щелкните для перехода к нужному каталогу. Щелкните правой кнопкой мыши и выберите "Свойства" в меню.

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Выберите **Предыдущие версии** для просмотра списка моментальных снимков общих ресурсов для этого каталога. Вывод списка может занять несколько секунд в зависимости от скорости сети и количества моментальных снимков в папке.

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Щелкните **Открыть** для просмотра отдельного моментального снимка. 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Восстановление из предыдущей версии
Нажмите кнопку **Восстановить**, чтобы рекурсивно скопировать содержимое всего каталога на момент создания моментального снимка общих ресурсов в исходном расположении.
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Операции с моментальными снимками общих файловых ресурсов в Azure CLI 2.0
Вы можете использовать Azure CLI 2.0 для выполнения одних и тех же операций, таких как вывод списка моментальных снимков общих ресурсов, просмотр их содержимого, восстановление или загрузка файлов из моментального снимка или удаление моментальных снимков общих ресурсов.

#### <a name="list-share-snapshots"></a>Вывод списка моментальных снимков общих ресурсов

Вы можете вывести список моментальных снимков определенной общей папки с помощью команды [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) с `--include-snapshots`.

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>Пример выходных данных
Команда позволяет получить список моментальных снимков общих ресурсов и всех связанных свойств.

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-share-snapshots"></a>Просмотр снимков общего ресурса
Вы также можете перейти к определенному моментальному снимку общего ресурса, чтобы просмотреть его содержимое. Для этого используйте команду [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Укажите имя общего ресурса `--share-name` и метку времени `--snapshot '2017-10-04T19:45:18.0000000Z'`, к которой требуется перейти.

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>Пример выходных данных

Вы увидите, что содержимое моментального снимка общих ресурсов идентично содержимому общего ресурса в момент создания моментального снимка.

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-share-snapshots"></a>Восстановление из моментальных снимков общего ресурса

Вы можете восстановить файл, скопировав или скачав его из моментального снимка общего ресурса с помощью команды `az storage file download`.

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>Пример выходных данных

Вы увидите, что содержимое скачанного файла и его свойства идентичны содержимому и свойствам в момент, когда был создан моментальный снимок.

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-azure-files-share-snapshot"></a>Удаление моментальных снимков общих файловых ресурсов Azure

Удалить моментальные снимки общих файловых ресурсов можно с помощью портала Azure, PowerShell, интерфейса командной строки, REST API или любого пакета SDK службы хранилища. Следующие несколько разделов содержат сведения о том, как удалить снимок общих ресурсов с помощью портала Azure, интерфейса командной строки и PowerShell.

Вы можете просмотреть моментальные снимки общих ресурсов и просмотреть различия между двумя из них, используя любой инструмент сравнения, чтобы определить, какой моментальный снимок вы хотите удалить. 

Нельзя удалить общий ресурс с моментальным снимком общих ресурсов. Чтобы иметь возможность удалить общий ресурс, сначала необходимо удалить все моментальные снимки общих ресурсов.

### <a name="delete-share-snapshot-using-portal"></a>Удаление моментальных снимков общих ресурсов с помощью портала  
Перейдите в колонку общих файловых ресурсов и нажмите кнопку `delete` на портале, чтобы удалить один или несколько моментальных снимков общих ресурсов.

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>Удаление моментальных снимков общих ресурсов с помощью Azure CLI 2.0
Чтобы удалить моментальный снимок общего ресурса, используйте команду `[az storage share delete]` и укажите для параметра `--snapshot '2017-10-04T23:28:35.0000000Z' ` метку времени этого снимка.

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Пример выходных данных
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>Удаление моментальных снимков общих ресурсов с помощью PowerShell
Вы можете создать моментальный снимок общих ресурсов с помощью команды `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор моментальных снимков файловых ресурсов Azure (предварительная версия)](storage-snapshots-files.md)
* [Часто задаваемые вопросы о службе "Файлы Azure"](storage-files-faq.md)