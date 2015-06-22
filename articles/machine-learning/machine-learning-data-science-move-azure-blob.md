<properties 
	pageTitle="Перемещение данных в хранилище и из него  Хранилище больших двоичных объектов Azure" 
	description="Перемещение данных в хранилище и из него  Хранилище больших двоичных объектов Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="sunliangms,sachouks" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,sachouks" />

# Перемещение данных в хранилище больших двоичных объектов Azure и из него

Статья о [примерах сценариев обработки и анализа данных в облаке](http://azure.microsoft.com/documentation/articles/machine-learning-data-science-plan-sample-scenarios) помогает определить, какие ресурсы необходимы для выполнения различных рабочих процессов обработки и анализа данных. Если вам необходимо переместить данные в хранилище больших двоичных объектов Azure или из него, основываясь на сценарии, используйте один из следующих методов:

- [Использование обозревателя хранилищ Azure](#explorer)
- [Использование служебной программы командной строки AzCopy](#AzCopy)
- [Использование пакета SDK для Azure на языке Python](#PythonSDK)



> [AZURE.NOTE] Полное описание базовых принципов использования хранилища больших двоичных объектов Azure см. в статьях [Основы использования больших двоичных объектов Azure](../storage-dotnet-how-to-use-blobs.md) и  [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx). 

Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ. Указания по получению этой информации см. в "Практическом руководстве: Просмотр, копирование и повторное создание ключей доступа к хранилищу" статьи [Управление учетными записями хранения](../storage-create-storage-account.md). Для выполнения указаний в этом документе у вас должна быть учетная запись хранения Azure и соответствующие ключи.

<a id="explorer"></a>
## Использование обозревателя хранилищ Azure 

Обозреватель хранилищ Azure - это бесплатный инструмент для проверки и изменения данных в учетной записи хранения Azure на платформе Windows. Его можно скачать, перейдя на страницу [Обозреватель хранилищ Azure](http://azurestorageexplorer.codeplex.com/). Ниже рассказывается о том, как отправить или скачать данные с помощью обозревателя хранилищ Azure. 

1.  Запустите обозреватель хранилищ Azure. 
2.  Если учетная запись хранения, к которой нужно получить доступ, не добавлена в обозреватель хранилищ Azure, нажмите кнопку "Добавить учетную запись", чтобы ее добавить. Если она уже добавлена, выберите ее в раскрывающемся списке "--Выбрать учетную запись хранения--".  
![Create workspace][1]
<br>
3. Введите имя учетной записи хранения и соответствующий ключ, а затем щелкните "Добавить учетную запись хранения". Можно добавить несколько учетных записей хранения, и каждая из них будет отображаться на вкладке. На левой панели под этой учетной записью хранения отображаются контейнеры. Выберите контейнер, чтобы на панели справа отобразились хранящиеся в нем большие двоичные объекты.  
![Create workspace][2]
<br>
![Create workspace][3]
<br>
4. Отправьте данные, нажав кнопку "Передать". Выберите один или несколько файлов в файловой системе для отправки и нажмите кнопку "Открыть", чтобы начать их передачу.
5. Скачайте данные, выбрав большой двоичный объект в соответствующем контейнере и нажав кнопку "Скачать".

<a id="AzCopy"></a>
## Использование программы AzCopy

AzCopy - это служебная программа командной строки для отправки и скачивания данных. 

**Предупреждение**. Если вы используете другой компьютер вместо виртуальной машины, установленной ранее в ходе обработки и анализа данных в облаке, установите AzCopy, придерживаясь следующих указаний по установке: [Скачивание и установка AzCopy](../storage-use-azcopy.md#install).

####Примеры отправки файлов в большие двоичные объекты и скачивания файлов из них:

	# Uploading from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

> [AZURE.TIP]   
> 1. При отправке с параметром /S файлы отправляются рекурсивно. Без этого параметра файлы из подкаталога не отправляются.  
> 2. При скачивании файла с параметром /S выполняется рекурсивный поиск по контейнеру до тех пор, пока не будут скачаны все файлы в указанном каталоге и его подкаталогах или все файлы, соответствующие указанному шаблону в заданном каталоге или его подкаталогах.  
> 3.  При использовании параметра /Source нельзя указать определенный файл большого двоичного объекта для скачивания. Чтобы скачать определенный файл, укажите имя файла большого двоичного объекта для скачивания, используя параметр /Pattern. Параметр /S можно использовать для рекурсивного поиска шаблона имени файла с помощью AzCopy. Без параметра шаблона AzCopy будет скачивать все файлы, содержащиеся в этом каталоге. 

Дополнительную информацию об использовании AzCopy см. в статье [Приступая к работе со служебной программой командной строки AzCopy](../storage-use-azcopy.md#install).


<a id="PythonSDK"></a>
## Использование Python

API на языке Python, предоставляемый с пакетом SDK для Azure, обеспечивает следующие возможности:

- Создание контейнера
- Отправка BLOB-объекта в контейнер
- Скачивание больших двоичных объектов
- Перечисление BLOB-объектов в контейнере
- Удаление большого двоичного объекта

В этом разделе приведены указания по перечислению, отправке и скачиванию больших двоичных объектов. Дополнительную информацию об использовании API на языке Python см. в статье [Использование службы BLOB-объектов в Python](../storage-python-how-to-use-blob-storage.md). 

> [AZURE.NOTE] Если вы используете другой компьютер вместо виртуальной машины, установленной ранее в ходе обработки и анализа данных в облаке, прежде чем использовать пример кода ниже, необходимо установить [пакет SDK для Azure на Python](../python-how-to-install.md).

###Отправка данных в большой двоичный объект
Добавьте следующий фрагмент кода в начало любого кода Python, из которого планируется получать доступ к службе хранилища Azure программным способом:

	from azure.storage import BlobService

Объект **BlobService** позволяет работать с контейнерами и большими двоичными объектами. Следующий код создает объект BlobService, используя имя и ключ учетной записи хранения. Замените имя учетной записи и ее ключ фактическими значениями.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Используйте следующие методы для отправки данных в большой двоичный объект:
 
1. put_block_blob_from_path (отправка содержимого файла из указанного пути);
2. put_block_blob_from_file (отправка содержимого открытого файла или потока);
3. put_block_blob_from_bytes (отправка массива байтов);
4. put_block_blob_from_text (отправка указанного текстового значения с использованием указанной кодировки).
 
Следующий пример кода отправляет локальный файл в контейнер:
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Следующий пример кода отправляет все файлы (за исключением каталогов) в локальном каталоге в хранилище больших двоичных объектов:

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

###Скачивание данных из большого двоичного объекта

Чтобы скачать данные из большого двоичного объекта, используйте следующие методы:
1. get_blob_to_path;
2. get_blob_to_file;
3. get_blob_to_bytes;
4. get_blob_to_text. 

Это методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ. 

Следующий пример кода скачивает содержимое большого двоичного объекта в контейнере в локальный файл: 

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Следующий пример кода скачивает все большие двоичные объекты в контейнере. Он использует метод list_blobs для получения списка больших двоичных объектов, доступных в контейнере, и скачивает их в локальный каталог. 

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!--HONumber=49--> 
