<properties 
	pageTitle="Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью AzCopy | Microsoft Azure" 
	description="Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью AzCopy" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью AzCopy

Сведения о технологиях, которые используются для перемещения данных в хранилище больших двоичных объектов Azure и из него, см. по следующим ссылкам:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Введение

AzCopy — это служебная программа командной строки, разработанная для быстрой отправки, загрузки и копирования данных из хранилища больших двоичных объектов, файлов и табличного хранилища Microsoft Azure.

Указания по установке программы AzCopy и дополнительные сведения о ее использовании с платформой Azure см. в разделе [Начало работы с программой командной строки AzCopy](../storage-use-azcopy.md).

> [AZURE.NOTE]Если используется виртуальная машина, созданная с помощью скриптов, предоставленных [виртуальными машинами для обработки и анализа данных в Azure](machine-learning-data-science-virtual-machines.md), то программа AzCopy уже установлена на виртуальной машине.

> [AZURE.NOTE]Полное описание базовых принципов использования хранилища больших двоичных объектов Azure см. в статьях [Основы использования больших двоичных объектов Azure](../storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Предварительные требования

Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

- Чтобы настроить подписку Azure, перейдите в раздел [Бесплатный пробный период на 1 месяц](https://azure.microsoft.com/ru-RU/pricing/free-trial/).
- Указания по созданию учетной записи хранения и получению учетной записи и сведений о ключах см. в разделе [Об учетных записях хранения Azure](../storage-create-storage-account.md).

## Передача файлов в большой двоичный объект Azure

Для передачи файла используйте следующую команду в окне командной строки AzCopy:

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

## Скачивание файлов из большого двоичного объекта Azure

Для скачивания файла из большого двоичного объекта Azure используйте следующую команду в окне командной строки AzCopy:

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

## Передача больших двоичных объектов между контейнерами Azure

Для передачи больших двоичных объектов между контейнерами Azure используйте следующую команду в окне командной строки AzCopy:

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

## Советы по использованию AzCopy

> [AZURE.TIP]1. При отправке с параметром /S файлы отправляются рекурсивно. Без этого параметра файлы из подкаталога не отправляются. 2. При скачивании файла с параметром /S выполняется рекурсивный поиск по контейнеру до тех пор, пока не будут скачаны все файлы в указанном каталоге и его подкаталогах или все файлы, соответствующие указанному шаблону в заданном каталоге или его подкаталогах. 3. При использовании параметра /Source нельзя указать определенный файл большого двоичного объекта для скачивания. Чтобы скачать определенный файл, укажите имя файла большого двоичного объекта для скачивания, используя параметр /Pattern. Параметр /S можно использовать для рекурсивного поиска шаблона имени файла с помощью AzCopy. Без параметра шаблона AzCopy будет скачивать все файлы, содержащиеся в этом каталоге.

<!---HONumber=Nov15_HO1-->