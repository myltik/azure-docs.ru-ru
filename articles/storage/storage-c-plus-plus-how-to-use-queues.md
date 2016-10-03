<properties
    pageTitle="Использование хранилища очередей (C++) | Microsoft Azure"
    description="Узнайте, как использовать службу хранилища очередей в Azure. Примеры написаны на C++."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager=jahogg""
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="dineshm;robinsh"/>

# Использование хранилища очередей из C++  

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)] .<br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Обзор
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранения очередей Azure. Примеры написаны на C++ и используют [клиентскую библиотеку хранилища Azure для C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Здесь описаны такие сценарии, как **вставка**, **просмотр**, **получение** и **удаление** сообщений очереди, а также **создание и удаление очередей**.

>[AZURE.NOTE] Данное руководство предназначено для клиентской библиотеки хранилища Azure для С++ версии 1.0.0 и выше. Рекомендуемая версия — клиентская библиотека хранилища 2.2.0, которая доступна на сайте [NuGet](http://www.nuget.org/packages/wastorage) или [GitHub](http://github.com/Azure/azure-storage-cpp/).

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения на C++  
В этом руководстве будут использоваться компоненты хранилища, которые могут выполняться в приложениях C++.

Для этого необходимо установить клиентскую библиотеку хранилища Azure для C++ и создать учетную запись хранения Azure в подписке Azure.

Чтобы установить клиентскую библиотеку хранилища для C++, можно использовать следующие методы.

-	**Linux:** следуйте инструкциям, указанным в файле README [клиентской библиотеки хранилища Azure для C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
-	**Windows:** в Visual Studio нажмите **Инструменты > Диспетчер пакетов NuGet > Консоль диспетчера пакетов**. Введите следующую команду в [консоли диспетчера пакетов NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) и нажмите клавишу **ВВОД**.

		Install-Package wastorage

## Настройка приложения для доступа к хранилищу очередей
Если нужно использовать API-интерфейсы Azure для доступа к очередям, добавьте следующие инструкции импорта в верхнюю часть файла C++.

	#include "was/storage_account.h"
	#include "was/queue.h"

## Настройка строки подключения к хранилищу Azure

Клиент хранилища Azure использует строку подключения с целью хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и клавишу доступа для учетной записи хранения, указанные на [портале Azure](https://portal.azure.com) значениями *AccountName* и *AccountKey*. Сведения об учетных записях хранения и ключи доступа см. в разделе [Об учетных записях хранения Azure](storage-create-storage-account.md). В этом примере показано, как объявить статическое поле для размещения строки подключения:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Чтобы протестировать приложение на локальном компьютере Windows, можно использовать [эмулятор хранения](storage-use-emulator.md) Microsoft Azure, установленный с [Azure SDK](https://azure.microsoft.com/downloads/). Эмулятор хранения — это программа, моделирующая службы больших двоичных объектов, очередей и таблиц, доступных в Azure на локальном компьютере разработки. В следующем примере показано, как объявить статическое поле для размещения строки подключения для эмулятора локального хранилища.

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Чтобы запустить эмулятор службы хранилища Azure, нажмите кнопку **Пуск** или клавишу **Windows**. Начните вводить **эмулятор хранения Azure** и выберите **эмулятор хранения Microsoft Azure** из списка приложений.

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.

## Получить строку подключения
Информацию о своей учетной записи хранения можно представить с помощью класса **cloud\_storage\_account**. Чтобы получить данные учетной записи хранения из строки подключения хранилища, можно использовать метод **синтаксического анализа**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

## Практическое руководство. Создание очереди
Объект **cloud\_queue\_client** позволяет ссылаться на объекты очередей. Следующий код создает объект **cloud\_queue\_client**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create a queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

С помощью объекта **cloud\_queue\_client** получите ссылку на очередь, которую необходимо использовать. Очередь можно создать, если она не существует.

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Create the queue if it doesn't already exist.
 	queue.create_if_not_exists();  

## Практическое руководство. Вставка сообщения в очередь
Чтобы вставить сообщение в существующую очередь, сначала создайте новый объект **cloud\_queue\_message**. Затем вызовите метод **add\_message**. Объект **cloud\_queue\_message** может быть создан из строки или массива **байтов**. Ниже приведен код, который создает очередь (если она отсутствует) и вставляет сообщение "Hello World".

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Create the queue if it doesn't already exist.
	queue.create_if_not_exists();

	// Create a message and add it to the queue.
	azure::storage::cloud_queue_message message1(U("Hello, World"));
	queue.add_message(message1);  

## Практическое руководство. Просмотр следующего сообщения
Вы можете просмотреть сообщение в начале очереди, не удаляя его из очереди, вызвав метод **peek\_message**.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Peek at the next message.
	azure::storage::cloud_queue_message peeked_message = queue.peek_message();

	// Output the message content.
	std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;

## Практическое руководство. Изменение содержимого сообщения в очереди
Вы можете изменить содержимое сообщения непосредственно в очереди. Если сообщение представляет собой рабочую задачу, можно использовать эту функцию для обновления состояния рабочей задачи. Следующий код добавляет новое содержимое в очередь сообщений и продлевает время ожидания видимости еще на 60 секунд. Это сохраняет состояние работы, связанной с данным сообщением, и позволяет клиенту продолжить работу с сообщением на протяжении еще одной минуты. Этот метод можно использовать для отслеживания многошаговых рабочих процессов по сообщениям в очереди без необходимости начинать с самого начала в случае сбоя шага обработки в связи с ошибкой аппаратного или программного обеспечения. Обычно также сохраняется счетчик повторов; если количество повторов сообщения превысит n раз, его нужно удалить. Это обеспечивает защиту от сообщений, которые инициируют ошибку приложения при каждой попытке обработки.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Get the message from the queue and update the message contents.
	// The visibility timeout "0" means make it visible immediately.
	// The visibility timeout "60" means the client can get another minute to continue
	// working on the message.
	azure::storage::cloud_queue_message changed_message = queue.get_message();

	changed_message.set_content(U("Changed message"));
	queue.update_message(changed_message, std::chrono::seconds(60), true);

	// Output the message content.
	std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  

## Практическое руководство. Удаление следующего сообщения из очереди
Код удаляет сообщение из очереди в два этапа. При вызове **get\_message** вы получаете следующее сообщение в очереди. Сообщение, возвращаемое методом **get\_message**, становится невидимым для другого кода, считывающего сообщения из этой очереди. Чтобы завершить удаление сообщения из очереди, необходимо также вызвать метод **delete\_message**. Этот двухэтапный процесс удаления сообщения позволяет удостовериться, что если коду не удастся обработать сообщение из-за сбоя оборудования или программного обеспечения, другой экземпляр кода сможет получить то же сообщение и повторить попытку. Код вызывает метод **delete\_message** сразу после обработки сообщения.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Get the next message.
	azure::storage::cloud_queue_message dequeued_message = queue.get_message();
	std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

	// Delete the message.
	queue.delete_message(dequeued_message);

## Дополнительные параметры для удаления сообщений из очереди
Способ извлечения сообщения из очереди можно настроить двумя способами. Во-первых, можно получить пакет сообщений (до 32 сообщений). Во-вторых, можно задать более длительное или короткое время ожидания видимости, чтобы предоставить коду больше или меньше времени на полную обработку каждого сообщения. В следующем примере кода для получения 20 сообщений за один вызов используется метод **get\_messages**. Затем он обрабатывает каждое сообщение с помощью цикла **for**. Он также задает время ожидания невидимости 5 минут для каждого сообщения. Обратите внимание, что пятиминутный период начинается для всех сообщений одновременно, поэтому по прошествии 5 минут с момента вызова **get\_messages** все сообщения, которые не были удалены, снова становятся видимыми.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
	// 5 minutes (300 seconds).
	azure::storage::queue_request_options options;
	azure::storage::operation_context context;

	// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
	std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

	for (auto it = messages.cbegin(); it != messages.cend(); ++it)
	{
		// Display the contents of the message.
		std::wcout << U("Get: ") << it->content_as_string() << std::endl;
	}

## Практическое руководство. Получение длины очереди
Вы можете узнать приблизительное количество сообщений в очереди. Метод **download\_attributes** отправляет в службу очередей запрос на извлечение атрибутов очереди, включая количество сообщений. Метод **Approximate\_message\_count** возвращает приблизительное количество сообщений в очереди.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// Fetch the queue attributes.
	queue.download_attributes();

	// Retrieve the cached approximate message count.
	int cachedMessageCount = queue.approximate_message_count();

	// Display number of messages.
	std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  

## Практическое руководство. Удаление очереди
Для удаления очереди и всех сообщений в ней вызовите метод **delete\_queue\_if\_exists** для объекта очереди.

	// Retrieve storage account from connection-string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the queue client.
	azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

	// Retrieve a reference to a queue.
	azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

	// If the queue exists and delete it.
	queue.delete_queue_if_exists();  

## Дальнейшие действия

Теперь, когда вы ознакомились с основными сведениями о хранилище очередей, используйте следующие ссылки для получения дополнительных сведений о хранилище Azure.

-	[Использование хранилища BLOB-объектов из C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Использование табличного хранилища из C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Перечисление ресурсов хранилища Azure в C++](storage-c-plus-plus-enumeration.md)
-	[Справочник по клиентской библиотеке хранилища для C++](http://azure.github.io/azure-storage-cpp)
-	[Документация по хранилищу Azure](https://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_0921_2016-->