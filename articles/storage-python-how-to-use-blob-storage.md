<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Использование службы BLOB-объектов в Python

В этом руководстве показано, как реализовать типичные сценарии с использованием службы BLOB-объектов Azure. Примеры написаны с помощью Python API. Здесь описаны такие сценарии, как **отправка**, **перечисление**, **загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о  BLOB-объектах см. в разделе [Дальнейшие действия][].

## Оглавление

[Что такое хранилище BLOB-объектов?][]
 [Основные понятия][]
 [Создание учетной записи хранения Azure][]
 [Практическое руководство: Создание контейнера][]
 [Практическое руководство: Отправка Blob-объекта в контейнер][]
 [Практическое руководство: Перечисление BLOB-объектов в контейнере][]
 [Практическое руководство: Загрузка BLOB-объектов][]
 [Практическое руководство: Удаление BLOB-объекта][]
 [Практическое руководство: Отправка и загрузка больших BLOB-объектов][]
 [Дальнейшие действия][]

[WACOM.INCLUDE [руководство-блоб-объект-хранилище][]]

## <a name="create-account"> </a> Создание учетной записи хранения Azure

[WACOM.INCLUDE [создание-хранилище-учетная запись][]]

## <a name="create-container"> </a>Практическое руководство. Создание контейнера

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [Руководство по установке Python][].

Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Следующий код создает объект **BlobService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

    from azure.storage import *

Следующий код создает объект **BlobService**, используя имя и ключ доступа учетной записи. Замените "myaccount" и "mykey" на фактические значения учетной записи и ключа.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Все BLOB-объекты хранилища содержатся в контейнере. Объект **BlobService** можно использовать для создания контейнера, если контейнер не существует:

    blob_service.create_container('mycontainer')

По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, можно создать контейнер и предоставить открытый доступ, используя следующий код:

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Либо можно изменить контейнер после его создания, используя следующий код:

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

После этого изменения любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнере, но изменить или удалить их можете только вы.

## <a name="upload-blob"> </a>Практическое руководство. Отправка BLOB-объекта в контейнер

Чтобы отправить данные в blob-объект, используйте методы **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** или **put\_block\_blob\_from\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

**put\_block\_blob\_from\_path** отправляет содержимое файла из заданного пути, **put\_block\_blob\_from\_file** отправляет содержимое уже уже открытого файла или потока. **put\_block\_blob\_from\_bytes** отправляет массив байтов, **put\_block\_blob\_from\_text** отправляет заданное значение текста с использованием определенного шифрования (по умолчанию UTF-8).

В следующем примере содержимое файла **test1.txt** передается в BLOB-объект **myblob**.

    blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы получить список BLOB-объектов в контейнере, используйте метод **list\_blobs** в цикле **for** для отображения имени каждого BLOB-объекта в контейнере. Следующий код выводит на консоль **имя** и **url-адрес** каждого blob-объекта в контейнере
.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

## <a name="download-blobs"> </a>Практическое руководство. Загрузка BLOB-объектов

Чтобы загрузить данные из blob-объекта, используйте **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** или **get\_blob\_to\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В следующем пример показано использование **get\_blob\_to\_path** для загрузки содержимого blob-объекта **myblob** и его сохранения в файле **out-task1.txt**:

    blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>Практическое руководство. Удаление BLOB-объекта

Наконец, чтобы удалить BLOB-объект, вызовите **deleteBlob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a> Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите [блог команды разработчиков хранилища Azure][].

  [Дальнейшие действия]: #next-steps
  [Что такое хранилище BLOB-объектов?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Практическое руководство: Создание контейнера]: #create-container
  [Практическое руководство: Отправка Blob-объекта в контейнер]: #upload-blob
  [Практическое руководство: Перечисление BLOB-объектов в контейнере]: #list-blob
  [Практическое руководство: Загрузка BLOB-объектов]: #download-blobs
  [Практическое руководство: Удаление BLOB-объекта]: #delete-blobs
  [Практическое руководство: Отправка и загрузка больших BLOB-объектов]: #large-blobs
  [руководство-блоб-объект-хранилище]: ../includes/howto-blob-storage.md
  [создание-хранилище-учетная запись]: ../includes/create-storage-account.md
  [Руководство по установке Python]: ../python-how-to-install/
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
