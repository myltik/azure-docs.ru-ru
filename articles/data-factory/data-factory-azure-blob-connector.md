---
title: "Копирование данных в хранилище BLOB-объектов Azure и из него | Документация Майкрософт"
description: "Как копировать данные BLOB-объектов в фабрике данных Azure. Используйте наш пример: как копировать данные в базу данных SQL Azure и хранилище BLOB-объектов Azure и обратно."
keywords: "данные BLOB-объектов, копирование BLOB-объекта Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: 16a094b8a311c43658aad299e206d79e29bafed0
ms.lasthandoff: 04/12/2017


---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Копирование данных в хранилище BLOB-объектов Azure и обратно с помощью фабрики данных Azure
В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure копировать данные в хранилище BLOB-объектов Azure и обратно. Это продолжение статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования.

Данные можно скопировать из любого поддерживаемого в качестве источника хранилища данных в хранилище BLOB-объектов Azure или из хранилища BLOB-объектов Azure в любое поддерживаемое в качестве приемника хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

> [!IMPORTANT]
> Действие копирования поддерживает копирование данных как в учетные записи хранения Azure общего назначения и в хранилище горячих и холодных BLOB-объектов, так и из них. Это действие поддерживает **чтение данных из блочных, добавочных или страничных BLOB-объектов**, однако **запись поддерживается только в блочные BLOB-объекты**. Хранилище Azure класса Premium нельзя использовать в качестве приемника, так как оно поддерживается страничными BLOB-объектами.
>
> Действие копирования не удаляет данные из источника после их успешного копирования в место назначения. Если необходимо удалить исходные данные после успешного копирования, создайте настраиваемое действие для удаления данных и используйте это действие в конвейере.

