<properties urlDisplayName="Blob Service" pageTitle="Как использовать хранилище больших двоичных объектов (Ruby) - Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/21/2014" ms.author="tomfitz" />





#Использование службы BLOB-объектов в Ruby
 В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны с помощью Ruby API. Здесь описаны такие сценарии, как **отправка, перечисление, скачивание** и **удаление** больших двоичных объектов. Дополнительную информацию о больших двоичных объектах см. в разделе [Дальнейшие действия](#next-steps) .

##Оглавление

* [Что такое служба BLOB-объектов?](#what-is)
* [Основные понятия](#concepts)
* [Создание учетной записи хранения Azure](#CreateAccount)
* [Создание приложения Ruby](#CreateRubyApp)
* [Настройка приложения для доступа к хранилищу](#ConfigAccessStorage)
* [Настройка подключения к службе хранилища Azure](#SetupStorageConnection)
* [Практическое руководство. Создание контейнера](#CreateContainer)
* [Практическое руководство. Отправка большого двоичного объекта в контейнер](#UploadBlob)
* [Практическое руководство. Перечисление больших двоичных объектов в контейнере](#ListBlobs)
* [Практическое руководство. Скачивание больших двоичных объектов](#DownloadBlobs)
* [Практическое руководство. Удаление большого двоичного объекта](#DeleteBlob)
* [Дальнейшие действия](#NextSteps)


[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a id="CreateAccount"></a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="CreateRubyApp"></a>Создание приложения Ruby

Создайте приложение Ruby. Указания 
см. в разделе [Создание приложения Ruby в Azure](/ru-ru/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="ConfigAccessStorage"></a>Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### Использование RubyGems для получения пакета

1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).

2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### Импорт пакета

Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

	require "azure"

## <a id="SetupStorageConnection"></a>Настройка подключения к хранилищу Azure

Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS_KEY**, 
чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать информацию об учетной записи перед использованием **Azure::BlobService** с помощью следующего кода:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Для получения этих значений:

1. Войдите на [портал управления Azure](https://manage.windowsazure.com/).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ** в нижней части области навигации.
4. В открывшемся диалоговом окне вы увидите имя учетной записи хранения, первичный ключ доступа и вторичный ключ доступа. В качестве ключа доступа можно использовать либо первичный, либо вторичный ключ.

## <a id="CreateContainer"></a>Практическое руководство. Создание контейнера

Объект **Azure::BlobService** позволяет работать с контейнерами и большими двоичными объектами. Чтобы создать контейнер, используйте метод **create\_container()**.

В следующем примере создается контейнер или выводится ошибка, если она возникла.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Если вы хотите сделать файлы в контейнере общедоступными, можно установить разрешения контейнера. 

Вы можете просто изменить вызов <strong>create\_container(),</strong> чтобы передавать параметр **:public\_access\_level**:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Допустимые значения параметра **:public\_access\_level**:

* **blob -** полный общий доступ на чтение данных контейнера и большого двоичного объекта. Клиенты могут перечислять BLOB-объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

* **container -** общий доступ на чтение для больших двоичных объектов. Данные BLOB-объектов в этом контейнере можно считать с помощью анонимного запроса, но данные контейнера недоступны. Клиенты не могут перечислять BLOB-объекты внутри с помощью анонимного запроса.

Кроме того, можно изменить уровень общего доступа к контейнеру, используя метод **set\_container\_acl()** для определения уровня общего доступа.
 
В следующем примере уровень общего доступа изменяется на **container**:

	azure_blob_service.set_container_acl('test-container', "container")

## <a id="UploadBlob"></a>Практическое руководство. Отправка BLOB-объекта в контейнер

Чтобы передать содержимое в большой двоичный объект, используйте метод **create\_block\_blob()** для создания большого двоичного объекта, а в качестве содержимого объекта используйте файл или строку. 

Следующий код отправляет файл **test.png** как новый большой двоичный объект image-blob в контейнер.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## <a id="ListBlobs"></a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы перечислить контейнеры, используйте метод **list_containers()**. 
Чтобы перечислить большие двоичные объекты в контейнере, используйте метод **list\_blobs()**. 

Этот пример выводит URL-адреса всех BLOB-объектов во всех контейнерах учетной записи.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## <a id="DownloadBlobs"></a>Практическое руководство. Загрузка BLOB-объектов

Чтобы скачать большие двоичные объекты, используйте метод **get\_blob()** для извлечения содержимого. 

В следующем примере показано, как использовать метод **get\_blob()**, чтобы скачать содержимое image-blob и записать его в локальный файл.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## <a id="DeleteBlob"></a>Практическое руководство. Удаление BLOB-объекта
Наконец, чтобы удалить большой двоичный объект, используйте метод **delete\_blob()**. В следующем примере показано, как удалить BLOB-объект.

	azure_blob_service.delete_blob(container.name, "image-blob")

## <a id="NextSteps"></a>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure](http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx)
-   Посетите блог [группы разработчиков службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Посетите репозиторий [Пакет SDK для Azure для Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub.

<!--HONumber=35_1-->
