<properties
	pageTitle="Использование хранилища больших двоичных объектов из Ruby | Microsoft Azure"
	description="Вы можете хранить неструктурированные данные в облаке в хранилище BLOB-объектов Azure (хранилище объектов)."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
    ms.date="04/29/2016"
	ms.author="robmcm"/>


# Использование хранилища BLOB-объектов из Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием хранилища больших двоичных объектов. Примеры написаны с помощью Ruby API. Здесь описаны такие сценарии, как **отправка, перечисление, загрузка** и **удаление** больших двоичных объектов.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание приложения Ruby

Создайте приложение Ruby. Инструкции см. в разделе [Веб-приложение Ruby on Rails на виртуальной машине Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).

## Настройка приложения для доступа к хранилищу

Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### Использование RubyGems для получения пакета

1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).

2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### Импорт пакета

Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

	require "azure"

## Настройка подключения к службе хранилища Azure

Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS\_KEY**, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать сведения об учетной записи перед использованием **Azure::Blob::BlobService** с помощью следующего кода:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Вот как можно получить эти значения из классический учетной записи хранения или учетной записи хранения ARM на портале Azure:

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. В колонке "Параметры" справа щелкните **Ключи доступа**.
4. В колонке "Ключи доступа" вы увидите ключи доступа 1 и 2. Можно использовать любой из них. 
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена. 

Вот как можно получить эти значения из классический учетной записи хранения на классическом портале:

1. Войдите на [классический портал](https://manage.windowsazure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части области навигации.
4. В открывшемся диалоговом окне вы увидите имя учетной записи хранения, первичный ключ доступа и вторичный ключ доступа. В качестве ключа доступа можно использовать либо первичный, либо вторичный ключ. 
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена.

## Создание контейнера

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Объект **Azure::Blob::BlobService** позволяет работать с контейнерами и BLOB-объектами. Чтобы создать контейнер, используйте метод **create\_container()**.

В следующем примере кода создается контейнер или выводится ошибка, если она возникла.

	azure_blob_service = Azure::Blob::BlobService.new
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

* **blob**: полный общий доступ на чтение данных контейнера и большого двоичного объекта. Клиенты могут перечислять BLOB-объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

* **container:** общий доступ на чтение для больших двоичных объектов. Данные BLOB-объектов в этом контейнере можно считать с помощью анонимного запроса, но данные контейнера недоступны. Клиенты не могут перечислять BLOB-объекты внутри с помощью анонимного запроса.

Кроме того, можно изменить уровень общего доступа к контейнеру, используя метод **set\_container\_acl()** для определения уровня общего доступа.

В следующем примере кода уровень общего доступа изменяется на **container**:

	azure_blob_service.set_container_acl('test-container', "container")

## Отправка BLOB-объекта в контейнер

Для передачи содержимого в BLOB-объект используйте метод **create\_block\_blob()** для создания BLOB-объекта, а в качестве содержимого объекта используйте файл или строку.

Следующий код отправляет файл **test.png** как новый большой двоичный объект с именем "image-blob" в контейнер.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Перечисление BLOB-объектов в контейнере

Для перечисления контейнеров используйте метод **list\_containers()**. Для перечисления больших двоичных объектов в контейнере используйте метод **list\\_blobs()**.

Этот пример выводит URL-адреса всех BLOB-объектов во всех контейнерах учетной записи.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Скачивание больших двоичных объектов

Чтобы загрузить BLOB-объекты, используйте метод **get\_blob()** для извлечения содержимого.

В следующем примере кода показано, как использовать метод **get\_blob()**, чтобы загрузить содержимое "image-blob" и записать его в локальный файл.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Удаление BLOB-объекта
Наконец, для удаления BLOB-объекта используйте метод **delete\_blob()**. В следующем примере кода показано, как удалить большой двоичный объект.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Дальнейшие действия

Дополнительную информацию о выполнении более сложных задач хранения см. по указанным ниже ссылкам.

- [Блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Репозиторий [пакетов SDK Azure для Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub
- [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0511_2016-->