## <a name="getting-started"></a>Приступая к работе
Можно создать конвейер с действием копирования, которое перемещает данные из хранилища BLOB-объектов Azure или в него с помощью различных инструментов и интерфейсов API.

Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги:

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования.
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных.

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании средств и API-интерфейсов (за исключением .NET API) эти сущности фабрики данных определяются в формате JSON.  Примеры с определениями JSON для сущностей фабрики данных, которые используются для копирования данных из хранилища BLOB-объектов Azure и обратно, см. в разделе [Примеры определений JSON](#json-examples) этой статьи.

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, характерных для хранилища BLOB-объектов Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы
Для связи хранилища Azure с фабрикой данных Azure можно использовать два типа связанных служб: **AzureStorage** и **AzureStorageSas**. Связанная служба хранилища Azure предоставляет фабрике данных глобальный доступ к хранилищу Azure, а связанная служба SAS хранилища Azure — ограниченный или привязанный ко времени доступ к хранилищу Azure. Других различий между этими связанными службами нет. Выберите связанную службу, соответствующую вашим задачам. Более подробно эти службы описаны в следующих разделах.

[!INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Свойства набора данных
Чтобы указать набор данных, представляющий входные или выходные данные в хранилище BLOB-объектов Azure, укажите для свойства типа набора данных значение **AzureBlob**. Для свойства **linkedServiceName** набора данных задайте имя связанной службы хранилища Azure или SAS службы хранилища Azure.  В свойствах типа набора данных указывается **контейнер больших двоичных объектов** и **папка** в хранилище BLOB-объектов.

Полный список разделов и свойств JSON, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Фабрика данных поддерживает следующие CLS-совместимые значения типов на основе .NET, когда нужно указывать сведения о типах в разделе structure для схемы по считываемым источникам данных, таким как большие двоичные объекты Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset, Timespan. Фабрика данных автоматически преобразует типы при переносе данных из источника в приемник.

Раздел **typeProperties** отличается для разных типов наборов данных. Он содержит сведения о расположении данных в хранилище данных, формате данных и т. д. Раздел typeProperties набора данных типа **AzureBlob** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| folderPath |Путь контейнеру и папке в хранилище BLOB-объектов. Пример: myblobcontainer\myblobfolder\ |Да |
| fileName |Имя большого двоичного объекта. Свойство fileName является необязательным и в нем учитывается регистр знаков.<br/><br/>Если указать имя файла, то действие (включая копирование) работает с определенным большим двоичным объектом.<br/><br/>Если значение fileName не указано, то копируются все большие двоичные объекты в folderPath для входного набора данных.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.). |Нет |
| partitionedBy |Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Дополнительные сведения и примеры см. ниже в разделе [Использование свойства partitionedBy](#using-partitionedBy-property). |Нет |
| свойства | Поддерживаются следующие типы формата: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](data-factory-supported-file-and-compression-formats.md#text-format), [формате Json](data-factory-supported-file-and-compression-formats.md#json-format), [формате Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [формате Orc](data-factory-supported-file-and-compression-formats.md#orc-format) и [ формате Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. См. дополнительные сведения о [форматах файлов и сжатия данных в фабрике данных Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Нет |

### <a name="using-partitionedby-property"></a>Использование свойства partitionedBy
Как сказано выше, для данных временных рядов путь к папке (folderPath) и имя файла (fileName) можно указывать динамически. Это делается с помощью свойства **partitionedBy**, [функций фабрики данных и системных переменных](data-factory-functions-variables.md).

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md) и [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Пример 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Пример 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

В этом примере год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

## <a name="copy-activity-properties"></a>Свойства действия копирования
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства, например имя, описание, входные и выходные таблицы, политики и т. д., доступны для всех типов действий. В свою очередь свойства, доступные в разделе **typeProperties** действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников. При перемещении данных из хранилища BLOB-объектов Azure в действии копирования задается тип источника **BlobSource**. Аналогично, при перемещении данных в хранилище BLOB-объектов Azure в действии копирования задается тип приемника **BlobSink**. Этот раздел содержит список свойств, поддерживаемых типами BlobSource и BlobSink.

Для **BlobSource** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True (значение по умолчанию), False |Нет |

Для **BlobSink** в разделе **typeProperties** могут быть указаны следующие свойства.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| copyBehavior |Это свойство определяет поведение функции копирования, когда в качестве источника используется BlobSource или FileSystem. |<b>PreserveHierarchy:</b> сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><br/><b>FlattenHierarchy</b>: все файлы из исходной папки размещаются на первом уровне в целевой папке. Целевые файлы имеют автоматически сформированное имя. <br/><br/><b>MergeFiles</b>: объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано. |Нет |

**BlobSource** также поддерживает эти два свойства для обеспечения обратной совместимости.

* **treatEmptyAsNull**указывает, следует ли интерпретировать null или пустую строку как значение null.
* **skipHeaderLineCount** указывает, сколько строк необходимо пропустить. Применяется, только когда для входного набора данных используется TextFormat.

Аналогичным образом **BlobSink** поддерживает следующее свойство для обеспечения обратной совместимости.

* **blobWriterAddHeader**указывает, следует ли добавлять заголовок определений столбцов при записи в выходной набор данных.

Наборы данных поддерживают теперь такие свойства, которые реализуют те же функции: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

В таблице ниже приведены инструкции по использованию новых свойств набора данных вместо указанных свойств источника и приемника больших двоичных объектов.

| Свойство "Действие копирования" | Свойство "Набор данных" |
|:--- |:--- |
| skipHeaderLineCount в BlobSource |skipLineCount и firstRowAsHeader. Сначала строки пропускаются, а затем первая строка считывается как заголовок. |
| treatEmptyAsNull в BlobSource |treatEmptyAsNull во входном наборе данных |
| blobWriterAddHeader в BlobSink |firstRowAsHeader в выходном наборе данных |

Подробную информацию об этих свойствах см. в разделе [Определение TextFormat](data-factory-supported-file-and-compression-formats.md#text-format).    

### <a name="recursive-and-copybehavior-examples"></a>Примеры recursive и copyBehavior
В данном разделе описываются результаты выполнения операции копирования при использовании различных сочетаний значений recursive и copyBehavior.

| recursive | copyBehavior | Результаты выполнения операции |
| --- | --- | --- |
| Да |preserveHierarchy |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается с такой же структурой, как и исходная папка.<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5 |
| Да |flattenHierarchy |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл3"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл4"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл5" |
| Да |mergeFiles |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1", "Файл2", "Файл3", "Файл4" и "Файл5" объединяется в один файл с автоматически созданным именем. |
| нет |preserveHierarchy |Если у исходной папки "Папка1" такая структура:  <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/><br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |flattenHierarchy |Если у исходной папки "Папка1" такая структура: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл1"<br/>&nbsp;&nbsp;&nbsp;&nbsp;Автоматически созданное имя для "Файл2"<br/><br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |
| нет |mergeFiles |Если у исходной папки "Папка1" такая структура: <br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Файл2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Вложенная_папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Файл5<br/><br/>Целевая папка "Папка1" создается со следующей структурой:<br/><br/>Папка1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Содержимое файлов "Файл1" и "Файл2" объединяется в один файл с автоматически созданным именем. Автоматически созданное имя для "Файл1"<br/><br/>Папка "Вложенная_папка1" с файлами "Файл3", "Файл4" и "Файл5" не будет включена в эту папку. |

## <a name="supported-file-and-compression-formats"></a>Поддерживаемые форматы файлов и сжатия
Подробные сведения см. в разделе [Форматы файлов и сжатия данных в фабрике данных Azure](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Примеры определений JSON
Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в базу данных SQL Azure и хранилище BLOB-объектов Azure и обратно. Тем не менее данные можно копировать **непосредственно** из любых источников в любой указанный [здесь](data-factory-data-movement-activities.md#supported-data-stores-and-formats) приемник. Это делается с помощью действия копирования в фабрике данных Azure.

## <a name="example-copy-data-from-blob-storage-to-sql-database"></a>Пример. Копирование данных из хранилища BLOB-объектов Azure в базу данных SQL
В примере ниже используется следующее:

1. Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Связанная служба типа [AzureStorage](#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [BlobSource](#copy-activity-properties) и [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties).

В этом примере данные временного ряда каждый час копируются из большого двоичного объекта Azure в таблицу SQL Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Связанная служба хранилища Azure**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Фабрика данных Azure поддерживает два типа связанных служб хранилища Azure: **AzureStorage** и **AzureStorageSas**. Для первой необходимо указать строку подключения, содержащую ключ учетной записи, а для второй — URI подписанного URL-адреса. Дополнительные сведения см. в разделе [Связанные службы](#linked-service-properties).  

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, фабрика данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Выходной набор данных SQL Azure**

В этом примере данные копируются в таблицу с именем MyOutputTable в базе данных SQL Azure. Создайте таблицу в базе данных SQL Azure с тем количеством столбцов, которое должно быть в CSV-файле большого двоичного объекта. Новые строки добавляются в таблицу каждый час.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Действие копирования в конвейере с BLOB-объектом в качестве источника и базой данных SQL в качестве приемника:**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **BlobSource**, а для типа **sink** — значение **SqlSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```
## <a name="example-copy-data-from-azure-sql-to-azure-blob"></a>Пример. Копирование данных из базы данных SQL Azure в BLOB-объект Azure
В примере ниже используется следующее:

1. Связанная служба типа [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties).
2. Связанная служба типа [AzureStorage](#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, в котором используются [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) и [BlobSink](#copy-activity-properties).

В этом примере данные временного ряда каждый час копируются из таблицы SQL Azure в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба SQL Azure**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Связанная служба хранилища Azure**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Фабрика данных Azure поддерживает два типа связанных служб хранилища Azure: **AzureStorage** и **AzureStorageSas**. Для первой необходимо указать строку подключения, содержащую ключ учетной записи, а для второй — URI подписанного URL-адреса. Дополнительные сведения см. в разделе [Связанные службы](#linked-service-properties).  

**Входной набор данных SQL Azure**

В примере предполагается, что таблица MyTable уже создана в SQL Azure и содержит столбец с именем timestampcolumn для данных временных рядов.

Если для параметра external задать значение true, то фабрика данных воспримет эту таблицу как внешнюю, которая создана не в результате какого-либо действия в этой службе.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Действие копирования в конвейере с базой данных SQL в качестве источника и BLOB-объектом в качестве приемника:**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для типа **source** установлено значение **SqlSource**, а для типа **sink** — значение **BlobSink**. SQL-запрос, указанный для свойства **SqlReaderQuery** , выбирает для копирования данные за последний час.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

> [!NOTE]
> Сведения о сопоставлении столбцов в наборе данных, используемом в качестве источника, со столбцами в приемнике см. в [этой статье](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

