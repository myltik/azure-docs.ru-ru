---
title: "Как использовать хранилище таблиц Azure с Python | Документация Майкрософт"
description: "Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL)."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a4480750377b3762346e746867b83c3c2a50e46f
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-use-azure-table-storage-with-python"></a>Как использовать хранилище таблиц Azure с Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

В этом руководстве объясняется, как реализовать типичные сценарии хранилища таблиц Azure в Python с помощью [пакета SDK для службы хранилища Microsoft Azure для Python](https://github.com/Azure/azure-storage-python). Здесь описаны такие сценарии, как создание и удаление таблицы, вставка и запрос сущностей.

Работая над сценариями в этом руководстве, вы можете использовать в качестве справки [пакет SDK для службы хранилища Microsoft Azure для Python](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Установка пакета SDK для службы хранилища Microsoft Azure для Python

После создания учетной записи хранения установите [пакет SDK для службы хранилища Microsoft Azure для Python](https://github.com/Azure/azure-storage-python). Дополнительные сведения по установке пакета SDK см. в файле [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) в репозитории GitHub Storage SDK for Python (пакет SDK для службы хранилища для Python).

## <a name="create-a-table"></a>Создание таблицы

Для работы со службой таблиц Azure в Python необходимо импортировать модуль [TableService][py_TableService]. Так как вы будете работать с сущностями в таблице, вам также нужен класс [Entity][py_Entity]. Добавьте следующий код в начало файла Python, чтобы импортировать модуль и класс:

```python
from azure.storage.table import TableService, Entity
```

Создайте объект [TableService][py_TableService], передав ключ учетной записи и имя учетной записи хранения. Замените `myaccount` и `mykey` именем и ключом своей учетной записи и вызовите метод [create_table][py_create_table], чтобы создать таблицу в службе хранилища Azure.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте объект, который представляет сущность, затем передайте объект в метод [TableService][py_TableService].[insert_entity][py_insert_entity]. Объект сущности может быть словарем или объектом типа [Entity][py_Entity]. Он определяет имена и значения свойств сущности. Каждая сущность должна включать требуемые свойства [PartitionKey и RowKey](#partitionkey-and-rowkey) помимо других свойств, определенных для сущности.

В этом примере создается объект словаря, который представляет сущность, а затем передает его в метод [insert_entity][py_insert_entity], чтобы добавить его в таблицу:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

В этом примере создается объект [Entity][py_Entity], а затем передает его в метод [insert_entity][py_insert_entity], чтобы добавить его в таблицу:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey и RowKey

Для каждой сущности необходимо указать свойства **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, так как вместе они формируют первичный ключ сущности. С помощью этих значений можно отправлять запросы быстрее, чем к другим свойствам, так как индексируются только эти свойства.

Служба таблиц использует **PartitionKey** для интеллектуального распределения сущностей таблицы по узлам хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

## <a name="update-an-entity"></a>Обновление сущности

Чтобы обновить все значения свойств сущности, вызовите метод [update_entity][py_update_entity]. Этот пример показывает, как заменить сущность обновленной версией:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Если обновляемая сущность больше не существует, операция обновления завершается ошибкой. Если вы хотите сохранить сущность независимо от того, существует она или нет, используйте метод [insert_or_replace_entity][py_insert_or_replace_entity]. В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, так как в таблице нет сущности с указанными свойствами PartitionKey и RowKey.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Метод [update_entity][py_update_entity] заменяет все свойства и значения сущности. Его также можно использовать для удаления свойства из сущности. Чтобы обновить сущность с помощью новых или измененных значений свойств без полной замены сущности используйте метод [merge_entity][py_merge_entity].

## <a name="modify-multiple-entities"></a>Изменение нескольких сущностей

Для атомарной обработки запроса службой таблиц можно отправить сразу несколько операций в пакете. Сначала используйте класс [TableBatch][py_TableBatch], чтобы добавить несколько операций в одном пакете. Затем вызовите метод [TableService][py_TableService].[commit_batch][py_commit_batch], чтобы отправить операции в атомарной операции. Все сущности, которые должны быть изменены в пакете, должны находиться в одном разделе.

В этом примере показано добавление двух сущностей в пакете:

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Для пакетов также можно использовать синтаксис диспетчера контекста:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Запрос сущности

Чтобы запросить сущность в таблице, передайте ее свойства PartitionKey и RowKey в метод [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Запрос набора сущностей

Можно запросить набор сущностей, указав строку фильтра с помощью параметра **filter**. Этот пример находит все задачи в Сиэтле, используя фильтр PartitionKey:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности

Также можно ограничить свойства, возвращаемые для каждой сущности в запросе. Этот метод, который называется *проекцией*, снижает потребление пропускной способности и может повысить производительность запросов, особенно для больших сущностей и наборов результатов. Используйте параметр **select** и передайте имена свойств, которые необходимо вернуть клиенту.

Запрос в следующем коде возвращает только описания сущностей в таблице.

> [!NOTE]
> Следующий фрагмент работает только для службы хранилища Azure. Его не поддерживает эмулятор хранения.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Удаление сущности

Чтобы удалить сущность, передайте ее свойства PartitionKey и RowKey в метод [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Удаление таблицы

Если вам больше не нужна таблица или сущность в ней, вызовите метод [delete_table][py_delete_table], чтобы полностью удалить таблицу из службы хранилища Azure.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Дальнейшие действия

* [Azure Storage SDK for Python API reference](https://azure-storage.readthedocs.io/en/latest/index.html) (Справочник по пакету SDK службы хранилища Azure для API Python)
* [Пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python)
* [Центр по разработке для Python](https://azure.microsoft.com/develop/python/)
* [Приступая к работе с обозревателем службы хранилища (предварительная версия)](../vs-azure-tools-storage-manage-with-storage-explorer.md). Обозреватель службы хранилища Microsoft Azure — бесплатное кроссплатформенное приложение для визуализации данных службы хранилища Azure в Windows, macOS и Linux.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch