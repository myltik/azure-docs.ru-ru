---
title: "Как использовать хранилище файлов Azure из Python | Документация Майкрософт"
description: "Узнайте, как отправлять, перечислять, скачивать и удалять файлы с помощью хранилища файлов Azure из Python."
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 9973da827ea5a9311904d7d6d4c22d59b5e2d0ce
ms.contentlocale: ru-ru
ms.lasthandoff: 11/17/2016


---
<a id="how-to-use-azure-file-storage-from-python" class="xliff"></a>

# Использование хранилища файлов Azure из Python
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

<a id="overview" class="xliff"></a>

## Обзор
В этой статье описано, как реализовать типичные сценарии использования хранилища файлов. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python]. Здесь описаны такие сценарии, как отправка, перечисление, скачивание и удаление файлов.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

<a id="create-a-share" class="xliff"></a>

## Создание общей папки
Объект **FileService** позволяет работать с общими ресурсами, каталогами и файлами. Приведенный ниже код создает объект **TableService** . Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к службе хранилища Azure программным способом.

```python
from azure.storage.file import FileService
```

Следующий код создает объект **TableService** , используя имя учетной записи хранения и ключ учетной записи.  Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

В следующем примере кода для создания общего ресурса (если он не существует) можно использовать объект **FileService** .

```python
file_service.create_share('myshare')
```

<a id="upload-a-file-into-a-share" class="xliff"></a>

## Отправка файла в общую папку
Общая папка хранилища файлов Azure содержит по крайней мере, корневой каталог, где могут размещаться файлы. В этом разделе вы узнаете, как отправить файл из локального хранилища в корневой каталог общего ресурса.

Чтобы создать файл и отправить данные, используйте методы **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** или **create\_file\_from\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

Метод **create\_file\_from\_path** передает содержимое файла из указанного расположения, а метод **create\_file\_from\_stream** передает содержимое уже открытого файла или потока. Метод **create\_file\_from\_bytes** передает массив байтов, а метод **create\_file\_from\_text** передает заданное текстовое значение, используя указанную кодировку (по умолчанию — UTF-8).

В примере далее содержимое файла **sunset.png** передается в файл **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

<a id="how-to-create-a-directory" class="xliff"></a>

## Как создать каталог
Вы также можете организовать хранилище, помещая файлы в подкаталоги вместо их размещения в корневом каталоге. Служба хранилища файлов Azure позволяет создавать столько каталогов, сколько может позволить ваша учетная запись. В следующем примере кода в корневом каталоге создается вложенный каталог с именем **sampledir** .

```python
file_service.create_directory('myshare', 'sampledir')
```

<a id="how-to-list-files-and-directories-in-a-share" class="xliff"></a>

## Как получить список файлов и каталогов в общей папке
Чтобы получить список файлов и каталогов в общем ресурсе, используйте метод **list\_directories\_and\_files**. Этот метод возвращает генератор. Приведенный далее код выводит в консоль **имя** каждого файла и каталога в общем ресурсе.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

<a id="download-files" class="xliff"></a>

## Скачивание файлов
Чтобы скачать данные из файла, используйте методы **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** или **get\_file\_to\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В примере ниже показано использование метода **get\_file\_to\_path** для скачивания содержимого файла **myfile** и его сохранения в файл **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

<a id="delete-a-file" class="xliff"></a>

## Удаление файла
Наконец, чтобы удалить файл, вызовите метод **delete_file**.

```python
file_service.delete_file('myshare', None, 'myfile')
```

<a id="next-steps" class="xliff"></a>

## Дальнейшие действия
Вы ознакомились с базовыми понятиями о хранилище файлов. Дополнительные сведения см. по следующим ссылкам.

* [Центр по разработке для Python](/develop/python/)
* [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [Блог рабочей группы службы хранилища Azure]
* [пакет SDK для службы хранилища Microsoft Azure для Python]

[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python

