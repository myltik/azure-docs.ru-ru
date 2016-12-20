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
ms.date: 09/27/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 701d82971b7da92fb0946cbfc7f708ad32501ef3
ms.openlocfilehash: b3957c93a0b536b67f81d7e7be52d918a8e82ead


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Перемещение данных в озеро данных Azure и обратно с помощью фабрики данных Azure
В этой статье описано использование действия копирования в фабрике данных Azure для перемещения данных Azure Data Lake Store в другое хранилище данных и обратно. Эта статья основана на статье о [действиях перемещения данных](data-factory-data-movement-activities.md) , в которой приведены общие сведения о перемещении данных с помощью действия копирования и о поддерживаемых сочетаниях хранилищ данных.

> [!NOTE]
> Чтобы создать конвейер с действием копирования для перемещения данных в Azure Data Lake Store и обратно, вам потребуется учетная запись этого хранилища. Сведения об Azure Data Lake Store см. в статье [Начало работы с хранилищем озера данных Azure с помощью портала Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>
> В руководстве по [созданию первого конвейера](data-factory-build-your-first-pipeline.md) подробно описаны процедуры создания фабрики данных, связанных служб, наборов данных и конвейера. Для создания сущностей фабрики данных запустите предложенные фрагменты кода JSON в редакторе фабрики данных, в Visual Studio или в Azure PowerShell.
>
>

## <a name="copy-data-wizard"></a>Мастер копирования данных
Самый простой способ создать конвейер, копирующий данные в Azure Data Lake Store или из него, — использовать мастер копирования данных. В статье [Руководство. Создание конвейера с действием копирования с помощью мастера копирования фабрики данных](data-factory-copy-data-wizard-tutorial.md) приведены краткие пошаговые указания по созданию конвейера с помощью мастера копирования данных.

Ниже приведены примеры с определениями JSON, которые можно использовать для создания конвейера с помощью [портала Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) или [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). В них показано, как копировать данные в Azure Data Lake Store и хранилище BLOB-объектов Azure и обратно. Однако данные можно скопировать данные **непосредственно** из любых источников на любой из поддерживаемых приемников. Дополнительные сведения см. в разделе "Поддерживаемые хранилища данных и форматы" статьи [Перемещение данных с помощью действия копирования](data-factory-data-movement-activities.md).  

## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Пример. Копирование данных из BLOB-объекта Azure в хранилище озера данных Azure
В примере ниже используется следующее:

1. Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
2. Связанная служба типа [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, который использует [BlobSource](#azure-blob-copy-activity-type-properties) и [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

В этом примере данные временного ряда каждый час копируются из хранилища BLOB-объектов Azure в Azure Data Lake Store. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Связанная служба озера данных Azure:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

### <a name="to-create-azure-data-lake-linked-service-using-data-factory-editor"></a>Создание связанной службы озера данных Azure с помощью редактора фабрики данных
Далее приводятся пошаговые инструкции по созданию связанной службы хранилища озера данных Azure с помощью редактора фабрики данных.

1. Щелкните **Новое хранилище данных** на панели команд и выберите **Azure Data Lake Store**.
2. В редакторе JSON введите универсальный код ресурса (URI) Data Lake в качестве значения свойства **dataLakeStoreUri** .
3. Щелкните кнопку **Авторизовать** на панели команд. Появится всплывающее окно.

    ![Кнопка "Авторизовать"](./media/data-factory-azure-data-lake-connector/authorize-button.png)
4. Войдите в систему с помощью своих учетных данных. После этого свойство **authorization** в JSON должно получить нужное значение.
5. Укажите значения необязательных параметров JSON, например **accountName**, **subscriptionID** и **resourceGroupName**, или удалите эти свойства из JSON. Этот шаг можно пропустить.
6. Чтобы развернуть эту службу, нажмите кнопку **Развернуть** на панели команд.

> [!IMPORTANT]
> Срок действия кода авторизации, созданного с помощью кнопки **Авторизовать**, через некоторое время истекает. Когда **срок действия маркера истечет**, вам потребуется **повторно авторизоваться** с помощью кнопки **Авторизовать** и повторно развернуть связанную службу. Дополнительные сведения см. в разделе [Связанная служба хранилища озера данных Azure](#azure-data-lake-store-linked-service-properties).
>
>

**Входной набор данных BLOB-объекта Azure**

Данные берутся из нового BLOB-объекта каждый час (frequency: hour, interval: 1). Путь к папке с BLOB-объектом и имя файла вычисляются динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц и день начала, а в имени файла — час начала. Когда для параметра external задано значение true, служба фабрики данных считает эту таблицу внешней по отношению к себе и не созданной в результате какого-либо действия в фабрике данных.

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


**Выходной набор данных озера данных Azure:**

Этот пример кода копирует данные в хранилище озера данных Azure. Новые данные копируются в озеро данных каждый час.

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



**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **BlobSource**, а для **sink** — тип **AzureDataLakeStoreSink**.

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
                            "type": "BlobSource",
                            "treatEmptyAsNull": true,
                            "blobColumnSeparators": ","
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Пример. Копирование данных из хранилища озера данных Azure в BLOB-объект Azure
В примере ниже используется следующее:

1. Связанная служба типа [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2. Связанная служба типа [AzureStorage](#azure-storage-linked-service-properties).
3. Входной [набор данных](data-factory-create-datasets.md) типа [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4. Выходной [набор данных](data-factory-create-datasets.md) типа [AzureBlob](#azure-blob-dataset-type-properties).
5. [Конвейер](data-factory-create-pipelines.md) с действием копирования, который использует [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) и [BlobSink](#azure-blob-copy-activity-type-properties).

В этом примере данные временного ряда каждый час копируются из Azure Data Lake Store в большой двоичный объект Azure. Используемые в этих примерах свойства JSON описаны в разделах, следующих за примерами.

**Связанная служба хранилища озера данных Azure:**

    {
        "name": "AzureDataLakeStoreLinkedService",
        "properties": {
            "type": "AzureDataLakeStore",
            "typeProperties": {
                "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
                "sessionId": "<session ID>",
                "authorization": "<authorization URL>"
            }
        }
    }

> [!NOTE]
> Чтобы получить URL-адрес авторизации, см. шаги, приведенные в предыдущем примере.  
>
>

**Связанная служба хранилища Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Входной набор данных озера данных Azure:**

Если для параметра **external задать значение true** , то фабрика данных воспримет эту таблицу как внешнюю, которая создана не в результате какого-либо действия в этой службе.

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

**Выходной набор данных BLOB-объекта Azure**

Данные записываются в новый BLOB-объект каждый час (frequency: hour, interval: 1). Путь к папке BLOB-объекта вычисляется динамически на основе времени начала обрабатываемого среза. В пути к папке используется год, месяц, день и час времени начала.

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

**Конвейер с действием копирования**

Конвейер содержит действие копирования, которое использует входной и выходной наборы данных и выполняется каждый час. В определении JSON конвейера для параметра **source** задается тип **AzureDataLakeStoreSource**, а для параметра **sink** — тип **BlobSink**.

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


## <a name="azure-data-lake-store-linked-service-properties"></a>Свойства связанной службы хранилища озера данных Azure
С помощью связанной службы хранилища Azure учетную запись хранения Azure можно связать с фабрикой данных Azure. В таблице ниже приведено описание элементов JSON, которые относятся к связанной службе хранилища Azure.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| type |Для свойства type следует задать значение **AzureDataLakeStore** |Да |
| dataLakeStoreUri |Указывает сведения об учетной записи хранилища озера данных Azure в следующем формате: https://<Azure Data Lake account name>.azuredatalake.net/webhdfs/v1 |Да |
| authorization |Нажмите кнопку **Авторизовать** в **редакторе фабрики данных** и введите учетные данные. URL-адрес авторизации будет создан автоматически и присвоен этому свойству. |Да |
| sessionId |Идентификатор сеанса OAuth из сеанса авторизации oauth. Каждый идентификатор сеанса является уникальным и используется только один раз. Этот параметр создается автоматически при использовании редактора фабрики данных. |Да |
| accountName |Имя учетной записи озера данных |Нет |
| subscriptionId |Идентификатор подписки Azure. |Нет (если не указан, используется подписка фабрики данных). |
| имя_группы_ресурсов |Имя группы ресурсов Azure |Нет (если не указано, используется группа ресурсов фабрики данных). |

## <a name="token-expiration"></a>Срок действия маркера
Срок действия кода авторизации, созданного с помощью кнопки **Авторизовать**, через некоторое время истекает. Сроки действия для различных типов учетных записей пользователей см. в следующей таблице. При истечении **срока действия маркера** аутентификации может появиться следующее сообщение об ошибке: "Ошибка операции с учетными данными: invalid_grant - AADSTS70002: ошибка при проверке учетных данных. AADSTS70008: срок действия предоставленных прав доступа истек или они были отозваны. Идентификатор отслеживания: d18629e8-af88-43c5-88e3-d8419eb1fca1 Идентификатор корреляции: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Временная отметка: 2015-12-15 21-09-31Z".

| Тип пользователя | Срок действия |
|:--- |:--- |
| Учетные записи пользователей, которые НЕ управляются Azure Active Directory (@hotmail.com, @live.com, и т. д.). |12 часов |
| Учетные записи пользователей, которые управляются Azure Active Directory (AAD) |14 дней после последнего запуска среза. <br/><br/>90 дней, если срез, основанный на связанной службе на основе OAuth, выполняется по крайней мере раз в 14 дней. |

Если пароль изменяется прежде, чем срок действия маркера истечет, маркер мгновенно устаревает и отображается указанная в этом разделе ошибка.

Чтобы избежать этой ошибки или исправить ее, вам потребуется повторно авторизоваться с помощью кнопки **Авторизовать** и повторно развернуть связанную службу, когда **срок действия маркера истечет**. Значения свойств **sessionId** и **authorization** можно также задавать программно с помощью кода, приведенного в следующем разделе.

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Программное создание значений свойств sessionId и authorization
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

Подробные сведения о классах фабрики данных, используемых в коде, см. в статьях [AzureDataLakeStoreLinkedService — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) и [AuthorizationSessionGetResponse — класс](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Для использования в коде класса WindowsFormsWebAuthenticationDialog необходимо добавить ссылку на версию **2.9.10826.1824** файла **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll**.

## <a name="azure-data-lake-dataset-type-properties"></a>Свойства типа "Набор данных озера данных Azure"
Полный список разделов и свойств JSON, используемых для определения наборов данных, см. в статье [Создание наборов данных](data-factory-create-datasets.md). Разделы структуры, доступности и политики JSON набора данных одинаковы для всех типов наборов данных (SQL Azure, большие двоичные объекты Azure, таблицы Azure и т. д.).

Раздел **typeProperties** отличается для разных типов наборов данных. Он содержит сведения о расположении данных в хранилище данных, формате данных и т. д. Раздел typeProperties набора данных типа **AzureDataLakeStore** содержит следующие свойства.

| Свойство | Описание | Обязательно |
|:--- |:--- |:--- |
| folderPath |Путь к контейнеру и папке в хранилище озера данных Azure. |Да |
| fileName |Имя файла в хранилище озера данных Azure. Свойство fileName является необязательным и в нем учитывается регистр знаков. <br/><br/>Если указать имя файла, то действие (включая копирование) работает с определенным файлом.<br/><br/>Если значение fileName не указано, то копируются все файлы в folderPath для входного набора данных.<br/><br/>Если свойство fileName не указано для выходного набора данных, то имя созданного файла будет иметь следующий формат: Data.<Guid>.txt (например, Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.). |Нет |
| partitionedBy |Необязательное свойство. Его можно использовать, чтобы указать динамические путь к папке и имя файла для временного ряда данных. Например, путь к папке (значение folderPath) каждый час может быть другим. Дополнительные сведения и примеры см. ниже в разделе [Использование свойства partitionedBy](#using-partitionedby-property). |Нет |
| свойства |Поддерживаются следующие типы формата: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Свойству **type** в разделе format необходимо присвоить одно из этих значений. Дополнительные сведения см. в разделах [Определение TextFormat](#specifying-textformat), [Определение AvroFormat](#specifying-avroformat), [Указание JsonFormat](#specifying-jsonformat), [Указание OrcFormat](#specifying-orcformat) и [Указание ParquetFormatt](#specifying-parquetformat). Если требуется скопировать файлы между файловыми хранилищами "как есть" (двоичное копирование), можно пропустить раздел форматирования в определениях входного и выходного наборов данных. |Нет |
| compression |Укажите тип и уровень сжатия данных. Поддерживаемые типы: **GZip**, **Deflate** и **BZip2**. Поддерживаемые уровни: **Optimal** и **Fastest**. Сейчас для данных в формате **AvroFormat** или **OrcFormat** параметры сжатия не поддерживаются. Дополнительные сведения см. в разделе [Поддержка сжатия](#compression-support). |Нет |

### <a name="using-partitionedby-property"></a>Использование свойства partitionedBy
Динамические значения folderPath и fileName для данных временных рядов можно задать в разделе **partitionedBy** , а также с помощью макросов фабрики данных и системных переменных SliceStart и SliceEnd, которые указывают время начала и окончания среза данных.

Дополнительные сведения о наборах данных временных рядов, планировании и срезах см. в статьях [Наборы данных в фабрике данных Azure](data-factory-create-datasets.md) и [Планирование и исполнение с использованием фабрики данных](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Пример 1
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

В этом примере {Slice} заменяется значением SliceStart (системная переменная фабрики данных) в формате ГГГГММДДЧЧ. SliceStart указывает время начала среза. Значение folderPath отличается для каждого среза. Например: wikidatagateway/wikisampledataout/2014100103 или wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Пример 2
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

В этом примере год, месяц, день и время SliceStart извлекаются в отдельные переменные, используемые в свойствах folderPath и fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

### <a name="compression-support"></a>Поддержка сжатия
Обработка больших наборов данных может привести к возникновению узких мест ввода-вывода и сети. Поэтому использование сжатых данных в хранилищах может не только ускорить передачу данных по сети и освободить место на диске, но также обеспечить и значительное повышение производительности при обработке больших данных. Сейчас сжатие поддерживается для файловых хранилищ данных, таких как хранилище BLOB-объектов Azure или локальная файловая система.  

Чтобы указать сжатие для набора данных, используйте свойство **compression** в наборе данных JSON, как показано в следующем примере.   

    {  
        "name": "AzureDatalakeStoreDataSet",  
          "properties": {  
            "availability": {  
                "frequency": "Day",  
                  "interval": 1  
            },  
            "type": "AzureDatalakeStore",  
            "linkedServiceName": "DataLakeStoreLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                  "folderPath": "compression/file/",  
                  "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                  }  
            }  
          }  
    }  

Раздел **compression** содержит два свойства:  

* **Type** — кодек сжатия; возможные значения: **GZIP**, **Deflate** и **BZIP2**.  
* **Level** — коэффициент сжатия; возможные значения: **Optimal** и **Fastest**.

  * **Fastest:** операция сжатия должна выполняться как можно быстрее, даже если итоговый файл сжимается не оптимально.
  * **Optimal**: операция сжатия должна выполняться оптимально, даже если для ее завершения требуется больше времени.

    Дополнительные сведения см. в разделе [Уровень сжатия](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Предположим, что пример набора данных используется в качестве результата действия копирования. Это действие сжимает выходные данные с использованием кодека GZIP и оптимального коэффициента сжатия, а затем записывает сжатые данные в файл с именем pagecounts.csv.gz в Azure Data Lake Store.   

Если задать свойство compression во входном наборе данных JSON, конвейер будет считывать сжатые данные из источника. Если задать это свойство в выходном наборе данных JSON, действие копирования может записывать сжатые данные в место назначения. Ниже приведено несколько примеров сценариев:

* Считайте сжатые с помощью кодека GZIP данные из хранилища озера данных Azure, распакуйте их и запишите результирующие данные в базу данных SQL Azure. В этом случае вы определяете входной набор данных хранилища озера данных Azure с помощью свойства "compression" JSON.
* Считайте данные из обычного текстового файла в локальной файловой системе, сожмите их в формате GZip и запишите сжатые данные в хранилище озера данных Azure. В этом случае вы определяете выходной набор озера данных Azure с помощью свойства "compression" JSON.  
* Считайте сжатые с помощью кодека GZIP данные из хранилища озера данных Azure, распакуйте их и сожмите с помощью BZIP2, а затем запишите результирующие данные в хранилище озера данных Azure. Для типа сжатия необходимо установить значение GZIP, а для входного и выходного набора данных — значение BZIP2 соответственно.   

## <a name="azure-data-lake-copy-activity-type-properties"></a>Свойства типа "Действие копирования озера данных Azure"
Полный список разделов и свойств, используемых для определения действий, см. в статье [Создание конвейеров](data-factory-create-pipelines.md). Свойства (включая имя, описание, входные и выходные таблицы, политику и т. д.) доступны для всех типов действий.

С другой стороны, свойства, доступные в разделе typeProperties действия, зависят от конкретного типа действия. Для действия копирования они различаются в зависимости от типов источников и приемников.

**AzureDataLakeStoreSource** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| recursive |Указывает, следует ли читать данные рекурсивно из вложенных папок или только из указанной папки. |True (значение по умолчанию), False |Нет |

**AzureDataLakeStoreSink** поддерживает следующие свойства в разделе **typeProperties**.

| Свойство | Описание | Допустимые значения | Обязательно |
| --- | --- | --- | --- |
| copyBehavior |Определяет поведение копирования. |/ **PreserveHierarchy:** сохраняет иерархию файлов в целевой папке. Относительный путь исходного файла в исходной папке идентичен относительному пути целевого файла в целевой папке.<br/><br/>**FlattenHierarchy**: все файлы из исходной папки создаются на первом уровне в целевой папке. Целевые файлы создаются с автоматически создаваемыми именами.<br/><br/>**MergeFiles**: объединяет все файлы из исходной папки в один файл. Если указано имя файла или большого двоичного объекта, именем объединенного файла будет указанное имя; в противном случае имя файла будет автоматически сформировано. |Нет |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Производительность и настройка
Ознакомьтесь со статьей [Руководство по настройке производительности действия копирования](data-factory-copy-activity-performance.md), в которой описываются ключевые факторы, влияющие на производительность перемещения данных (действие копирования) в фабрике данных Azure, и различные способы оптимизации этого процесса.



<!--HONumber=Nov16_HO3-->


