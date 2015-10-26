<properties
	pageTitle="Использование табличного хранилища из Python | Microsoft Azure"
	description="Узнайте, как создать, удалить, вставить и запросить таблицу с помощью службы таблиц в Python."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="emgerner"/>


# Использование табличного хранилища из Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища таблиц Azure. Примеры написаны на Python и используют пакет [Хранилище Azure для Python][]. Здесь описаны такие сценарии, как создание и удаление таблицы, а также вставка и запрос сущностей в таблице.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE]Если требуется установить Python или [пакет Azure для Python][], см. [Руководство по установке Python](../python-how-to-install.md).


## Создание таблицы

Объект **TableService** позволяет работать со службами таблиц. Следующий код создает объект **TableService**. Добавьте этот код в начало любого файла Python, из которого планируется получать доступ к службе хранилища Azure программным способом:

	from azure.storage.table import TableService, Entity

Следующий код создает объект **TableService**, используя имя и ключ доступа учетной записи хранения. Замените myaccount и mykey фактическими значениями учетной записи и ключа.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте словарь, который определяет имена и значения свойств сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей. Вы можете запрашивать эти значения гораздо быстрее, чем другие свойства. Система использует **PartitionKey**, чтобы автоматически распространять сущности таблиц на множество узлов хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

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

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Если обновляемая сущность отсутствует, операция обновления завершается ошибкой. Если вам необходимо сохранить сущность независимо от того, существовала ли она ранее, следует использовать метод **insert\_or\_replace\_entity**. В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, поскольку сущность с указанным **PartitionKey** и **RowKey** в таблице отсутствует.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## Изменение группы сущностей

Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Чтобы сделать это, используйте метод **begin\_batch** для **TableService**, а затем вызовите обычную последовательность операций. Если требуется отправить пакет, вызовите метод **commit\_batch**. Обратите внимание, что для изменения в пакетном режиме все сущности должны находиться в одном разделе. В следующем примере показано добавление двух сущностей в пакете.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## Запрос сущности

Чтобы запросить сущность в таблице, используйте метод **get\_entity**, передав значения **PartitionKey** и **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Запрос набора сущностей

Этот пример находит все задачи в Сиэтле на основе **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности. Этот метод, который называется *проекцией*, снижает потребление полосы пропускания и может повысить производительность запросов, особенно для крупных сущностей. Используйте параметр **select** и передайте имена свойств, которые необходимо передать клиенту.

Запрос в следующем коде возвращает только описания сущностей в таблице.

[AZURE.NOTE]Следующий фрагмент работает только для службы облачного хранения. Он не поддерживается эмулятором хранения.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## Удаление сущности

Сущность можно удалить с помощью ее ключей раздела и строки.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## Удаление таблицы

Следующий код удаляет таблицу из учетной записи хранения.

	table_service.delete_table('tasktable')

## Дальнейшие действия

Вы изучили основную информацию о хранилище таблиц. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранилище Azure][].
-   Посетите [блог команды разработчиков службы хранилища Azure][].

Дополнительные сведения см. в [Центре разработчика Python](/develop/python/).


[Хранилище Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[блог команды разработчиков службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет Azure для Python]: https://pypi.python.org/pypi/azure
[Хранилище Azure для Python]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=Oct15_HO3-->