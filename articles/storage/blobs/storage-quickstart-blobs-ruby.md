---
title: "Краткое руководство по Azure. Передача объектов в хранилище BLOB-объектов Azure и из него с помощью Ruby | Документация Майкрософт"
description: "Краткое руководство по передаче объектов в хранилище BLOB-объектов Azure и из него с помощью Ruby"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 12/7/2017
ms.author: v-ruogun
ms.openlocfilehash: 30cda1997ca93f237dafd34cfffc2a86b86d6c7c
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-ruby"></a>Передача объектов в хранилище BLOB-объектов Azure и из него с помощью Ruby
Из этого краткого руководства вы узнаете, как использовать Ruby для передачи, скачивания и перечисления блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure. 

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством сделайте следующее: 
* Установите [Ruby](https://www.ruby-lang.org/en/downloads/).
* Установите [библиотеку службы хранилища Azure для Ruby](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage) с помощью пакета rubygem. 

```
gem install azure-storage
```

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения
[Пример приложения](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git), используемый в этом кратком руководстве, представляет собой простое приложение Ruby.  

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть пример приложения Ruby, найдите папку storage-blobs-ruby-quickstart и откройте файл example.rb.  

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища
В приложении нужно указать имя и ключ учетной записи хранения, чтобы создать экземпляр `Client` для вашего приложения. Откройте файл `example.rb` с помощью обозревателя решений в IDE. Замените строки **accountname** и **accountkey** фактическими значениями имени и ключа учетной записи. 

```ruby 
client = Azure::Storage.client(storage_account_name: account_name, storage_access_key: account_key)
```

## <a name="run-the-sample"></a>Запуск примера
Этот пример создает тестовый файл в папке "Документы". Пример программы отправляет тестовый файл в хранилище BLOB-объектов, выводит списокBLOB-объектов в контейнере и скачивает файл с новым именем. 

Запустите пример. Ниже представлен пример выходных данных, которые возвращаются при выполнении этого приложения.
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Теперь нажмите любую клавишу, чтобы пример программы удалил контейнер хранилища и файлы. Прежде чем продолжить, проверьте наличие двух файлов в папке "Документы". Вы можете открыть их и убедиться, что они идентичны.

Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](http://storageexplorer.com). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения. 

После проверки файлов нажмите любую клавишу для завершения демонстрации и удаления тестовых файлов. Теперь, когда известны функциональные возможности примера, откройте файл example.rb, чтобы просмотреть код. 

## <a name="understand-the-sample-code"></a>Разбор примера кода

Разберем пример кода, чтобы понять, как он работает.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища
Сначала необходимо создать ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга, и каждый из них используется следующим в списке объектом.

* Создайте экземпляр объекта **Client** службы хранилища Azure, чтобы настроить учетные данные для подключения. 
* Создайте объект **BlobService**, указывающий на службу BLOB-объектов в вашей учетной записи хранения. 
* Создайте объект **Container**, представляющий контейнер, к которому осуществляется доступ. Контейнеры используются для организации BLOB-объектов аналогично папкам для упорядочения файлов на компьютере.

Теперь у вас есть облачный контейнер BLOB-объектов. Вы можете создать **блочный** BLOB-объект, который указывает на конкретный интересующий вас BLOB-объект, и выполнять с ним различные операции: отправку, скачивание или копирование.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

В этом разделе вы настроите экземпляр клиента службы хранилища Azure, создадите экземпляр объекта службы BLOB-объектов и контейнер, а затем зададите для контейнера разрешения на общий доступ к BLOB-объектам. Контейнер называется **quickstartblobs**. 

```ruby 
# Setup a specific instance of an Azure::Storage::Client
client = Azure::Storage.client(storage_account_name: account_name, storage_access_key: account_key)

# Create the BlobService that represents the Blob service for the storage account
blob_service = client.blob_client

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
container = blob_service.create_container(container_name)   

# Set the permission so the blobs are public.
blob_service.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Чаще всего используются блочные BLOB-объекты. Именно этот тип представлен в этом кратком руководстве.  

Чтобы отправить файл в BLOB-объект, получите полный путь к файлу, соединив имя каталога и имя файла на локальном диске. Затем вы можете передать файл по указанному пути, используя метод **create\_block\_blob()**. 

Этот пример кода создает локальный файл, который будет использоваться для отправки и скачивания. Он сохраняет отправляемый файл в переменной **file\_path\_to\_file**, а имя BLOB-объекта — в переменной **local\_file\_name**. В приведенном ниже примере файл отправляется в контейнер с именем **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_service.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Чтобы выполнить частичное обновление содержимого блочного BLOB-объекта, используйте метод **create\_block\_list()**. Блочные BLOB-объекты могут иметь размер 4,7 ТБ и представлять собой любые объекты, начиная от электронных таблиц Excel до больших видеофайлов. Страничные BLOB-объекты в основном используются для файлов виртуального жесткого диска, применяемых для поддержки виртуальных машин IaaS. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Добавочные BLOB-объекты должны использоваться в модели с одним средством записи. Большинство объектов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Список файлов в контейнере можно получить с помощью метода **list\_blobs()**. Следующий код извлекает список BLOB-объектов и поочередно перебирает их, отображая имена найденных в контейнере BLOB-объектов.  

```ruby
# List the blobs in the container
puts "\n List blobs in the container"
blobs = blob_service.list_blobs(container_name)
blobs.each do |blob|
    puts "\t Blob name #{blob.name}"   
end  
```

### <a name="download-the-blobs"></a>Скачивание больших двоичных объектов

Чтобы скачать BLOB-объекты на локальный диск, используйте метод **get\_blob()**. Следующий код скачивает BLOB-объект, который мы передали в предыдущем разделе. К имени BLOB-объекта добавляется суффикс _DOWNLOADED, чтобы на локальном диске сохранились оба файла. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_service.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Очистка ресурсов
Если вам больше не нужны BLOB-объекты, отправленные при работе с этим руководством, удалите весь контейнер с помощью метода **delete\_container()**. Если вам больше не нужны созданные файлы, удалите их с помощью метода **delete\_blob()**.

```ruby
# Clean up resources. This includes the container and the temp files
blob_service.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```

## <a name="next-steps"></a>Дальнейшие действия
 
Из этого краткого руководства вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Ruby. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](./storage-ruby-how-to-use-blob-storage.md)


Дополнительные сведения об обозревателе объектов и BLOB-объектах см. в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
