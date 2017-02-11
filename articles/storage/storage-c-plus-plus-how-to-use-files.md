---
title: "Использование хранилища файлов из C++ | Документация Майкрософт"
description: "Хранение файловых данных в облаке в хранилище файлов Azure."
services: storage
documentationcenter: .net
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: a1e8c99e-47a6-43a9-9541-c9262eb00b38
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: bc97472a07ac4c27c60fbe2cb803f2360a3362c4
ms.openlocfilehash: 7faa219c7c21c768419f6c5e98712a0f0f471924


---
# <a name="how-to-use-file-storage-from-c"></a>Использование хранилища файлов из C++
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

[!INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

## <a name="about-this-tutorial"></a>О данном учебнике
В этом руководстве вы узнаете, как выполнять основные операции в службе хранилища файлов Microsoft Azure. С помощью примеров на языке C++ вы узнаете, как создавать общие папки и каталоги, отправлять и удалять файлы, а также получать список файлов. Если вы не знакомы с хранилищем файлов Microsoft Azure, рекомендуем изучить информацию, изложенную в последующих разделах. Это будет очень полезно для понимания примеров программ.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Создание приложения на C++
Для создания примеров необходимо установить клиентскую библиотеку хранилища Azure версии 2.4.0 для C++. Вам также необходимо создать учетную запись хранилища Azure.

Чтобы установить клиентскую библиотеку хранилища Azure версии 2.4.0 для C++, можно использовать следующие методы:

* **Linux:** следуйте инструкциям, указанным в файле README [клиентской библиотеки хранилища Azure для C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
* **Windows.** В Visual Studio щелкните **Инструменты &gt; Диспетчер пакетов NuGet ** Консоль диспетчера пакетов&gt;. Введите следующую команду в [консоли диспетчера пакетов NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) и нажмите клавишу **ВВОД**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-file-storage"></a>Настройка приложения для использования хранилища файлов
Добавьте следующие инструкции в начало файла C++, где требуется использовать API-интерфейсы Azure для доступа к файлам:

```cpp
#include "was/storage_account.h"
#include "was/file.h"
```

## <a name="set-up-an-azure-storage-connection-string"></a>Настройка строки подключения к хранилищу Azure
Чтобы начать работу с хранилищем файлов, необходимо подключиться к вашей учетной записи хранилища Azure. Для начала потребуется настроить строку подключения, которое будет использоваться для подключения к учетной записи хранилища. Для этого определим статическую переменную.

```cpp
// Define the connection-string with your values.
const utility::string_t 
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Подключение к учетной записи хранилища Azure
Информацию о своей учетной записи хранения можно представить с помощью класса **cloud_storage_account**. Чтобы получить данные учетной записи хранения из строки подключения хранилища, можно использовать метод **синтаксического анализа** .

```cpp
// Retrieve storage account from connection string.    
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-share"></a>Как создать общую папку
Все файлы и каталоги в хранилище файлов находятся в контейнере с именем **Share**. Учетная запись хранения может иметь столько общих папок, насколько позволяет емкость вашей учетной записи. Чтобы получить доступ к общей папке и ее содержимому, необходимо использовать клиент хранилища файлов.

```cpp
// Create the file storage client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();
```

С помощью клиента хранилища файлов можно затем получить ссылку на общий ресурс.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Чтобы создать общий ресурс, используйте метод **create_if_not_exists** объекта **cloud_file_share**.

```cpp
if (share.create_if_not_exists()) {    
    std::wcout << U("New share created") << std::endl;    
}
```

На этом этапе объект **share** содержит ссылку на общую папку с именем **my-sample-share**.

## <a name="how-to-upload-a-file"></a>Как отправить файл
Общая папка хранилища файлов Azure содержит по крайней мере корневой каталог, где могут размещаться файлы. В этом разделе вы узнаете, как отправить файл из локального хранилища в корневой каталог общего ресурса.

Первым шагом при отправке файла является получение ссылки на каталог, где файл будет находиться. Для этого нужно вызвать метод **getRootDirectoryReference** объекта общей папки.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Теперь, когда у вас имеется ссылка на корневой каталог общего ресурса, вы можете отправить туда файл. В этом примере выполняется отправка из файла, текста и потока.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream = 
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));    
```

## <a name="how-to-create-a-directory"></a>Как создать каталог
Вы также можете организовать хранилище, помещая файлы в подкаталоги, а не размещая их в корневом каталоге. Служба хранилища файлов Azure позволяет создавать столько каталогов, сколько может позволить ваша учетная запись. В следующем примере кода создается каталог с именем **my-sample-directory** в корневом каталоге, а также подкаталог **my-sample-subdirectory**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory = 
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>Как получить список файлов и каталогов в общей папке
Получить список файлов и каталогов в общей папке довольно просто, вызвав метод **list_files_and_directorie** по ссылке **cloud_file_directory**. Для доступа к широкому набору свойств и методов возвращаемого объекта **list_file_and_directory_item** необходимо вызвать метод **list_file_and_directory_item.as_file**, чтобы получить объект **cloud_file**, или метод **list_file_and_directory_item.as_directory**, чтобы получить объект **cloud_file_directory**.

Следующий код демонстрирует, как получить и вывести URI каждого элемента в корневом каталоге общей папки.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_diretory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }        
}
```

