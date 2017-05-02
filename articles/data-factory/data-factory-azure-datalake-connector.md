---
title: "Перемещение данных в Azure Data Lake Store и из него | Документация Майкрософт"
description: "Узнайте, как с помощью фабрики данных Azure перемещать данные в озеро данных Azure и обратно"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 3f0575a170eb20d136858bedc1f87d4f4375c812
ms.lasthandoff: 04/15/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Перемещение данных в озеро данных Azure и обратно с помощью фабрики данных Azure
В этой статье рассказывается, как с помощью действия копирования в фабрике данных Azure перемещать данные в Azure Data Lake Store и обратно. Этот документ является продолжением статьи о [действиях перемещения данных](data-factory-data-movement-activities.md), в которой приведены общие сведения о перемещении данных с помощью действия копирования.

Данные можно скопировать из любого поддерживаемого в качестве источника хранилища данных в Azure Data Lake Store или из Azure Data Lake Store в любое поддерживаемое в качестве приемника хранилище данных. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, см. в таблице [Поддерживаемые хранилища данных и форматы](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

> [!NOTE]
> Чтобы создать конвейер с действием копирования для перемещения данных в Azure Data Lake Store и обратно, вам потребуется учетная запись этого хранилища. Сведения об Azure Data Lake Store см. в статье [Начало работы с хранилищем озера данных Azure с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Поддерживаемые типы аутентификации
Соединитель Azure Data Lake Store поддерживает проверку подлинности на основе **субъекта-службы** и проверку подлинности на основе **учетных данных пользователя** (OAuth). Рекомендуется использовать первый тип, особенно для запланированных заданий копирования данных, чтобы избежать истечения срока действия токена во втором типе проверки подлинности. Сведения о конфигурации см. в разделе [Свойства связанной службы](#linked-service-properties).

## <a name="getting-started"></a>Приступая к работе
Можно создать конвейер с действием копирования, которое перемещает данные из Azure Data Lake Store или обратно с помощью различных инструментов и интерфейсов API.

Проще всего создать конвейер с помощью **мастера копирования**. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Также для создания конвейера можно использовать следующие инструменты: **портал Azure**, **Visual Studio**, **Azure PowerShell**, **шаблон Azure Resource Manager**, **API .NET** и **REST API**. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Независимо от используемого средства или API-интерфейса, для создания конвейера, который перемещает данные из источника данных в приемник, выполняются следующие шаги:

1. Создайте **связанные службы**, чтобы связать входные и выходные данные с фабрикой данных.
2. Создайте **наборы данных**, которые представляют входные и выходные данные для операции копирования.
3. Создайте **конвейер** с действием копирования, который принимает входной набор данных и возвращает выходной набор данных.

Если вы используете мастер, то он автоматически создает определения JSON для сущностей фабрики данных (связанных служб, наборов данных и конвейера). При использовании средств и API-интерфейсов (за исключением .NET API) эти сущности фабрики данных определяются в формате JSON.  Примеры с определениями JSON для сущностей фабрики данных, которые используются для копирования данных из Azure Data Lake Store, см. в разделе [Примеры определений JSON](#json-examples) этой статьи.

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, характерных для Azure Data Lake Store.

## <a name="linked-service-properties"></a>Свойства связанной службы
Связанная служба привязывает хранилище данных к фабрике данных. Для связи Azure Data Lake Store с фабрикой данных следует создать связанную службу типа **AzureDataLakeStore**. В следующей таблице содержится описание элементов JSON, которые относятся к связанной службе Azure Data Lake Store. Можно выбрать тип аутентификации: на основе **субъекта-службы** или на основе **учетных данных пользователя**.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type | Для свойства type следует задать значение **AzureDataLakeStore** | Да |
| dataLakeStoreUri | Указывает сведения об учетной записи хранилища озера данных Azure Используемый формат: `https://[accountname].azuredatalakestore.net/webhdfs/v1` или `adl://[accountname].azuredatalakestore.net/`. | Да |
| subscriptionId | Идентификатор подписки Azure, к которой принадлежит Data Lake Store. | Необходимо для приемника |
| имя_группы_ресурсов | Имя группы ресурсов Azure, к которой принадлежит Data Lake Store. | Необходимо для приемника |

### <a name="using-service-principal-authentication-recommended"></a>Использование аутентификации на основе субъекта-службы (рекомендуется)
При использовании аутентификации на основе субъекта-службы необходимо зарегистрировать сущность приложения в Azure Active Directory (AAD) и предоставить ей доступ к Data Lake Store. Подробные инструкции см. в разделе [Взаимодействие между службами](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Запишите **идентификатор приложения**, **ключ приложения** и **идентификатор клиента**. Эти значения используются в определении связанной службы.

> [!IMPORTANT]
> Если вы используете мастер копирования для создания конвейеров данных, обязательно назначьте субъекту-службе как минимум роль читателя в службе управления доступом (IAM) для учетной записи Data Lake Store и как минимум разрешение на чтение и выполнение для корня Data Lake Store ("/") и его дочерних элементов. Иначе может появиться ошибка "указанные учетные данные недействительны".
>
> Для применения изменений после создания или обновления субъекта-службы в AAD может потребоваться несколько минут. Сначала внимательно проверьте конфигурацию ACL субъекта-службы и Data Lake Store. Если по-прежнему отображается ошибка "Указаны недопустимые учетные данные", подождите некоторое время и повторите попытку.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Да |
| servicePrincipalKey | Укажите ключ приложения. | Да |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Его можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Да |

**Пример: использование аутентификации на основе субъекта-службы**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>Использование аутентификации на основе учетных данных пользователя
Кроме того, для копирования данных в Data Lake Store и обратно можно использовать проверку подлинности на основе учетных данных пользователя, указав приведенные ниже свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| authorization | Нажмите кнопку **Авторизовать** в **редакторе фабрики данных** и введите учетные данные. URL-адрес авторизации будет создан автоматически и присвоен этому свойству. | Да |
| sessionId | Идентификатор сеанса OAuth из сеанса авторизации OAuth. Каждый идентификатор сеанса является уникальным и используется только один раз. Этот параметр создается автоматически при использовании редактора фабрики данных. | Да |

**Пример: использование аутентификации на основе учетных данных пользователя**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Срок действия маркера
Срок действия кода авторизации, созданного с помощью кнопки **Авторизовать**, через некоторое время истекает. Сроки действия для различных типов учетных записей пользователей см. в следующей таблице. В случае истечения **срока действия маркера** аутентификации может появиться следующее сообщение об ошибке.

```
"Credential operation error: invalid_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z".
```

| Тип пользователя | Срок действия |
|:--- |:--- |
| Учетные записи пользователей, которые НЕ управляются с помощью Azure Active Directory (@hotmail.com, @live.com и т. д.) |12 часов |
| Учетные записи пользователей, которые управляются Azure Active Directory (AAD) |14 дней после последнего запуска среза. <br/><br/>90 дней, если срез, основанный на связанной службе на основе OAuth, выполняется по крайней мере раз в 14 дней. |

Если пароль изменяется прежде, чем срок действия маркера истечет, маркер мгновенно устаревает и отображается указанная в этом разделе ошибка.

Чтобы избежать этой ошибки или исправить ее, вам потребуется повторно авторизоваться с помощью кнопки **Авторизовать** и повторно развернуть связанную службу, когда **срок действия маркера истечет**. Значения свойств **sessionId** и **authorization** можно также задавать программно с помощью кода, приведенного в следующем разделе.

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Программное создание значений свойств sessionId и authorization

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Подробные сведения о классах фабрики данных, используемых в коде, см. в статьях [AzureDataLakeStoreLinkedService — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) и [AuthorizationSessionGetResponse — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Для использования в коде класса WindowsFormsWebAuthenticationDialog необходимо добавить ссылку на версию **2.9.10826.1824** файла **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll**.

## <a name="dataset-properties"></a>Свойства набора данных
Чтобы указать набор данных, представляющий входные данные в Azure Data Lake Store, присвойте свойству типа набора данных значение **AzureDataLakeStore**. Для свойства **linkedServiceName** набора данных задайте имя связанной службы Azure Data Lake Store. Полный список разделов и свойств JSON, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.). Раздел **typeProperties** отличается для разных типов наборов данных. Он содержит сведения о расположении данных в хранилище данных, формате данных и т. д. Раздел typeProperties набора данных типа **AzureDataLakeStore** содержит следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| folderPath |Путь к контейнеру и папке в хранилище озера данных Azure. |Да |
| fileName |Имя файла в хранилище озера данных Azure. Свойство fileName является необязательным и в нем учитывается регистр знаков. <br/><br/>Если указать имя файла, то действие (включая копирование) работает с определенным файлом.<br/><br/>Если значение fileName не указано, то копируются все файлы в folderPath для входного набора данных.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: Data<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.). |Нет |
| partitionedBy |Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Дополнительные сведения и примеры см. ниже в разделе [Использование свойства partitionedBy](#using-partitionedby-property). |Нет |
| свойства | Поддерживаются следующие типы формата: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах о [текстовом формате](data-factory-supported-file-and-compression-formats.md#text-format), [формате Json](data-factory-supported-file-and-compression-formats.md#json-format), [формате Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [формате Orc](data-factory-supported-file-and-compression-formats.md#orc-format) и [ формате Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Если требуется скопировать файлы между файловыми хранилищами **как есть** (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression | Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate**, **BZip2** и **ZipDeflate**. Поддерживаемые уровни: **Optimal** и **Fastest**. См. дополнительные сведения о [форматах файлов и сжатия данных в фабрике данных Azure](data-factory-supported-file-and-compression-formats.md#compression-support). |Нет |

### <a name="using-partitionedby-property"></a>Использование свойства partitionedBy
Как сказано выше, для данных временных рядов путь к папке (folderPath) и имя файла (fileName) можно указывать динамически. Это делается с помощью свойства **partitionedBy**, [функций фабрики данных и системных переменных](data-factory-functions-variables.md).

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md) и [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Пример 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Пример 2
```JSON
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
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

В свою очередь свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

**AzureDataLakeStoreSource** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True (значение по умолчанию), False |Нет |

**AzureDataLakeStoreSink** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| copyBehavior |Определяет поведение копирования. |<b>PreserveHierarchy:</b> сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><br/><b>FlattenHierarchy</b>: все файлы из исходной папки создаются на первом уровне в целевой папке. Целевые файлы создаются с автоматически создаваемыми именами.<br/><br/><b>MergeFiles</b>: объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано. |Нет |

## <a name="supported-file-and-compression-formats"></a>Поддерживаемые форматы файлов и сжатия
Подробные сведения см. в разделе [Форматы файлов и сжатия данных в фабрике данных Azure](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Примеры определений JSON
Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в Azure Data Lake Store и хранилище BLOB-объектов Azure и обратно. Однако данные можно скопировать данные **непосредственно** из любых источников на любой из поддерживаемых приемников. Дополнительные сведения см. в разделе "Поддерживаемые хранилища данных и форматы" статьи [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md).  
## <a name="example-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Пример. Копирование данных из BLOB-объекта Azure в Azure Data Lake Store
В примере ниже используется следующее:

1. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
2. Связанная служба типа [AzureDataLakeStore](#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureDataLakeStore](#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, который использует [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) и [AzureDataLakeStoreSink](#copy-activity-properties).

В этом примере данные временного ряда каждый час копируются из хранилища BLOB-объектов Azure в Azure Data Lake Store. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища Azure**

```JSON
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

**Связанная служба Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Сведения о настройках см. в разделе [Свойства связанной службы хранилища озера данных Azure](#linked-service-properties).
>

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
      ]
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

**Выходной набор данных Azure Data Lake Store:**

Этот пример кода копирует данные в хранилище озера данных Azure. Новые данные копируются в озеро данных каждый час.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Действие копирования в конвейере с BLOB-объектом в качестве источника и Azure Data Lake Store в качестве приемника:**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **BlobSource**, а для **sink** — тип **AzureDataLakeStoreSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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

## <a name="example-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Пример. Копирование данных из Azure Data Lake Store в BLOB-объект Azure
В примере ниже используется следующее:

1. Связанная служба типа [AzureDataLakeStore](#linked-service-properties).
2. Связанная служба типа [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureDataLakeStore](#dataset-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, который использует [AzureDataLakeStoreSource](#copy-activity-properties) и [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

В этом примере данные временного ряда каждый час копируются из Azure Data Lake Store в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища озера данных Azure:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Сведения о настройках см. в разделе [Свойства связанной службы хранилища озера данных Azure](#linked-service-properties).
>

**Связанная служба хранилища Azure**

```JSON
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
**Входной набор данных озера данных Azure:**

Если для параметра **external задать значение true** , то фабрика данных воспримет эту таблицу как внешнюю, которая создана не в результате какого-либо действия в этой службе.

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
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
**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Действие копирования в конвейере с Azure Data Lake Store в качестве источника и BLOB-объектом в качестве приемника:**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **AzureDataLakeStoreSource**, а для параметра **sink** — тип **BlobSink**.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

Также можно сопоставить столбцы из набора данных, используемого в качестве источника, со столбцами из приемника в определении действия копирования. Дополнительные сведения см. в статье, посвященной [сопоставлению столбцов наборов данных в фабрике данных Azure](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.

