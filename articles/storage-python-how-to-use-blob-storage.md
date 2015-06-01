<properties 
	pageTitle="Использование хранилища BLOB-объектов из Python в Microsoft Azure" 
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
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Использование хранилища BLOB-объектов из Python

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Обзор

В этом руководстве показано, как реализовать типичные сценарии с использованием
службы хранения BLOB-объектов Azure. Примеры написаны на Python и используют [пакет Azure для Python][]. Здесь описаны такие сценарии, как **отправка**, **перечисление**,
**скачивание** и **удаление** больших двоичных объектов.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Практическое руководство. Создание контейнера

> [AZURE.NOTE] Если требуется установить Python или [пакет Azure для Python][], см. [Руководство по установке Python](python-how-to-install.md).


Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Следующий код создает объект **BlobService**. Добавьте следующий код в начало любого файла Python, из которого планируется получать доступ к хранилищу Azure программным способом:

	from azure.storage import BlobService

Следующий код создает объект **BlobService**, используя имя и ключ доступа учетной записи.  Замените 'myaccount' и 'mykey' фактическими значениями учетной записи и ключа.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Все BLOB-объекты хранилища содержатся в контейнере. Объект **BlobService** можно использовать для создания контейнера, если контейнер не существует:

	blob_service.create_container('mycontainer')

По умолчанию новый контейнер является закрытым, поэтому необходимо указать ключ доступа к хранилищу (как делалось раньше), чтобы загрузить BLOB-объекты из этого контейнера. Чтобы сделать файлы в этом контейнере доступными для всех пользователей, можно создать контейнер и предоставить открытый доступ, используя следующий код:

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Либо можно изменить контейнер после его создания, используя следующий код:

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

После этого изменения любой пользователь в Интернете сможет видеть большие двоичные объекты в общедоступном контейнере, но изменить или удалить их сможете только вы.

## Практическое руководство. Отправка BLOB-объекта в контейнер

Для отправки данных в BLOB-объект используйте методы **put_block_blob_from_path**, **put_block_blob_from_file**, **put_block_blob_from_bytes** или **put_block_blob_from_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

Метод **put_block_blob_from_path** отправляет содержимое файла из заданного пути, метод **put_block_blob_from_file** отправляет содержимое уже открытого файла или потока. Метод **put_block_blob_from_bytes** отправляет массив байтов, а метод **put_block_blob_from_text** отправляет заданное значение текста с использованием определенного шифрования (по умолчанию UTF-8).

В следующем примере содержимое файла **sunset.png** передается в BLOB-объект **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Практическое руководство. Перечисление BLOB-объектов в контейнере

Чтобы перечислить BLOB-объекты в контейнере, используйте метод **list_blobs** в цикле
**for** для отображения имени каждого BLOB-объекта в контейнере. Следующий код выводит на консоль **имя** и **URL-адрес** каждого большего двоичного объекта в контейнере.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## Практическое руководство. Загрузка BLOB-объектов

Чтобы загрузить данные из BLOB-объекта, используйте метод **get_blob_to_path**, **get_blob_to_file**, **get_blob_to_bytes** или **get_blob_to_text**. Это высокоуровневые методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

В следующем примере демонстрируется использование метода **get_blob_to_path** для загрузки содержимого BLOB-объекта **myblob** и его сохранения в файл **out-sunset.png**:

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Практическое руководство. Удаление BLOB-объекта

Наконец, чтобы удалить большой двоичный объект, вызовите **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Дальнейшие действия

Вы изучили основные сведения о хранилище BLOB-объектов. Дополнительные сведения о более сложных задачах по использованию хранилища можно найти по следующим ссылкам.

-   См. справочник MSDN: [Хранение и доступ к данным в Azure][]
-   Посетите [блог рабочей группы службы хранилища Azure][]

[Хранение и доступ к данным в Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Блог рабочей группы службы хранилища Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Пакет Azure для Python]: https://pypi.python.org/pypi/azure  

<!--HONumber=49-->