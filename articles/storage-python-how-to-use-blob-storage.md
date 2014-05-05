<properties linkid="develop-python-blob-service" urlDisplayName="Служба BLOB-объектов" pageTitle="Использование хранилища BLOB-объектов (Python) | Microsoft Azure" metaKeywords="служба BLOB-объектов Azure Python, BLOB-объекты Azure Python" description="Узнайте, как использовать службу BLOB-объектов для отправки, перечисления, загрузки и удаления BLOB-объектов." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="Использование службы BLOB-объектов из Python" authors="" videoId="" scriptId="" />

# Использование службы BLOB-объектов из Python
В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища BLOB-объектов Azure. Эти примеры написаны с помощью API Python. Здесь описаны такие сценарии, как **отправка**, **перечисление**,
**загрузка** и **удаление** BLOB-объектов. Дополнительные сведения о BLOB-объектах см. в разделе [Дальнейшие действия][].

## Оглавление

[Что такое хранилище BLOB-объектов?][]   
 [Основные понятия][]   
 [Создание учетной записи хранения Azure][]   
 [Практическое руководство. Создание контейнера][]   
 [Практическое руководство. Отправка BLOB-объекта в контейнер][]   
 [Практическое руководство. Перечисление BLOB-объектов в контейнере][]   
 [Практическое руководство. Загрузка BLOB-объектов][]   
 [Практическое руководство. Удаление BLOB-объекта][]   
 [Практическое руководство. Отправка и загрузка больших BLOB-объектов][]   
 [Дальнейшие действия][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Создание учетной записи хранения Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>Практическое руководство. Создание контейнера

**Примечание.** Если нужно установить клиентские библиотеки или Python, см. [руководство по установке Python](../python-how-to-install/)


Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Следующий код создает объект **BlobService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import *

Следующий код создает объект **BlobService**, используя имя и ключ доступа учетной записи.  Замените "myaccount" и "mykey" на фактические значения учетной записи и ключа.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Все BLOB-объекты хранилища содержатся в контейнере. Объект **BlobService** можно использовать для создания контейнера, если контейнер не существует:

	blob_service.create_container('mycontainer')

По умолчанию новый контейнер является закрытым, поэтому необходимо указать
ключ доступа к хранилищу (как делалось раньше), чтобы загрузить BLOB-объекты из этого
контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, можно создать контейнер и предоставить открытый доступ, используя следующий код:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Либо можно изменить контейнер после его создания, используя следующий код:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

После этого изменения любой пользователь в Интернете может видеть BLOB-объекты в открытом контейнере, но изменить или удалить их можете только вы.

## <a name="upload-blob"> </a>Практическое руководство. Отправка BLOB-объекта в контейнер

Чтобы передать файл в BLOB-объект, используйте метод **put_blob** для создания BLOB-объекта, используя файловый поток в качестве содержимого BLOB-объекта.
Сначала создайте файл с именем **task1.txt** (подходит любой контент) и сохраните его в одном каталоге с файлом Python.

	myblob = open(r'task1.txt', 'r').read()
	blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

## <a name="list-blob"> </a>Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы получить список BLOB-объектов в контейнере, используйте метод **list_blobs** с циклом
**for** для отображения имени каждого BLOB-объекта в контейнере. Следующий код выводит на консоль имя (**name**) и URL-адрес (**url**) каждого BLOB-объекта в контейнере.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>Практическое руководство. Загрузка BLOB-объектов

Для загрузки BLOB-объектов используйте метод **get_blob** для передачи содержимого BLOB-объектов в потоковый объект, который затем можно записать в локальный файл.

	blob = blob_service.get_blob('mycontainer', 'myblob')
	with open(r'out-task1.txt', 'w') as f:
		f.write(blob)

## <a name="delete-blobs"> </a>Практическое руководство. Удаление BLOB-объекта

Наконец, чтобы удалить BLOB-объект, вызовите **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="large-blobs"> </a>Практическое руководство. Отправка и загрузка больших BLOB-объектов

Максимальный размер BLOB-объектов блочного типа составляет 200 ГБ.  Если размер BLOB-объекта не превышает 64 МБ, этот BLOB-объект можно отправить или загрузить, используя один вызов **put\_blob** или **get\_blob**, как показано ранее.  Если размер BLOB-объекта превышает 64 МБ, его необходимо отправлять или загружать блоками не более 4 МБ.

Следующий код содержит примеры функций для отправки или загрузки блочных BLOB-объектов любого размера.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

Если нужны BLOB-объекты размером более 200 ГБ, вместо блочного BLOB-объекта можно использовать страничный BLOB-объект.  Максимальный размер страничного BLOB-объекта равен 1 ТБ со страницами, выровненными по 512-байтовым границам страниц.  Используйте **put\_blob** для создания страничного BLOB-объекта, **put\_page** для записи в него и **get\_blob** для чтения из этого BLOB-объекта.

## <a name="next-steps"> </a>Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите блог [команды разработчиков хранилища Azure][]

  [Дальнейшие действия]: #next-steps
  [Что такое хранилище BLOB-объектов?]: #what-is
  [Основные понятия]: #concepts
  [Создание учетной записи хранения Azure]: #create-account
  [Практическое руководство. Создание контейнера]: #create-container
  [Практическое руководство. Отправка BLOB-объекта в контейнер]: #upload-blob
  [Практическое руководство. Перечисление BLOB-объектов в контейнере]: #list-blob
  [Практическое руководство. Загрузка BLOB-объектов]: #download-blobs
  [Практическое руководство. Удаление BLOB-объекта]: #delete-blobs
  [Практическое руководство. Отправка и загрузка больших BLOB-объектов]: #large-blobs
  [Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/ru-ru/library/windowsazure/gg433040.aspx
  [команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/