## <a name="how-to-download-a-file"></a>Как скачать файл
Чтобы скачать файлы, сначала получите ссылку на файл, а затем вызовите метод **download_to_stream** для передачи содержимого файла в объект потока, который затем можно сохранить в локальном файле. Кроме того, можно использовать метод **download_to_file**, чтобы скачать содержимое файла в локальный файл. Вы можете также использовать метод **download_text**, чтобы скачать содержимое файла в виде текстовой строки.

В следующем примере используются методы **download_to_stream** и **download_text** для демонстрации скачивания файлов, созданных в предыдущих разделах.

```cpp
// Download as text
azure::storage::cloud_file text_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="how-to-delete-a-file"></a>Как удалить файл
Другой распространенной операцией над хранилищем файлов является удаление файлов. Следующий код удаляет файл с именем my-sample-file-3, который хранится в корневом каталоге.

```cpp
// Get a reference to the root directory for the share.    
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir = 
  share.get_root_directory_reference();

azure::storage::cloud_file file = 
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="how-to-delete-a-directory"></a>Как удалить каталог
Удаление каталога является простой задачей, однако следует отметить, что нельзя удалить каталог, по-прежнему содержащий файлы или другие каталоги.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory = 
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="how-to-delete-a-share"></a>Как удалить общую папку
Удалить общую папку можно путем вызова метода **delete_if_exists** объекта cloud_file_share. Ниже приведен пример кода, который выполняет это действие.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="set-the-maximum-size-for-a-file-share"></a>Установка максимального размера для файлового ресурса
Для файлового ресурса можно установить квоту (или максимальный размер) в гигабайтах. Можно также проверить, какой объем данных хранится в настоящее время в общей папке.

Задав квоту для файлового ресурса, можно ограничить общий размер файлов, хранящихся в общей папке. Если общий размер файлов в файловом ресурсе превышает установленную квоту, клиенты не смогут увеличить размер существующих файлов или создать новые файлы, только если они не являются пустыми.

В приведенном ниже примере показано, как проверить текущее использование данных в файловом ресурсе, а также задать для него квоту.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Создание подписи общего доступа для файла или файлового ресурса
Для файлового ресурса или отдельного файла можно создать подписанный URL-адрес (SAS). Также можно создать политики общего доступа на файловом ресурсе, чтобы управлять подписями общего доступа. Создание политики общего доступа рекомендуется, так как она позволяет отменить SAS, если она скомпрометирована.

В приведенном ниже примере создаются политики общего доступа в общей папке, а затем используются для обеспечения ограничения SAS для файла в общей папке.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account = 
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client = 
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share = 
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy = 
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() + 
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;    

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir = 
        share.get_root_directory_reference();
    azure::storage::cloud_file file = 
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share 
    //  and associate this access policy with it.        
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.        
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");        
    file_with_sas.upload_text(text);        

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();        
    ucout << downloaded_text << std::endl;        
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

Дополнительные сведения о подписанных URL-адресах см. в статье [Использование подписанных URL-адресов (SAS)](storage-dotnet-shared-access-signature-part-1.md).

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительных сведений о службе хранилища Azure изучите следующие ресурсы:

* [Клиентская библиотека хранилища для C++](https://github.com/Azure/azure-storage-cpp)
* [Azure Storage Explorer;](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Документация по хранилищу Azure](https://azure.microsoft.com/documentation/services/storage/)




<!--HONumber=Feb17_HO2-->


