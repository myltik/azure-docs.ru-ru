<properties
	pageTitle="Использование табличного хранилища из Python | Microsoft Azure"
	description="Хранение структурированных данных в облаке в хранилище таблиц Azure (хранилище данных NoSQL)."
	services="storage"
	documentationCenter="python"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="gusapost;tamram"/>


# Использование табличного хранилища из Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища таблиц Azure. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python]. Здесь описаны такие сценарии, как создание и удаление таблицы, а также вставка и запрос сущностей в таблице.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание таблицы

Объект **TableService** позволяет работать со службами таблиц. Следующий код создает объект **TableService**. Добавьте этот код в начало любого файла Python, из которого планируется получать доступ к службе хранилища Azure программным способом:

	from azure.storage.table import TableService, Entity

Следующий код создает объект **TableService**, используя имя и ключ доступа учетной записи хранения. Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте словарь или сущность, которая определяет имена и значения свойств сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей. Вы можете выполнить запрос этих значений гораздо быстрее, чем других свойств. Система использует **PartitionKey**, чтобы автоматически распространять сущности таблиц на множество узлов хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

Чтобы добавить сущность в таблицу, передайте объект словаря в метод **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Также можно передать экземпляр класса **Entity** в метод **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## Обновление сущности

Этот код показывает, как заменить старую версию имеющейся сущности на обновленную версию.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', task)

Если обновляемая сущность отсутствует, операция обновления завершается ошибкой. Если вам необходимо сохранить сущность независимо от того, существовала ли она ранее, следует использовать метод **insert\_or\_replace\_entity**. В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, поскольку сущность с указанным **PartitionKey** и **RowKey** в таблице отсутствует.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', task)

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '3', 'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', task)

## Изменение группы сущностей

Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Чтобы сделать это, используйте класс **TableBatch**. Если требуется отправить пакет, вызовите метод **commit\_batch**. Обратите внимание, что для изменения в пакетном режиме все сущности должны находиться в одном разделе. В следующем примере показано добавление двух сущностей в пакете.

	from azure.storage.table import TableBatch
	batch = TableBatch()
	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	batch.insert_entity(task10)
	batch.insert_entity(task11)
	table_service.commit_batch('tasktable', batch)

Для пакетов также можно использовать синтаксис диспетчера контекста:

	task12 = {'PartitionKey': 'tasksSeattle', 'RowKey': '12', 'description' : 'Go grocery shopping', 'priority' : 400}
	task13 = {'PartitionKey': 'tasksSeattle', 'RowKey': '13', 'description' : 'Clean the bathroom', 'priority' : 100}

	with table_service.batch('tasktable') as batch:
		batch.insert_entity(task12)
		batch.insert_entity(task13)


## Запрос сущности

Чтобы запросить сущность в таблице, используйте метод **get\_entity**, передав значения **PartitionKey** и **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Запрос набора сущностей

Этот пример находит все задачи в Сиэтле на основе **PartitionKey**.

	tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности. Этот метод, который называется *проекцией*, снижает потребление полосы пропускания и может повысить производительность запросов, особенно для крупных сущностей. Используйте параметр **select** и передайте имена свойств, которые необходимо передать клиенту.

Запрос в следующем коде возвращает только описания сущностей в таблице.

[AZURE.NOTE] Следующий фрагмент работает только для службы облачного хранения. Он не поддерживается эмулятором хранения.

	tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
	for task in tasks:
		print(task.description)

## Удаление сущности

Сущность можно удалить с помощью ее ключей раздела и строки.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Удаление таблицы

Следующий код удаляет таблицу из учетной записи хранения.

	table_service.delete_table('tasktable')

## Дальнейшие действия

Вы ознакомились с основными понятиями хранилища таблиц. Дополнительные сведения см. по следующим ссылкам.

- [Центр по разработке для Python](/develop/python/)
- [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Блог рабочей группы службы хранилища Azure]
- [пакет SDK для службы хранилища Microsoft Azure для Python]

[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->