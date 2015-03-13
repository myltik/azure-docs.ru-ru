<properties 
	pageTitle="Как использовать табличное хранилище (Python) - Microsoft Azure" 
	description="Вы узнаете, как использовать службу таблиц в Python для создания и удаления таблиц, вставки, удаления строк и создания запросов для таблиц." 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>





# Использование службы таблиц в Python
В этом руководстве показано, как реализовать типичные сценарии с использованием службы табличного хранилища Microsoft Azure. Примеры написаны с помощью Python API. Здесь описаны такие сценарии, как **создание и удаление таблицы, а также вставка и запрос сущностей в таблице**. Дополнительную информацию о таблицах см. в разделе [Дальнейшие действия][].

## Оглавление

[Что такое служба таблиц?][]
 [Основные понятия][]   
 [Создание учетной записи хранения Azure][]   
 [Практическое руководство. Создание таблицы][]   
 [Практическое руководство. Добавление сущности в таблицу][]   
 [Практическое руководство. Обновление сущности][]   
 [Практическое руководство. Изменение группы сущностей][]   
 [Практическое руководство. Запрос сущности][]   
 [Практическое руководство. Запрос набора сущностей][]   
 [Практическое руководство. Запрос подмножества свойств сущности][]   
 [Практическое руководство. Удаление сущности][]   
 [Практическое руководство. Удаление таблицы][]   
 [Дальнейшие действия][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a name="create-account"> </a>Создание учетной записи хранения Azure
[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Примечание.* Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python](../python-how-to-install/).


## <a name="create-table"> </a>Как создать таблицу

Объект **TableService** позволяет работать со службами таблиц. Следующий код создает объект **TableService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import TableService, Entity

Следующий код создает объект **TableService**, используя имя учетной записи хранения и ключ учетной записи.  Замените  'myaccount' и  'mykey' фактическими значениями учетной записи и ключа.

	table_service = TableService(account_name='myaccount', account_key='mykey')

	table_service.create_table('tasktable')

## <a name="add-entity"> </a>Как добавить сущность в таблицу

Чтобы добавить сущность, сначала создайте словарь, который определяет имена и значения свойств сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, которые можно запросить гораздо быстрее, чем для других свойств. Система использует **PartitionKey**, чтобы автоматически распространять сущности таблиц на множество узлов хранилища.
Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** - это уникальный идентификатор сущности в разделе, которому она принадлежит.

Чтобы добавить сущность в таблицу, передайте объект словаря в метод **insert\_entity**.

	task = {'PartitionKey': 'tasksSeattle', 'RowKey': '1', 'description' : 'Take out the trash', 'priority' : 200}
	table_service.insert_entity('tasktable', task)

Можно также передать экземпляр класса **Entity** в метод **insert\_entity**.

	task = Entity()
	task.PartitionKey = 'tasksSeattle'
	task.RowKey = '2'
	task.description = 'Wash the car'
	task.priority = 100
	table_service.insert_entity('tasktable', task)

## <a name="update-entity"> </a>Как обновить сущность

Этот код показывает, как заменить старую версию имеющейся сущности на обновленную версию.

	task = {'description' : 'Take out the garbage', 'priority' : 250}
	table_service.update_entity('tasktable', 'tasksSeattle', '1', task)

Если обновляемая сущность отсутствует, операция обновления завершается ошибкой. Если вам необходимо сохранить сущность независимо от того, существовала ли она ранее, следует использовать метод **insert\_or\_replace_entity**. 
В следующем примере первый вызов заменит существующую сущность. Второй вызов вставит новую сущность, поскольку сущность с указанным **PartitionKey** и **RowKey** в таблице отсутствует.

	task = {'description' : 'Take out the garbage again', 'priority' : 250}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '1', task)

	task = {'description' : 'Buy detergent', 'priority' : 300}
	table_service.insert_or_replace_entity('tasktable', 'tasksSeattle', '3', task)

## <a name="change-entities"> </a>Как изменить группу сущностей

Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Чтобы сделать это, используйте метод **begin\_batch** в **TableService**, а затем вызовите обычную последовательность операций. Если вы хотите отправить пакет, вызовите метод **commit\_batch**. Обратите внимание, что для изменения в пакетном режиме все сущности должны находиться в одном разделе. В следующем примере показано добавление двух сущностей в пакете.

	task10 = {'PartitionKey': 'tasksSeattle', 'RowKey': '10', 'description' : 'Go grocery shopping', 'priority' : 400}
	task11 = {'PartitionKey': 'tasksSeattle', 'RowKey': '11', 'description' : 'Clean the bathroom', 'priority' : 100}
	table_service.begin_batch()
	table_service.insert_entity('tasktable', task10)
	table_service.insert_entity('tasktable', task11)
	table_service.commit_batch()

## <a name="query-for-entity"> </a>Как выполнить запрос сущности

Для запроса сущности в таблице используйте метод **get\_entity**, передавая значения **PartitionKey** и **RowKey**.

	task = table_service.get_entity('tasktable', 'tasksSeattle', '1')
	print(task.description)
	print(task.priority)

## <a name="query-set-entities"> </a>Как запросить набор сущностей

Этот пример находит все задачи в Сиэтле на основе **PartitionKey**.

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'")
	for task in tasks:
		print(task.description)
		print(task.priority)

## <a name="query-entity-properties"> </a>Как выполнить запрос подмножества свойств сущности

Запрос к таблице может получить лишь несколько свойств сущности.
Этот метод, который называется  *проекцией*, снижает потребление пропускной способности и может повысить производительность запросов, особенно для больших сущностей. Используйте параметр **select** и передайте имена свойств, которые необходимо передать клиенту.

Запрос в следующем коде возвращает только описания сущностей **Descriptions** в таблице.

Обратите внимание, что следующий фрагмент кода работает только для облачной службы хранилища и не поддерживается в эмуляторе
хранения.*

	tasks = table_service.query_entities('tasktable', "PartitionKey eq 'tasksSeattle'", 'description')
	for task in tasks:
		print(task.description)

## <a name="delete-entity"> </a>Как удалить сущность

Сущность можно удалить с помощью ее ключей раздела и строки.

	table_service.delete_entity('tasktable', 'tasksSeattle', '1')

## <a name="delete-table"> </a>Как удалить таблицу

Следующий код удаляет таблицу из учетной записи хранения.

	table_service.delete_table('tasktable')

## <a name="next-steps"> </a>Дальнейшие действия

Теперь, когда вы ознакомились с основами использования табличного хранилища, следуйте приведенным ссылкам, чтобы узнать о том, как выполнять более сложные задачи хранения.

-   См. справочник MSDN: [Хранение и доступ к данным в Azure][]
-   [Посетите блог рабочей группы службы хранилища Azure][]

  [Дальнейшие действия]: #next-steps
  [Что такое служба таблиц]: #what-is
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
  [Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Посетите блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
<!--HONumber=42-->
