## <a name="fileshare-dataset-type-properties"></a>Свойства типа "Набор данных FileShare"
Полный список разделов и свойств, используемых для определения наборов данных, см. в статье [Наборы данных](../articles/data-factory/data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных.

Разделы **typeProperties** для каждого типа набора данных отличаются. Он предоставляет сведения, которые относятся к типу набора данных. Раздел typeProperties набора данных с типом **FileShare** содержит следующие свойства.

| Свойство | Описание | Обязательно |
| --- | --- | --- |
| folderPath |Подпуть к папке. Чтобы указать специальные знаки в строке, используйте escape-знак "\". Примеры приведены в разделе [Примеры определений связанной службы и набора данных](#sample-linked-service-and-dataset-definitions).<br/><br/>Вы можете использовать это свойство вместе с параметром **partitionBy**, чтобы в путях к папкам учитывались дата и время начала и окончания среза. |Да |
| fileName |Укажите имя файла в папке **folderPath** , если таблица должна ссылаться на определенный файл в папке. Если этому свойству не присвоить значение, таблица будет указывать на все файлы в папке.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: <br/><br/>Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |Нет |
| fileFilter |Укажите фильтр для выбора подмножества файлов из folderPath. Фильтр дает возможность выбирать только некоторые файлы, а не все.<br/><br/>Допустимые значения: `*` (несколько знаков) и `?` (один знак).<br/><br/>Пример 1: `"fileFilter": "*.log"`<br/>Пример 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> Свойство fileFilter применяется к входному набору данных FileShare. HDFS не поддерживает это свойство. |Нет |
| partitionedBy |Чтобы указать для временного ряда данных динамические путь к папке и имя файла, используйте свойство partitionedBy. Например, путь к папке folderPath каждый час будет другим. |Нет |
| свойства | Поддерживаются следующие типы формата: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](#specifying-textformat), [формате Json](#specifying-jsonformat), [формате Avro](#specifying-avroformat), [формате Orc](#specifying-orcformat) и [ формате Parquet](#specifying-parquetformat). <br><br> Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. Дополнительные сведения см. в разделе [Указание сжатия](#specifying-compression). |Нет |
| useBinaryTransfer |Укажите, использовать ли режим передачи в двоичном формате. Значение true, если использовать двоичный режим, и false, если ASCII. Значение по умолчанию: True. Это свойство можно использовать, только когда тип связанной службы является FTP-сервер. |Нет |

> [!NOTE]
> Свойства filename и fileFilter нельзя использовать одновременно.
>
>

### <a name="using-partionedby-property"></a>Использование свойства partionedBy
Как сказано выше, для временных рядов данных путь к папке и имя файла можно указывать динамически. Это делается с помощью свойства partitionedBy. Вы можете это сделать при помощи макроса фабрики данных и системных переменных SliceStart и SliceEnd, которые определяют логический период имеющегося среза данных.

Чтобы узнать о наборах данных временных рядов, планировании и срезах, ознакомьтесь со статьями [Наборы данных в фабрике данных Azure](../articles/data-factory/data-factory-create-datasets.md), [Планирование и исполнение с использованием фабрики данных](../articles/data-factory/data-factory-scheduling-and-execution.md) и [Конвейеры и действия в фабрике данных Azure](../articles/data-factory/data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Пример 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например, wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

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
