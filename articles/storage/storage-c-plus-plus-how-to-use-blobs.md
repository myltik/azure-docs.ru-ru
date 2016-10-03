<properties
    pageTitle="Использование хранилища BLOB-объектов (хранилища объектов) из C++ | Microsoft Azure"
	description="Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
    services="storage"
    documentationCenter=".net"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="dineshm;tamram"/>

# Использование хранилища BLOB-объектов из C++  

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)] .<br/> [AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## Обзор

Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища больших двоичных объектов Azure. Примеры написаны на C++ и используют [клиентскую библиотеку хранилища Azure для C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** больших двоичных объектов.

>[AZURE.NOTE] Данное руководство предназначено для клиентской библиотеки хранилища Azure для С++ версии 1.0.0 и выше. Рекомендуемая версия — клиентская библиотека хранилища 2.2.0, которая доступна на сайте [NuGet](http://www.nuget.org/packages/wastorage) или [GitHub](https://github.com/Azure/azure-storage-cpp).

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения на C++
В этом руководстве будут использоваться компоненты хранилища, которые могут выполняться в приложениях C++.

Для этого необходимо установить клиентскую библиотеку хранилища Azure для C++ и создать учетную запись хранения Azure в подписке Azure.

Чтобы установить клиентскую библиотеку хранилища для C++, можно использовать следующие методы.

-	**Linux:** следуйте инструкциям, указанным в файле README [клиентской библиотеки хранилища Azure для C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
-	**Windows:** в Visual Studio нажмите **Инструменты > Диспетчер пакетов NuGet > Консоль диспетчера пакетов**. Введите следующую команду в [консоли диспетчера пакетов NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) и нажмите клавишу **ВВОД**.

		Install-Package wastorage

## Настройка приложения для доступа к хранилищу BLOB-объектов  
Добавьте следующие инструкции в начало файла C++, где требуется использовать API-интерфейсы Azure для доступа к BLOB-объектам.

	#include "was/storage_account.h"
	#include "was/blob.h"

## Настройка строки подключения к службе хранилища Azure
Клиент хранилища Azure использует строку подключения с целью хранения конечных точек и учетных данных для доступа к службам управления данными. При работе в клиентском приложении необходимо указать для хранилища строку подключения в следующем формате, используя имя своей учетной записи хранения и клавишу доступа для учетной записи хранения, указанные на [портале Azure](https://portal.azure.com) значениями *AccountName* и *AccountKey*. Сведения об учетных записях хранения и ключи доступа см. в разделе [Об учетных записях хранения Azure](storage-create-storage-account.md). В этом примере показано, как объявить статическое поле для размещения строки подключения:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Чтобы протестировать приложение на локальном компьютере Windows, можно использовать [эмулятор хранения](storage-use-emulator.md) Microsoft Azure, установленный с [Azure SDK](https://azure.microsoft.com/downloads/). Эмулятор хранения — это программа, моделирующая службы больших двоичных объектов, очередей и таблиц, доступных в Azure на локальном компьютере разработки. В следующем примере показано, как объявить статическое поле для размещения строки подключения для эмулятора локального хранилища.

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Чтобы запустить эмулятор хранения Azure, нажмите кнопку **Запуск** или клавишу **Windows**. Начните вводить **эмулятор хранения Azure** и выберите **эмулятор хранения Microsoft Azure** из списка приложений.

В приведенных ниже примерах предполагается, что вы использовали одно из этих двух определений для получения строки подключения к хранилищу.

## Получить строку подключения
Информацию о своей учетной записи хранения можно представить с помощью класса **cloud\_storage\_account**. Чтобы получить данные учетной записи хранения из строки подключения хранилища, можно использовать метод **синтаксического анализа**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Затем получите ссылку на класс **cloud\_blob\_client**, позволяющий извлекать объекты, представляющие контейнеры и BLOB-объекты, сохраненные с помощью службы хранилища BLOB-объектов. Следующий код создает объект **cloud\_blob\_client** с помощью объекта учетной записи хранения, полученной выше.

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  

## Практическое руководство. Создание контейнера

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

В этом примере показано, как создать контейнер:

	try
	{
		// Retrieve storage account from connection string.
		azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

		// Create the blob client.
		azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

		// Retrieve a reference to a container.
		azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

		// Create the container if it doesn't already exist.
		container.create_if_not_exists();
	}
	catch (const std::exception& e)
	{
		std::wcout << U("Error: ") << e.what() << std::endl;
	}  

По умолчанию новый контейнер закрытый, и вам необходимо указать ключ доступа к хранилищу, чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы (BLOB) в этом контейнере доступными для всех пользователей, сделайте контейнер открытым, используя следующий код.

	// Make the blob container publicly accessible.
	azure::storage::blob_container_permissions permissions;
	permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
	container.upload_permissions(permissions);  

Любой пользователь в Интернете может видеть большие двоичные объекты в открытом контейнере, но изменить или удалить их можно только при наличии ключа доступа.

## Практическое руководство. Отправка BLOB-объекта в контейнер
Хранилище BLOB-объектов Azure поддерживает блочные и страничные BLOB-объекты. В большинстве случаев рекомендуется использовать блочные BLOB-объекты.

Для передачи файла в блочный BLOB-объект получите ссылку на контейнер и используйте ее для получения ссылки на блочный BLOB-объект. Получив ссылку на BLOB-объект, можно передать любой поток данных в него с помощью метода **upload\_from\_stream**. Эта операция создает большой двоичный объект, если он не существует, или заменяет его, если он существует. В следующем примере показано, как отправить BLOB-объект в контейнер. Предполагается, что контейнер уже был создан.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Create or overwrite the "my-blob-1" blob with contents from a local file.
	concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
	blockBlob.upload_from_stream(input_stream);
	input_stream.close().wait();

	// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
	// Retrieve a reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
	blob2.upload_text(U("more text"));

	// Retrieve a reference to a blob named "my-blob-3".
	azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
	blob3.upload_text(U("other text"));  

Кроме того, можно использовать метод **upload\_from\_file** для отправки файла в блочный BLOB-объект.

## Практическое руководство. Перечисление BLOB-объектов в контейнере
Для перечисления BLOB-объектов в контейнере сначала необходимо получить ссылку на контейнер. Затем вы можете использовать метод контейнера **list\_blobs**, чтобы получить большие двоичные объекты или каталоги внутри них. Для доступа к широкому набору свойств и методов возвращаемого объекта **list\_blob\_item** необходимо вызвать метод **list\_blob\_item.as\_blob**, чтобы получить объект **cloud\_blob**, или метод **list\_blob.as\_directory**, чтобы получить объект cloud\_blob\_directory. В следующем коде показано, как получить и вывести URI каждого элемента в контейнере **my-sample-container**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Output URI of each item.
	azure::storage::list_blob_item_iterator end_of_results;
	for (auto it = container.list_blobs(); it != end_of_results; ++it)
	{
		if (it->is_blob())
		{
			std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
		}
		else
		{
			std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
		}
	}

Дополнительные сведения об операциях перечисления см. в разделе [Перечисление ресурсов хранилища Azure в C++](storage-c-plus-plus-enumeration.md).

## Практическое руководство. Загрузка BLOB-объектов
Для загрузки BLOB-объектов сначала нужно получить ссылку на BLOB-объект и затем вызвать метод **download\_to\_stream**. В следующем примере используется метод **download\_to\_stream** для переноса содержимого BLOB-объекта в объект потока, который затем можно сохранить в локальном файле.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Save blob contents to a file.
	concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
	concurrency::streams::ostream output_stream(buffer);
	blockBlob.download_to_stream(output_stream);

	std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
	std::vector<unsigned char>& data = buffer.collection();

	outfile.write((char *)&data[0], buffer.size());
	outfile.close();  

Кроме того, можно использовать метод **download\_to\_file**, чтобы загрузить содержимое BLOB-объекта в файл. Можно также использовать метод **download\_text**, чтобы загрузить содержимое BLOB-объекта как текстовую строку.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-2".
	azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

	// Download the contents of a blog as a text string.
	utility::string_t text = text_blob.download_text();

## Практическое руководство. Удаление BLOB-объектов
Чтобы удалить большой двоичный объект, сначала нужно получить ссылку на него, а затем вызвать для него метод **delete\_blob**.

	// Retrieve storage account from connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the blob client.
	azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

	// Retrieve a reference to a previously created container.
	azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

	// Retrieve reference to a blob named "my-blob-1".
	azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

	// Delete the blob.
	blockBlob.delete_blob();

## Дальнейшие действия
Теперь, когда вы ознакомились с основными сведениями о хранилище BLOB-объектов, используйте следующие ссылки для получения дополнительных сведений о хранилище Azure.

-	[Использование хранилища очередей из C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Использование табличного хранилища из C++](storage-c-plus-plus-how-to-use-tables.md)
-	[Перечисление ресурсов хранилища Azure в C++](storage-c-plus-plus-enumeration.md)
-	[Справочник по клиентской библиотеке хранилища для C++](http://azure.github.io/azure-storage-cpp)
-	[Документация по хранилищу Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0921_2016-->