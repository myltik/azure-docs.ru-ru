<properties 
	pageTitle="Использование табличного хранилища из Python в Microsoft Azure" 
	description="Вы узнаете, как использовать службу таблиц в Python для создания и удаления таблиц, вставки, удаления строк и создания запросов для таблиц." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>


# Использование табличного хранилища из Python

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием табличного хранилища Azure. Примеры написаны на Python и используют [пакет Azure для Python][]. Здесь описаны такие сценарии, как **создание и удаление таблицы, вставка и запрос сущностей в таблице**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.NOTE] Если требуется установить Python или [пакет Azure для Python][], см. [Руководство по установке Python](../python-how-to-install.md).


## Создание таблицы

Объект **TableService** позволяет работать со службами таблиц. Следующий код создает объект **TableService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import TableService, Entity

Следующий код создает объект **TableService**, используя имя учетной записи хранения и ключ учетной записи.  Замените 'myaccount' и 'mykey' фактическими значениями учетной записи и ключа.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте словарь, который определяет имена и значения свойств сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, которые можно запросить гораздо быстрее, чем для других свойств. Система использует **PartitionKey**, чтобы автоматически распространять сущности таблиц на множество узлов хранилища.
Сущности с одним значением **PartitionKey** хранятся на одном узле. Свойство
**RowKey** - это уникальный идентификатор сущности в разделе, которому она принадлежит.

Чтобы добавить сущность в таблицу, передайте объект словаря в метод **insert_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Также можно передать экземпляр класса **Entity** в метод **insert_entity**.

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

Если обновляемая сущность отсутствует, операция обновления завершается ошибкой. Если вам необходимо сохранить сущность независимо от того, существовала ли она ранее, следует использовать метод **insert_or_replace_entity**. 
В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, поскольку сущность с указанным **PartitionKey** и **RowKey** в таблице отсутствует.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## Изменение группы сущностей

Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Чтобы сделать это, используйте метод **begin_batch** для **TableService**, а затем вызовите обычную последовательность операций. Если требуется отправить пакет, вызовите метод **commit_batch**. Обратите внимание, что для изменения в пакетном режиме все сущности должны находиться в одном разделе. В следующем примере показано добавление двух сущностей в пакете.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## Запрос сущности

Для запроса сущности в таблице используйте метод **get_entity**, передав значения **PartitionKey** и **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## Запрос набора сущностей

Этот пример находит все задачи в Сиэтле на основе **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## Практическое руководство. Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности.
Этот метод, который называется *projection*, снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Используйте параметр **select** и передайте имена свойств, которые необходимо передать клиенту.

Запрос в следующем коде возвращает только описания сущностей в таблице.

*Обратите внимание, что следующий фрагмент кода работает только для облачной службы хранилища и не поддерживается в эмуляторе
хранения.*

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

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение и доступ к данным в Azure][]
-   [Посетите блог рабочей группы службы хранилища Azure][]

[Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Посетите блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Пакет Azure для Python]: https://pypi.python.org/pypi/azure  
[пакет Azure для Python]: https://pypi.python.org/pypi/azure  

<!--HONumber=49--> 