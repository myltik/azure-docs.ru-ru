---
title: "Как использовать хранилище BLOB-объектов Azure (хранилище объектов) из Python | Документация Майкрософт"
description: "Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 0348e360-b24d-41fa-bb12-b8f18990d8bc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 2/24/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1cab8407be6fc8932b68e50d0c301e8ea37ea3ac
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---
# <a name="how-to-use-azure-blob-storage-from-python"></a>Использование хранилища больших двоичных объектов Azure из Python
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Обзор
Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этой статье описано, как реализовать типичные сценарии с использованием хранилища больших двоичных объектов. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python]. Здесь описаны такие сценарии, как отправка, перечисление, скачивание и удаление больших двоичных объектов.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-container"></a>Создание контейнера
В зависимости от требуемого типа BLOB-объекта создайте объект **BlockBlobService**, **AppendBlobService** или **PageBlobService**. В следующем коде используется объект **BlockBlobService** . Добавьте следующий код в начало любого файла Python, из которого планируется получать программный доступ к хранилищу блочных BLOB-объектов Azure.

```python
from azure.storage.blob import BlockBlobService
```

Следующий код создает объект **BlockBlobService** , используя имя и ключ учетной записи хранения.  Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

```python
block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')
```

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

В следующем примере кода для создания контейнера (если он не существует) можно использовать объект **BlockBlobService** .

```python
block_blob_service.create_container('mycontainer')
```

По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы скачать большие двоичные объекты из этого контейнера. Чтобы сделать большие двоичные объекты в этом контейнере доступными для всех пользователей, вы можете создать контейнер и предоставить открытый доступ, используя следующий код.

```python
from azure.storage.blob import PublicAccess
block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)
```

Либо можно изменить контейнер после его создания, используя следующий код.

```python
block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)
```

После этого изменения любой пользователь в Интернете сможет видеть большие двоичные объекты в общедоступном контейнере, но изменить или удалить их сможете только вы.

## <a name="upload-a-blob-into-a-container"></a>Отправка BLOB-объекта в контейнер
Чтобы создать блочный BLOB-объект и передать данные, используйте метод **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** или **create\_blob\_from\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

Метод **create\_blob\_from\_path** передает содержимое файла из указанного расположения, а метод **create\_blob\_from\_stream** передает содержимое уже открытого файла или потока. Метод **create\_blob\_from\_bytes** передает массив байтов, а метод **create\_blob\_from\_text** передает заданное текстовое значение, используя указанную кодировку (по умолчанию — UTF-8).

В следующем примере содержимое файла **sunset.png** передается в BLOB-объект **myblob**.

```python
from azure.storage.blob import ContentSettings
block_blob_service.create_blob_from_path(
    'mycontainer',
    'myblockblob',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png')
            )
```

## <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере
Чтобы перечислить BLOB-объекты в контейнере, используйте метод **list\_blobs**. Этот метод возвращает генератор. Следующий код выводит на консоль **имя** каждого большого двоичного объекта в контейнере.

```python
generator = block_blob_service.list_blobs('mycontainer')
for blob in generator:
    print(blob.name)
```

## <a name="download-blobs"></a>Скачивание больших двоичных объектов
Чтобы скачать данные из BLOB-объекта, используйте **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** или **get\_blob\_to\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В примере ниже показано использование метода **get\_blob\_to\_path** для скачивания содержимого большого двоичного объекта **myblob** и его сохранения в файл **out-sunset.png**.

```python
block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')
```

## <a name="delete-a-blob"></a>Удаление большого двоичного объекта
Наконец, чтобы удалить BLOB-объект, вызовите **delete_blob**.

```python
block_blob_service.delete_blob('mycontainer', 'myblockblob')
```

## <a name="writing-to-an-append-blob"></a>Запись в расширенный большой двоичный объект
Добавочный большой двоичный объект оптимизирован для операций добавления, например ведения журналов. Как и блочный BLOB-объект, добавочный большой двоичный объект состоит из блоков, но при добавлении нового блока в добавочный большой двоичный объект он всегда добавляется в конец этого объекта. Вы не можете обновить или удалить существующий блок в добавочном большом двоичном объекте. Идентификаторы блоков в добавочном большом двоичном объекте не отображаются, как в блочном BLOB-объекте.

Каждый блок в добавочном большом двоичном объекте может иметь разный размер (не более 4 МБ), кроме того, добавочный большой двоичный объект может содержать не более 50 000 блоков. Таким образом, максимальный размер добавочного большого двоичного объекта немного превышает 195 ГБ (4 МБ X 50 000 блоков).

Приведенный ниже пример создает новый добавочный большой двоичный объект и добавляет в него некоторые данные, имитируя простые операции ведение журнала.

```python
from azure.storage.blob import AppendBlobService
append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

# The same containers can hold all types of blobs
append_blob_service.create_container('mycontainer')

# Append blobs must be created before they are appended to
append_blob_service.create_blob('mycontainer', 'myappendblob')
append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')
```

## <a name="next-steps"></a>Дальнейшие действия
Вы ознакомились с базовыми понятиями о хранилище BLOB-объектов. Дополнительные сведения см. по следующим ссылкам.

* [Центр по разработке для Python](https://azure.microsoft.com/develop/python/)
* [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Блог рабочей группы службы хранилища Azure]
* [пакет SDK для службы хранилища Microsoft Azure для Python]

[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python

