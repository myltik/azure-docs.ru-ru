<properties
	pageTitle="Использование хранилища больших двоичных объектов Azure из Python | Microsoft Azure"
	description="Узнайте, как отправлять, перечислять, скачивать и удалять большие двоичные объекты с помощью хранилища больших двоичных объектов Azure из Python."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="emgerner"/>

# Использование хранилища больших двоичных объектов Azure из Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этой статье описано, как реализовать типичные сценарии с использованием хранилища больших двоичных объектов. Примеры написаны на Python и используют пакет [Хранилище Azure для Python][]. Здесь описаны такие сценарии, как отправка, перечисление, скачивание и удаление больших двоичных объектов.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Создание контейнера

> [AZURE.NOTE]Если требуется установить Python или [пакет Azure для Python][], см. [Руководство по установке Python](../python-how-to-install.md).

Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Следующий код создает объект **BlobService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к службе хранилища Azure программным способом.

	from azure.storage.blob import BlobService

Следующий код создает объект **BlobService**, используя имя и ключ доступа учетной записи. Замените myaccount и mykey фактическими значениями учетной записи и ключа.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

В следующем примере кода для создания контейнера (если он не существует) можно использовать объект **BlobService**.

	blob_service.create_container('mycontainer')

По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы скачать большие двоичные объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, вы можете создать контейнер и предоставить открытый доступ, используя следующий код.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

Либо можно изменить контейнер после его создания, используя следующий код.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

После этого изменения любой пользователь в Интернете сможет видеть большие двоичные объекты в общедоступном контейнере, но изменить или удалить их сможете только вы.

## Отправка BLOB-объекта в контейнер

Чтобы отправить данные в blob-объект, используйте методы **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** или **put\_block\_blob\_from\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

**put\_block\_blob\_from\_path** отправляет содержимое файла из заданного пути, **put\_block\_blob\_from\_file** отправляет содержимое уже открытого файла или потока. **put\_block\_blob\_from\_bytes** отправляет массив байтов, **put\_block\_blob\_from\_text** отправляет заданное значение текста с использованием определенного кодирования (по умолчанию UTF-8).

В следующем примере содержимое файла **sunset.png** передается в большой двоичный объект **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Перечисление BLOB-объектов в контейнере

Чтобы перечислить большие двоичные объекты в контейнере, используйте метод **list\_blobs**. Каждый вызов **list\_blobs** возвращает сегмент результатов. Чтобы получить все результаты, проверьте **next\_marker** результатов и при необходимости повторно вызовите **list\_blobs**. Следующий код выводит на консоль **имя** каждого большого двоичного объекта в контейнере.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Скачивание больших двоичных объектов

В каждом сегменте результатов может содержаться переменное количество больших двоичных объектов (до 5000). Если для определенного сегмента существует **next\_marker**, контейнер может содержать много больших двоичных объектов.

Чтобы скачать данные из большого двоичного объекта, используйте **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** или **get\_blob\_to\_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В примере ниже показано использование метода **get\_blob\_to\_path** для скачивания содержимого большого двоичного объекта **myblob** и его сохранения в файл **out-sunset.png**.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Удаление большого двоичного объекта

Наконец, чтобы удалить BLOB-объект, вызовите **deleteBlob**.

	blob_service.delete_blob('mycontainer', 'myblob')

## Дальнейшие действия

Вы изучили основную информацию о хранилище больших двоичных объектов. Дополнительную информацию о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите [блог команды разработчиков хранилища Azure][].

[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет Azure для Python]: https://pypi.python.org/pypi/azure
[Хранилище Azure для Python]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=Sept15_HO2-->