---
title: "Работа с моментальными снимками общих ресурсов (предварительная версия) | Документация Майкрософт"
description: "Моментальный снимок общих ресурсов — это версия общих файловых ресурсов Azure только для чтения на определенный момент времени, позволяющая создать резервную копию общих ресурсов."
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
ms.openlocfilehash: 5212866bda9ff775d32ebb57874b3d58e11f1eb3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2017
---
# <a name="work-with-share-snapshots-preview"></a>Работа с моментальными снимками общих ресурсов (предварительная версия)
Моментальный снимок общего ресурса (предварительная версия) — это предназначенная только для чтения версия общего файлового ресурса Azure, созданная в определенный момент времени. После создания моментального снимка общего ресурса его можно читать, копировать или удалять, но нельзя изменять. Моментальные снимки общих ресурсов обеспечивают резервное копирование общих ресурсов в том виде, в котором они пребывают в определенный момент времени. 

Из этой статьи вы узнаете, как создавать и удалять моментальные снимки общих ресурсов, а также как управлять ими. Дополнительные сведения см. в [обзоре моментальных снимков общих ресурсов](storage-snapshots-files.md) и на странице [часто задаваемых вопросов о моментальных снимках](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Создание моментального снимка общего ресурса

Моментальный снимок общего ресурса можно создать с помощью портала Azure, PowerShell, Azure CLI, REST API или любого пакета SDK службы хранилища Azure. Следующие разделы содержат сведения о том, как создать моментальный снимок общего ресурса с помощью портала, интерфейса командной строки и PowerShell. 

Моментальный снимок общего файлового ресурса можно создать во время его использования. Тем не менее моментальные снимки общих ресурсов позволяют фиксировать только те данные, которые уже были записаны в общий файловый ресурс на момент поступления команды для создания моментального снимка. Таким образом можно исключить все данные, кэшированные любыми приложениями или операционной системой.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Создание моментального снимка общего ресурса с помощью портала  
Чтобы создать моментальный снимок общего ресурса на определенный момент времени, перейдите к файловому ресурсу на портале и выберите команду **Создать моментальный снимок**.

>   ![Меню моментальных снимков на портале](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Создание моментального снимка общего ресурса с помощью Azure CLI 2.0
Вы можете создать моментальный снимок общих ресурсов с помощью команды `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Выходные данные примера:
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

### <a name="create-a-share-snapshot-by-using-powershell"></a>Создание моментального снимка общего ресурса с помощью PowerShell
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

## <a name="perform-common-share-snapshot-operations"></a>Выполнение стандартных операций с моментальными снимками общих ресурсов

Вы можете перечислить моментальные снимки общих ресурсов, связанные с файловым ресурсом, используя вкладку **Предыдущие версии** в ОС Windows, с помощью REST, клиентской библиотеки, PowerShell и портала. После подключения файлового ресурса можно просмотреть все предыдущие версии файла с помощью вкладки **Предыдущие версии** в Windows. 

В следующих разделах объясняется, как использовать портал Azure, ОС Windows и Azure CLI 2.0 для вывода списка, просмотра и восстановления моментальных снимков общих ресурсов.

### <a name="share-snapshot-operations-in-the-portal"></a>Операции с моментальными снимками общих ресурсов на портале

На портале можно просмотреть все моментальные снимки общего файлового ресурса, а также перейти к моментальному снимку и просмотреть содержимое файлового ресурса.

#### <a name="view-a-share-snapshot"></a>Просмотр моментального снимка общего ресурса
В общем файловом ресурсе в меню **Моментальный снимок** выберите пункт **Просмотреть моментальные снимки**.

![Команда "Просмотреть моментальные снимки" на портале](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Вывод списка и просмотр содержимого моментальных снимков общих ресурсов
Просмотрите список моментальных снимков общих ресурсов, а затем перейдите непосредственно к содержимому отдельного моментального снимка, выбрав требуемую метку времени.

![Выбранный моментальный снимок в списке меток времени](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Вы также можете нажать кнопку **подключения** в представлении списка моментальных снимков, чтобы получить команду `net use` и путь к каталогу определенного моментального снимка. Затем вы можете перейти непосредственно к этому снимку.


![Область подключения с окном команд](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Скачивание и восстановление с помощью моментального снимка общего ресурса
На портале можно скачать или восстановить файл из моментального снимка с помощью кнопки **Скачать** или **Восстановить** соответственно.

![Кнопки "Скачать" и "Восстановить"](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>Операции с моментальными снимками общих ресурсов в Windows
Если вы уже создали моментальные снимки общего файлового ресурса, вы можете просмотреть предыдущие версии общего ресурса, каталога или определенного файла из подключенного общего файлового ресурса в Windows. Например, вот как с помощью функции "Предыдущие версии" можно просмотреть и восстановить предыдущую версию каталога в Windows.

> [!Note]  
> Одни и те же операции можно выполнять на уровне папки или файла. В списке отображается только версия, содержащая изменения для этого каталога или файла. Если конкретный каталог или файл не изменился между созданием двух моментальных снимков общего ресурса, моментальный снимок отображается в списке предыдущих версий на уровне общего ресурса, но не в списке предыдущих версий каталога или файла.

#### <a name="mount-a-file-share"></a>Подключение общего ресурса
Сначала подключите общий файловый ресурс с помощью команды `net use`.

#### <a name="open-a-mounted-share-in-file-explorer"></a>Открытие подключенного общего ресурса в проводнике
Перейдите в проводник файлов и найдите подключенные общие файловые ресурсы.

![Подключенный общий ресурс в проводнике](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Вывод списка предыдущих версий
Перейдите к элементу или родительскому элементу, который требуется восстановить. Дважды щелкните для перехода к нужному каталогу. Щелкните правой кнопкой мыши и выберите в меню пункт **Свойства**.

![Контекстное меню для выбранного каталога](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Выберите пункт **Предыдущие версии**, чтобы просмотреть список моментальных снимков общих ресурсов для этого каталога. Вывод списка может занять несколько секунд в зависимости от скорости сети и количества моментальных снимков в каталоге.

![Вкладка "Предыдущие версии"](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Чтобы открыть тот или иной моментальный снимок, нажмите кнопку **Открыть**. 

![Открытый моментальный снимок](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Восстановление из предыдущей версии
Нажмите кнопку **Восстановить**, чтобы рекурсивно скопировать содержимое всего каталога на момент создания моментального снимка общего ресурса в исходном расположении.
 ![Кнопка "Восстановить" в предупреждении](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Операции с моментальными снимками общих ресурсов в Azure CLI 2.0
С помощью Azure CLI 2.0 можно выполнять такие операции, как вывод списка моментальных снимков общих ресурсов, просмотр их содержимого, восстановление или скачивание файлов из моментальных снимков или удаление моментальных снимков общих ресурсов.

#### <a name="list-share-snapshots"></a>Вывод списка моментальных снимков общих ресурсов

Список моментальных снимков определенного общего ресурса можно вывести с помощью команды [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) с параметром `--include-snapshots`.

```azurecli-interactive 
az storage share list --include-snapshots
```

Эта команда позволяет получить список моментальных снимков общих ресурсов и всех связанных с ними свойств. Вот пример выходных данных:

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

#### <a name="browse-to-a-share-snapshot"></a>Просмотр снимка общего ресурса
Вы можете перейти к определенному моментальному снимку общего ресурса и просмотреть его содержимое с помощью команды [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Укажите имя общего ресурса и метку времени, к которой требуется перейти, как показано в следующем примере:

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

В выходных данных видно, что содержимое моментального снимка общего ресурса идентично содержимому общего ресурса в момент создания моментального снимка:

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
#### <a name="restore-from-a-share-snapshot"></a>Восстановление из моментального снимка общего ресурса

Вы можете восстановить файл, скопировав или скачав его из моментального снимка общего ресурса. Используйте команду `az storage file download`, как показано в следующем примере.

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

В выходных данных видно, что содержимое скачанного файла и его свойства идентичны содержимому и свойствам на момент, когда был создан моментальный снимок общего ресурса.

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

## <a name="delete-a-share-snapshot"></a>Удаление моментального снимка общего ресурса

Удалить моментальные снимки общих ресурсов можно с помощью портала Azure, PowerShell, интерфейса командной строки, REST API или любого пакета SDK службы хранилища. Следующие разделы содержат сведения о том, как удалять моментальные снимки общих ресурсов с помощью портала Azure, интерфейса командной строки и PowerShell.

Вы можете перейти к моментальным снимкам общих ресурсов и просмотреть различия между двумя моментальными снимками с помощью любого средства сравнения. Этого поможет вам определить, какой моментальный снимок следует удалить. 

Нельзя удалить общий ресурс, для которого создан моментальный снимок. Чтобы удалить общий ресурс, сначала необходимо удалить все его моментальные снимки.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>Удаление моментального снимка общего ресурса с помощью портала  
На портале перейдите в колонку общих файловых ресурсов и нажмите кнопку **Удалить**, чтобы удалить один или несколько моментальных снимков общих ресурсов.

>   ![Кнопка "Удалить"](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Удаление моментального снимка общего ресурса с помощью Azure CLI 2.0
Вы можете удалить моментальный снимок общего ресурса с помощью команды `[az storage share delete]`. Для параметра `--snapshot '2017-10-04T23:28:35.0000000Z' ` в следующем примере используйте метку времени требуемого моментального снимка:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Выходные данные примера:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Удаление моментального снимка общего ресурса с помощью PowerShell
Вы можете удалить моментальный снимок общего ресурса с помощью команды `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор моментальных снимков для службы файлов Azure (предварительная версия)](storage-snapshots-files.md)
* [Часто задаваемые вопросы о службе "Файлы Azure"](storage-files-faq.md)
