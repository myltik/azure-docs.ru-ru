<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Управление сущностями таблицы

Теперь, когда имеется таблица, рассмотрим управление сущностями или строками в таблице. 

Сущность может иметь до 255 свойств, включая 3 системных свойства: **PartitionKey**, **RowKey** и **Timestamp**. Вы отвечаете за вставку и обновление значений **PartitionKey** и **RowKey**. Сервер управляет значением **Timestamp**, которое не может быть изменено. Вместе **PartitionKey** и **RowKey** однозначно идентифицируют каждую сущность в пределах таблицы.

* **PartitionKey**— определяет раздел, в котором хранится сущность.
* **RowKey**— уникально определяет сущность в разделе.

Можно определить до 252 свойств для сущности. 

### <a name="add-table-entities"></a>Добавление сущностей таблицы

Добавьте сущности в таблицу, используя **Add-StorageTableRow**. В этих примерах используются ключи разделов со значениями "partition1" и "partition2", а ключи строк соответствуют сокращениям состояния. Свойства в каждой сущности — это имя и идентификатор пользователя. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Запрос сущностей таблицы

Существует несколько способов выполнения запроса сущностей таблицы.

#### <a name="retrieve-all-entities"></a>Получение всех сущностей

Чтобы получить все сущности, используйте **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Эта команда выдает результаты наподобие следующих:

| userid | Имя пользователя | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4. | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Получение сущностей для определенного раздела

Чтобы получить все сущности в отдельном разделе, используйте **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Результаты выглядят как следующая таблица.

| userid | Имя пользователя | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Получение сущностей для конкретного значения в определенном столбце

Чтобы получить сущности, значения которых в определенном столбце равны определенному значению, используйте **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Этот запрос получает одну запись.

|поле|value|
|----|----|
| userid | 1 |
| Имя пользователя | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Получение сущностей с использованием пользовательского фильтра 

Чтобы получить сущности с использованием пользовательского фильтра, используйте **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Этот запрос получает одну запись.

|поле|value|
|----|----|
| userid | 1 |
| Имя пользователя | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Обновление сущностей 

Для обновления сущностей нужно выполнить три шага. Сначала получите сущность, которую необходимо изменить. Затем внесите изменения и зафиксируйте их с помощью **Update-AzureStorageTableRow**.

Измените сущность с именем пользователя = "Jessie" на = "Jessie2". В этом примере также показан другой способ создания пользовательского фильтра с помощью типов .NET. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

В результатах показана запись Jessie2.

|поле|value|
|----|----|
| userid | 2 |
| Имя пользователя | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Удаление сущностей таблицы

Вы можете удалить одну или все сущности в таблице.

#### <a name="deleting-one-entity"></a>Удаление одной сущности

Для удаления одной сущности получите ссылку на сущность и передайте ее в **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Удаление всех сущностей в таблице 

Чтобы удалить все сущности в таблице, получите их и передайте результаты в командлет "remove". 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
