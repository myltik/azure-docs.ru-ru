<properties 
	pageTitle="Использование хранилища больших двоичных объектов из Python | Microsoft Azure" 
	description="Узнайте, как использовать службу BLOB-объектов Azure из Python для отправки, перечисления, загрузки и удаления больших двоичных объектов." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="huvalo"/>

# Использование хранилища BLOB-объектов из Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием службы хранилища BLOB-объектов Azure. Примеры написаны на Python и используют [пакет Azure для Python][]. Здесь описаны такие сценарии, как **отправка**, **перечисление**,**загрузка** и **удаление** BLOB-объектов.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Практическое руководство. Создание контейнера

> [AZURE.NOTE]Если требуется установить Python или [пакет Azure для Python][], см. [Руководство по установке Python](../python-how-to-install.md).

Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Указанный ниже код создает объект **BlobService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import BlobService

Следующий код создает объект **BlobService**, используя имя и ключ доступа учетной записи. Замените myaccount и mykey фактическими значениями учетной записи и ключа.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Объект **BlobService** можно использовать для создания контейнера, если контейнер не существует:

	blob_service.create_container('mycontainer')

По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, можно создать контейнер и предоставить открытый доступ, используя следующий код:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Либо можно изменить контейнер после его создания, используя следующий код:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

После этого изменения любой пользователь в Интернете сможет видеть большие двоичные объекты в общедоступном контейнере, но изменить или удалить их сможете только вы.

## Практическое руководство. Отправка BLOB-объекта в контейнер

Чтобы отправить данные в blob-объект, используйте методы **put_block_blob_from_path**, **put_block_blob_from_file**, **put_block_blob_from_bytes** или **put_block_blob_from_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

**put_block_blob_from_path** отправляет содержимое файла из заданного пути, **put_block_blob_from_file** отправляет содержимое уже уже открытого файла или потока. **put_block_blob_from_bytes** отправляет массив байтов, **put_block_blob_from_text** отправляет заданное значение текста с использованием определенного шифрования (по умолчанию UTF-8).

В следующем примере содержимое файла **sunset.png** передается в большой двоичный объект **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы получить список больших двоичных объектов в контейнере, используйте метод **list_blobs** в цикле **for** для отображения имени каждого такого объекта в контейнере. Следующий код выводит на консоль **имя** и **URL-адрес** каждого большого двоичного объекта в контейнере.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## Практическое руководство. Загрузка BLOB-объектов

Чтобы загрузить данные из blob-объекта, используйте **get_blob_to_path**, **get_blob_to_file**, **get_blob_to_bytes** или **get_blob_to_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В примере ниже демонстрируется использование метода **get_blob_to_path** для загрузки содержимого большого двоичного объекта **myblob** и его сохранения в файле **out-sunset.png**.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Практическое руководство. Удаление BLOB-объекта

Наконец, чтобы удалить BLOB-объект, вызовите **deleteBlob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение данных и доступ к ним в Azure][]
-   Посетите [блог команды разработчиков хранилища Azure][].

[Хранение данных и доступ к ним в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[блог команды разработчиков хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[пакет Azure для Python]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO4-->