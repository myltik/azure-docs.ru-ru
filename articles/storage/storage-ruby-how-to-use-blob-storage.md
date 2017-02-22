---
title: "Использование хранилища BLOB-объектов (хранилища объектов) из Ruby | Документация Майкрософт"
description: "Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
services: storage
documentationcenter: ruby
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: c63758346ebd8fa3d4c43e59c84bc2a6935e4bf7
ms.openlocfilehash: 7f7d0c52b2b50a360711477e8e0eafc07ddcf374


---
# <a name="how-to-use-blob-storage-from-ruby"></a>Использование хранилища BLOB-объектов из Ruby
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Обзор
Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этом руководстве показано, как реализовать типичные сценарии с использованием хранилища больших двоичных объектов. Примеры написаны с помощью Ruby API. Здесь описаны такие сценарии, как **отправка, перечисление, скачивание** и **удаление** BLOB-объектов.

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Создание приложения Ruby
Создайте приложение Ruby. Инструкции см. в статье [Веб-приложение Ruby on Rails на виртуальной машине Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Настройка приложения для доступа к хранилищу
Для использования хранилища Azure необходимо загрузить и использовать пакет Ruby Azure, который содержит набор библиотек, взаимодействующих со службами REST хранилища.

### <a name="use-rubygems-to-obtain-the-package"></a>Использование RubyGems для получения пакета
1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).
2. Введите "gem install azure" в окне командной строки, чтобы установить пакеты и зависимости.

### <a name="import-the-package"></a>Импорт пакета
Используйте свой любимый текстовый редактор, чтобы добавить следующий код в начало файла Ruby, где планируется использовать хранилище.

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Настройка подключения к службе хранилища Azure
Модуль Azure считывает переменные среды **AZURE\_STORAGE\_ACCOUNT** и **AZURE\_STORAGE\_ACCESS_KEY**, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать сведения об учетной записи перед использованием **Azure::Blob::BlobService** с помощью следующего кода:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Вот как можно получить эти значения из классический учетной записи хранения или учетной записи хранения Resource Manager на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. В колонке "Параметры" справа щелкните **Ключи доступа**.
4. В колонке "Ключи доступа" вы увидите ключи доступа 1 и 2. Можно использовать любой из них.
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена.

Вот как можно получить эти значения из классический учетной записи хранения на классическом портале Azure.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. Щелкните **УПРАВЛЕНИЕ КЛЮЧАМИ ДОСТУПА** в нижней части области навигации.
4. Во всплывающем диалоговом окне вы увидите имя учетной записи хранения, первичный ключ доступа и вторичный ключ доступа. В качестве ключа доступа можно использовать либо первичный, либо вторичный ключ.
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена.

## <a name="create-a-container"></a>Создание контейнера
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Объект **Azure::Blob::BlobService** позволяет работать с контейнерами и BLOB-объектами. Чтобы создать контейнер, используйте метод **create\_container()**.

В следующем примере кода создается контейнер или выводится ошибка, если она возникла.

```ruby
azure_blob_service = Azure::Blob::BlobService.new
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

Если вы хотите сделать файлы в контейнере общедоступными, можно установить разрешения контейнера.

Вы можете просто изменить вызов <strong>create\_container()</strong> и передать параметр **:public\_access\_level**:

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

Параметр **:public\_access\_level** принимает такие значения:

* **blob** — это общий доступ на чтение для больших двоичных объектов. Данные BLOB-объектов в этом контейнере можно считать с помощью анонимного запроса, но данные контейнера недоступны. Клиенты не могут перечислять BLOB-объекты внутри с помощью анонимного запроса.
* **container** — это полный общий доступ на чтение данных контейнера и большого двоичного объекта. Клиенты могут перечислять BLOB-объекты внутри контейнера с помощью анонимного запроса, но не могут перечислять контейнеры в учетной записи хранения.

Кроме того, можно изменить уровень общего доступа к контейнеру, определив уровень общего доступа в методе **set\_container\_acl()**.

В следующем примере кода уровень общего доступа изменяется на **container**:

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>Отправка BLOB-объекта в контейнер
Чтобы передать содержимое в BLOB-объект, создайте этот объект с помощью метода **create\_block\_blob()**, а в качестве содержимого объекта используйте файл или строку.

Следующий код отправляет файл **test.png** как новый большой двоичный объект с именем "image-blob" в контейнер.

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере
Для перечисления контейнеров используйте метод **list_containers()**.
Для перечисления BLOB-объектов в контейнере используйте метод **list\_blobs()**.

Этот пример выводит URL-адреса всех BLOB-объектов во всех контейнерах учетной записи.

```ruby
containers = azure_blob_service.list_containers()
containers.each do |container|
    blobs = azure_blob_service.list_blobs(container.name)
    blobs.each do |blob|
    puts blob.name
    end
end
```

## <a name="download-blobs"></a>Скачивание больших двоичных объектов
Для скачивания BLOB-объектов (получения их содержимого) используйте метод **get\_blob()**.

В следующем примере кода показано, как с помощью метода **get\_blob()** скачать содержимое объекта image-blob и записать его в локальный файл.

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>Удаление BLOB-объекта
Для удаления BLOB-объекта используйте метод **delete\_blob()**. В следующем примере кода показано, как удалить большой двоичный объект.

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>Дальнейшие действия
Дополнительную информацию о выполнении более сложных задач хранения см. по указанным ниже ссылкам.

* [Блог рабочей группы службы хранилища Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [пакетов SDK Azure для Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) на веб-сайте GitHub
* [Приступая к работе со служебной программой командной строки AzCopy](storage-use-azcopy.md)




<!--HONumber=Dec16_HO4-->


