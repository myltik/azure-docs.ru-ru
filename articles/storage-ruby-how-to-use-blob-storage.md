<properties 
	pageTitle="Использование хранилища BLOB-объектов из Ruby в Microsoft Azure" 
	description="Узнайте, как использовать службу BLOB-объектов Azure для отправки, загрузки, отображения и удаления содержимого больших двоичных объектов. Примеры кода написаны на Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Использование хранилища BLOB-объектов из Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы BLOB-объектов Azure. Примеры написаны с помощью Ruby API.
Здесь описаны такие сценарии, как **отправка, перечисление, скачивание** и **удаление** больших двоичных объектов.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Создание приложения Ruby

Создайте приложение Ruby. Указания 
см. в разделе [Создание приложения Ruby в Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### Использование RubyGems для получения пакета

1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).

2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### Импорт пакета

Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

	требуется модуль Azure

## Настройка подключения к хранилищу Azure

Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS_KEY**, 
чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать сведения об учетной записи перед использованием **Azure::BlobService** с помощью следующего кода:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Для получения этих значений:

1. Выполните вход на [портал управления Azure](https://manage.windowsazure.com/).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ** в нижней части области навигации.
4. В открывшемся диалоговом окне вы увидите имя учетной записи хранения, первичный ключ доступа и вторичный ключ доступа. В качестве ключа доступа можно использовать либо первичный, либо вторичный ключ.

## Практическое руководство. Создание контейнера

Объект **Azure::BlobService** позволяет работать с контейнерами и BLOB-объектами. Чтобы создать контейнер, используйте метод **create\_container()**.

В следующем примере создается контейнер или выводится ошибка, если она возникла.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Если вы хотите сделать файлы в контейнере общедоступными, можно установить разрешения контейнера. 

Вы можете просто изменить вызов <strong>create\_container()</strong>, чтобы передавать параметр **:public\_access\_level**:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


Допустимые значения параметра **:public\_access\_level**:

* **blob** - полный общий доступ на чтение данных контейнера и большого двоичного объекта. Клиенты могут перечислять большие двоичные объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

* **container** - общий доступ на чтение для больших двоичных объектов. Данные больших двоичных объектов в этом контейнере можно считывать с помощью анонимного запроса, но данные контейнера недоступны. Клиенты не могут перечислять большие двоичные объекты внутри контейнера с помощью анонимного запроса.

Кроме того, можно изменить уровень общего доступа к контейнеру, используя метод **set\_container\_acl()** для определения уровня общего доступа.
 
В следующем примере уровень общего доступа изменяется на **container**:

	azure_blob_service.set_container_acl('test-container', "container")

## Практическое руководство. Отправка BLOB-объекта в контейнер

Для передачи содержимого в BLOB-объект используйте метод **create\_block\_blob()** для создания BLOB-объекта, а в качестве содержимого объекта используйте файл или строку. 

Следующий код отправляет файл **test.png** как новый BLOB-объект с именем "image-blob" в контейнер.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Практическое руководство. Перечисление BLOB-объектов в контейнере

Для перечисления контейнеров используйте метод **list_containers()**. 
Для перечисления BLOB-объектов в контейнере используйте метод **list\_blobs()**. 

Этот пример выводит URL-адреса всех BLOB-объектов во всех контейнерах учетной записи.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Практическое руководство. Загрузка BLOB-объектов

Чтобы загрузить BLOB-объекты, используйте метод **get\_blob()** для извлечения содержимого. 

В следующем примере показано, как использовать метод **get\_blob()**, чтобы загрузить содержимое "image-blob" и записать его в локальный файл.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Практическое руководство. Удаление BLOB-объекта
Наконец, для удаления BLOB-объекта используйте метод **delete\_blob()**. В следующем примере показано, как удалить BLOB-объект.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

- См. справочник MSDN: [Хранилище Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Посетите [блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Обратитесь к репозиторию [Azure SDK для Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) (Пакет SDK Azure для Ruby) на веб-сайте GitHub.

<!--HONumber=49-->