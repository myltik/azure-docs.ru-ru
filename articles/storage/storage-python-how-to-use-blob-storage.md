<properties
	pageTitle="Использование хранилища BLOB-объектов Azure (хранилища объектов) из Python | Microsoft Azure"
	description="Хранение неструктурированных данных в облаке в хранилище BLOB-объектов Azure."
	services="storage"
	documentationCenter="python"
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
    ms.date="09/20/2016"
	ms.author="jwillis;tamram"/>

# Использование хранилища больших двоичных объектов Azure из Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## Обзор

Хранилище BLOB-объектов Azure — это служба, которая хранит неструктурированные данные в облаке в качестве объектов или больших двоичных объектов. В хранилище BLOB-объектов могут храниться текстовые или двоичные данные любого типа, например документы, файлы мультимедиа или установщики приложений. Хранилище BLOB-объектов иногда также называют хранилищем объектов.

В этой статье описано, как реализовать типичные сценарии с использованием хранилища больших двоичных объектов. Примеры написаны на Python, и в них используется [пакет SDK для службы хранилища Microsoft Azure для Python]. Здесь описаны такие сценарии, как отправка, перечисление, скачивание и удаление больших двоичных объектов.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание контейнера

В зависимости от требуемого типа большого двоичного объекта создайте объект **BlockBlobService**, **AppendBlobService** или **PageBlobService**. В следующем коде используется объект **BlockBlobService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать программный доступ к хранилищу BLOB-объектов Azure.

	from azure.storage.blob import BlockBlobService

Следующий код создает объект **BlockBlobService**, используя имя и ключ учетной записи хранения. Замените myaccount и mykey фактическими значениями имени и ключа учетной записи.

	block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

В следующем примере кода для создания контейнера (если он не существует) можно использовать объект **BlockBlobService**.

	block_blob_service.create_container('mycontainer')

По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы скачать большие двоичные объекты из этого контейнера. Чтобы сделать большие двоичные объекты в этом контейнере доступными для всех пользователей, вы можете создать контейнер и предоставить открытый доступ, используя следующий код.

	from azure.storage.blob import PublicAccess
	block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

Либо можно изменить контейнер после его создания, используя следующий код.

	block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

После этого изменения любой пользователь в Интернете сможет видеть большие двоичные объекты в общедоступном контейнере, но изменить или удалить их сможете только вы.

## Отправка BLOB-объекта в контейнер

Чтобы создать блочный BLOB-объект и передать данные, используйте метод **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** или **create\_blob\_from\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

**create\_blob\_from\_path** передает содержимое файла из заданного пути, **create\_blob\_from\_stream** передает содержимое уже открытого файла или потока. **create\_blob\_from\_bytes** передает массив байтов, а **create\_blob\_from\_text** передает заданное текстовое значение в заданной кодировке (по умолчанию — UTF-8).

В следующем примере содержимое файла **sunset.png** передается в большой двоичный объект **myblob**.

	from azure.storage.blob import ContentSettings
	block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
				)

## Перечисление BLOB-объектов в контейнере

Чтобы перечислить большие двоичные объекты в контейнере, используйте метод **list\_blobs**. Этот метод возвращает генератор. Следующий код выводит на консоль **имя** каждого большого двоичного объекта в контейнере.

	generator = block_blob_service.list_blobs('mycontainer')
	for blob in generator:
		print(blob.name)

## Скачивание больших двоичных объектов

Чтобы скачать данные из большого двоичного объекта, используйте **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** или **get\_blob\_to\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В примере ниже показано использование метода **get\_blob\_to\_path** для скачивания содержимого большого двоичного объекта **myblob** и его сохранения в файл **out-sunset.png**.

	block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## Удаление большого двоичного объекта

Наконец, чтобы удалить BLOB-объект, вызовите **deleteBlob**.

	block_blob_service.delete_blob('mycontainer', 'myblockblob')

## Запись в расширенный большой двоичный объект

Добавочный большой двоичный объект оптимизирован для операций добавления, например ведения журналов. Как и блочный BLOB-объект, добавочный большой двоичный объект состоит из блоков, но при добавлении нового блока в добавочный большой двоичный объект он всегда добавляется в конец этого объекта. Вы не можете обновить или удалить существующий блок в добавочном большом двоичном объекте. Идентификаторы блоков в добавочном большом двоичном объекте не отображаются, как в блочном BLOB-объекте.

Каждый блок в добавочном большом двоичном объекте может иметь разный размер (не более 4 МБ), кроме того, добавочный большой двоичный объект может содержать не более 50 000 блоков. Таким образом, максимальный размер добавочного большого двоичного объекта немного превышает 195 ГБ (4 МБ X 50 000 блоков).

Приведенный ниже пример создает новый добавочный большой двоичный объект и добавляет в него некоторые данные, имитируя простые операции ведение журнала.

	from azure.storage.blob import AppendBlobService
	append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

	# The same containers can hold all types of blobs
	append_blob_service.create_container('mycontainer')

	# Append blobs must be created before they are appended to
	append_blob_service.create_blob('mycontainer', 'myappendblob')
	append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

	append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## Дальнейшие действия

Вы ознакомились с базовыми понятиями о хранилище BLOB-объектов. Дополнительные сведения см. по следующим ссылкам.

- [Центр по разработке для Python](/develop/python/)
- [API-интерфейс REST служб хранилища Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Блог рабочей группы службы хранилища Azure]
- [пакет SDK для службы хранилища Microsoft Azure для Python]

[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет SDK для службы хранилища Microsoft Azure для Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0921_2016-->