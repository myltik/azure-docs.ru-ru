---
title: "Разработка для файлов Azure с помощью Python | Документы Майкрософт"
description: "Узнайте, как разрабатывать приложения и службы Python, использующие файлы Azure для хранения данных файлов."
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
ms.date: 09/19/2017
ms.author: robinsh
ms.openlocfilehash: 48bfe97c42cad237ad7b395eeeac4e993201848a
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="develop-for-azure-files-with-python"></a>Разработка для файлов Azure с помощью Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>О данном учебнике
В этом руководстве мы рассмотрим основы использования Python для разработки приложений и служб, использующих файлы Azure для хранения данных файлов. В рамках этого руководства мы создадим простое консольное приложение, а также покажем, как выполнять основные действия с Python и файлами Azure:

* создание файловых ресурсов Azure;
* создание каталогов;
* перечисление файлов и каталогов в файловом ресурсе Azure;
* Передача, загрузка и удаление файлов.

> [!Note]  
> Так как к файлам Azure можно обращаться через SMB, вы можете создавать простые приложения, которые получают доступ к общим папкам файлов Azure с использованием стандартных классов ввода-вывода и функций в Python. Из этой статьи вы узнаете, как создавать приложения на основе пакета SDK Python для службы хранилища Azure. Этот пакет SDK использует [API REST файлов Azure](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) для взаимодействия с файлами Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Скачивание и установка пакета SDK службы хранилища Azure для Python

Для установки пакета SDK службы хранилища Azure для Python потребуется Python 2.7, 3.3, 3.4, 3.5 или 3.6. Как правило, этот пакет бывает 4 видов: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` и `azure-storage-queue`. В этом руководстве мы будем использовать пакет `azure-storage-file`.
 
## <a name="install-via-pypi"></a>Установка с помощью PyPI

Для установки с помощью индекса пакетов Python (PyPI) введите:

```bash
pip install azure-storage-file
```


> [!NOTE]
> Если вы обновляете пакет SDK службы хранилища Azure для Python с версии 0.36 или более ранней, сначала удалите эту версию с помощью команды `pip uninstall azure-storage`, так как этот пакет больше не выпускается отдельно.
> 
> 

Другие методы установки см. в статье [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/) (Пакет SDK службы хранилища Microsoft Azure для Python) на сайте Github.

## <a name="set-up-your-application-to-use-azure-files"></a>Настройка приложения для работы со службой файлов Azure
Добавьте следующий код в начало любого исходного файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Настройка подключения к файлам Azure 
Объект `FileService` позволяет работать с общими ресурсами, каталогами и файлами. Следующий код создает объект `FileService`, используя имя и ключ учетной записи хранения. Замените `<myaccount>` и `<mykey>` именем учетной записи и ключом.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Создание файлового ресурса Azure
В следующем примере кода для создания общего ресурса (если он не существует) можно использовать объект `FileService`.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Создайте каталог
Вы также можете организовать хранилище, помещая файлы в подкаталоги вместо их размещения в корневом каталоге. Служба файлов Azure позволяет создать такое количество каталогов, которое допускается в вашей учетной записи. В следующем примере кода в корневом каталоге создается вложенный каталог с именем **sampledir** .

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Перечисление файлов и каталогов в файловом ресурсе Azure
Чтобы получить список файлов и каталогов в общем ресурсе, используйте метод **list\_directories\_and\_files**. Этот метод возвращает генератор. Приведенный далее код выводит в консоль **имя** каждого файла и каталога в общем ресурсе.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Отправить файл. 
Файловый ресурс Azure содержит по крайней мере корневой каталог, где могут размещаться файлы. В этом разделе вы узнаете, как отправить файл из локального хранилища в корневой каталог общего ресурса.

Для создания файла и передачи данных используйте методы `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` или `create_file_from_text`. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

Метод `create_file_from_path` передает содержимое файла из указанного пути, а метод `create_file_from_stream` передает содержимое уже открытого файла или потока. Метод `create_file_from_bytes` передает массив байтов, а `create_file_from_text` — указанное текстовое значение с использованием указанной кодировки (по умолчанию UTF-8).

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

## <a name="download-a-file"></a>Скачивание файла
Чтобы загрузить данные из файла, используйте методы `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` или `get_file_to_text`. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В следующем примере показано использование метода `get_file_to_path` для загрузки содержимого файла **myfile** и его сохранения в файл **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Удаление файла
Наконец, чтобы удалить файл, вызовите метод `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot-preview"></a>Создание моментального снимка общего ресурса (предварительная версия)
Вы можете создать копию на момент времени всего общего файлового ресурса.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Создание моментального снимка общего ресурса с метаданными**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Список общих ресурсов и моментальных снимков 
Вы можете вывести список всех моментальных снимков для определенного общего ресурса.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Просмотр снимков общего ресурса
Вы можете просмотреть содержимое каждого моментального снимка общих ресурсов для извлечения файлов и каталогов с этого момента времени.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Получение файлов из моментальных снимков общих ресурсов
Вы можете скачать файл из моментального снимка общих ресурсов для сценария восстановления.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Удаление одного моментального снимка общих ресурсов  
Вы можете удалить один моментальный снимок общих ресурсов.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Удаление общего ресурса с имеющимся моментальным снимком общих ресурсов
Нельзя удалить общий ресурс, содержащий моментальные снимки, пока все моментальные снимки не удалены.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы узнали, как работать с файлами Azure с помощью Python, воспользуйтесь следующими ссылками для получения дополнительных сведений.

* [Центр по разработке для Python](/develop/python/)
* [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
* [пакет SDK для службы хранилища Microsoft Azure для Python](https://github.com/Azure/azure-storage-python)