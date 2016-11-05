### Пример преобразования типов
В следующем примере данные копируются из большого двоичного объекта в SQL Azure с преобразованием типов.

Предположим, набор данных большого двоичного объекта представлен в формате CSV и содержит три столбца. Один из них — это столбец даты и времени, в котором данные указаны в пользовательском формате (используются французские сокращения дней недели).

Указать исходный набор данных большого двоичного объекта и определить типы столбцов нужно так:

    {
        "name": "AzureBlobTypeSystemInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
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

Учитывая таблицу преобразования из типа SQL в тип .NET выше, таблицу SQL Azure можно определить по следующей схеме:

| Имя столбца | Тип SQL |
| --- | --- |
| userid |bigint |
| name |text |
| lastlogindate |datetime; |

Далее нужно определить набор данных SQL Azure, как показано ниже. Примечание. Указывать раздел structure с информацией о типах не нужно, поскольку типы уже указаны в базовом хранилище данных.

    {
        "name": "AzureSQLOutput",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }

При перемещении данных из большого двоичного объекта в SQL Azure фабрика данных автоматически преобразует типы (в том числе тип поля с датой и временем в пользовательском формате), используя язык и региональные параметры fr-fr.

<!---HONumber=Oct15_HO3-->