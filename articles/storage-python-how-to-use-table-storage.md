<properties urlDisplayName="Table Service" pageTitle="Использование хранилища таблиц (Python - Microsoft Azure" metaKeywords="Azure table Python, creating table Azure, deleting table Azure, inserting table Azure, querying table Azure" description="Learn how to use the Table service from Python to create and delete a table, and insert, delete, and query the table." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Table Storage Service from Python" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />





# Использование службы таблиц в Python
В этом руководстве показано, как реализовать типичные сценарии с использованием службы
хранилища таблиц Azure. Примеры написаны с помощью
Python API. Здесь описаны такие сценарии, как **создание и удаление
таблицы, вставка и запрос сущностей в таблице**. Дополнительные
сведения о таблицах см. в разделе [Дальнейшие действия][Дальнейшие действия].

## Оглавление

[Что такое служба таблиц?][Что такое служба таблиц?]   
 [Основные понятия][Основные понятия]   
 [Создание учетной записи хранения Azure][Создание учетной записи хранения Azure]   
 [Практическое руководство. Создание таблицы][Практическое руководство. Создание таблицы]   
 [Практическое руководство. Добавление сущности в таблицу][Практическое руководство. Добавление сущности в таблицу]   
 [Практическое руководство. Обновление сущности][Практическое руководство. Обновление сущности]   
 [Практическое руководство. Изменение группы сущностей][Практическое руководство. Изменение группы сущностей]   
 [Практическое руководство. Запрос сущности][Практическое руководство. Запрос сущности]   
 [Практическое руководство. Запрос набора сущностей][Практическое руководство. Запрос набора сущностей]   
 [Практическое руководство. Запрос подмножества свойств сущности][Практическое руководство. Запрос подмножества свойств сущности]   
 [Практическое руководство. Удаление сущности][Практическое руководство. Удаление сущности]   
 [Практическое руководство. Удаление таблицы][Практическое руководство. Удаление таблицы]   
 [Дальнейшие действия][Дальнейшие действия]

[WACOM.INCLUDE [руководство-таблица-хранилище](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Создание учетной записи хранения Azure
[WACOM.INCLUDE [создание-хранилище-учетная запись](../includes/create-storage-account.md)]

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python](../python-how-to-install/).


## <a name="create-table"> </a>Создание таблицы

Объект **TableService** позволяет работать со службами таблиц. Следующий
код создает объект **TableService**. Добавьте следующий код
в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import TableService, Entity

Следующий код создает объект **TableService** используя имя и ключ доступа учетной записи.  Замените "myaccount" и "mykey" на фактические значения учетной записи и ключа.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>Добавление сущности в таблицу

Чтобы добавить сущность, сначала создайте словарь, который определяет
имена и значения свойств сущности. Обратите внимание, что для каждой сущности необходимо
задать свойства **PartitionKey** и **RowKey**. Это уникальные идентификаторы
сущностей, которые можно запросить гораздо быстрее,
чем для других свойств. Система использует **PartitionKey**, чтобы
автоматически распространять сущности таблиц на множестве узлов хранилища.
Сущности с одним значением **PartitionKey** хранятся на одном узле. RowKey
**** - это уникальный идентификатор сущности в разделе, 
которому она принадлежит.

Чтобы добавить сущность в таблицу, передайте объект словаря
в метод **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Также можно передать экземпляр класса **Entity** в метод **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Практическое руководство. Обновление сущности

Этот код показывает, как заменить старую версию существующей сущности
на обновленную версию.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Если сущность, которая обновляется, не существует, операция обновления
завершается ошибкой. Если вам необходимо сохранить сущность
независимо от того, существовала ли она ранее, следует использовать **insert\_or\_replace_entity**. 
В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, поскольку сущность с указанным **PartitionKey** и **RowKey** в таблице отсутствует.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Изменение группы сущностей

Иногда имеет смысл отправлять совместно несколько операций в
пакете для атомарной обработки сервером. Чтобы сделать это,
используйте метод **begin\_batch** для **TableService** и затем вызовите
обычную последовательность операций. Если вы хотите отправить
пакет, вызовите **commit\_batch**. Обратите внимание, что для изменения в пакетном режиме все сущности должны находиться в одном разделе. В следующем примере показано добавление двух сущностей в пакете.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>Как выполнить запрос сущности

Для запроса сущности в таблице используйте метод **get\_entity** , 
передав имя таблицы **PartitionKey** и **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>Практическое руководство. Запрос набора сущностей

Этот пример находит все задачи в Сиэтле на основе **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>Практическое руководство. Запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности.
Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить
производительность запросов, особенно для крупных сущностей. Используйте параметр **select**
и передайте имена свойств, которые необходимо передать
клиенту.

Запрос в следующем коде возвращает только  **описания** 
сущностей в таблице.

Обратите внимание, что следующий фрагмент работает только для облачной службы 
хранилища и не поддерживается эмулятором 
хранения.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>Практическое руководство. Удаление сущности

Сущность можно удалить с помощью ее ключей раздела и строки.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Практическое руководство. Удаление таблицы

Следующий код удаляет таблицу из учетной записи хранения.

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Дальнейшие действия

Вы изучили основные сведения о табличном хранилище. Дополнительные сведения о более
сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   Справочник MSDN: [Хранение данных и доступ к ним в Azure][Хранение данных и доступ к ним в Azure]
-   [Посетите блог команды разработчиков хранилища Azure][Посетите блог команды разработчиков хранилища Azure]

  [Дальнейшие действия]: #next-steps
  [Что такое служба таблиц?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Практическое руководство. Создание таблицы]: #create-table
  [Практическое руководство. Добавление сущности в таблицу]: #add-entity
  [Практическое руководство. Обновление сущности]: #update-entity
  [Практическое руководство. Изменение группы сущностей]: #change-entities
  [Практическое руководство. Запрос сущности]: #query-for-entity
  [Практическое руководство. Запрос набора сущностей]: #query-set-entities
  [Практическое руководство. Запрос подмножества свойств сущности]: #query-entity-properties
  [Практическое руководство. Удаление сущности]: #delete-entity
  [Практическое руководство. Удаление таблицы]: #delete-table
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [Посетите блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
