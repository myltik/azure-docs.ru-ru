---
title: Краткое руководство Azure. Отправка, скачивание и составление списка больших двоичных объектов в службе хранилища Azure с помощью языка Python | Документация Майкрософт
description: В рамках этого краткого руководства вы создадите учетную запись хранения и контейнер. Затем используйте клиентскую библиотеку службы хранилища для языка Python, чтобы отправить большой двоичный объект в службу хранилища Azure, скачать его и составить список больших двоичных объектов в контейнере.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: a3b081e34886d4075ae3c88e412d732578d1ce26
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-python"></a>Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью языка Python

Из этого краткого руководства вы узнаете, как использовать Python для отправки, скачивания и перечисления блочных BLOB-объектов в контейнере в хранилище BLOB-объектов Azure. 

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим кратким руководством сделайте следующее: 
* установите [Python](https://www.python.org/downloads/);
* скачайте и установите [пакет SDK службы хранилища Azure для Python](https://github.com/Azure/azure-sdk-for-python). 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Загрузка примера приложения
[Пример приложения](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git), используемый в этом кратком руководстве, является простым приложением Python.  

Используйте команду [git](https://git-scm.com/), чтобы скачать копию приложения в среду разработки. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Эта команда клонирует репозиторий в локальную папку git. Чтобы открыть программу Python, найдите папку storage-blobs-python-quickstart и файл example.py в ней.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Настройка строки подключения хранилища
В приложении нужно указать имя и ключ учетной записи хранения, чтобы создать объект `BlockBlobService`. Откройте файл `example.py` с помощью обозревателя решений в IDE. Замените строки **accountname** и **accountkey** фактическими значениями имени и ключа учетной записи. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Запуск примера
Этот пример создает тестовый файл в папке "Документы". Пример программы отправляет тестовый файл в хранилище BLOB-объектов, выводит списокBLOB-объектов в контейнере и скачивает файл с новым именем. 

Запустите пример. Ниже представлен пример выходных данных, которые возвращаются при выполнении этого приложения.
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Теперь нажмите любую клавишу, чтобы пример программы удалил контейнер хранилища и файлы. Прежде чем продолжить, проверьте наличие двух файлов в папке "Документы". Вы можете открыть их и убедиться, что они идентичны.

Для просмотра файлов в хранилище BLOB-объектов можно также воспользоваться таким средством, как [обозреватель службы хранилища Azure](http://storageexplorer.com). Обозреватель службы хранилища Azure — это бесплатное кроссплатформенное средство для доступа к данным учетной записи хранения. 

После проверки файлов нажмите любую клавишу для завершения демонстрации и удаления тестовых файлов. Теперь вы знаете, что делает этот пример. Давайте откроем файл example.py и изучим его код. 

## <a name="understand-the-sample-code"></a>Разбор примера кода

Разберем пример кода, чтобы понять, как он работает.

### <a name="get-references-to-the-storage-objects"></a>Получение ссылок на объекты хранилища
Сначала необходимо создать ссылки на объекты, используемые для доступа к хранилищу BLOB-объектов и управлению им. Эти объекты зависят друг от друга, и каждый из них используется следующим в списке объектом.

* Создайте экземпляр объекта **BlockBlobService**, указывающий на службу BLOB-объектов в учетной записи хранения. 

* Создайте объект **CloudBlobContainer**, представляющий контейнер, к которому выполняется доступ. Контейнеры используются для организации BLOB-объектов аналогично папкам для упорядочения файлов на компьютере.

Теперь у вас есть контейнер CloudBlobContainer и вы можете создать экземпляр объекта **CloudBlockBlob**, который указывает на конкретный BLOB-объект, и выполнять с ним разные операции: отправку, скачивание, копирование и т. д.

> [!IMPORTANT]
> Имена контейнеров должны состоять из знаков нижнего регистра. Дополнительные сведения об именовании контейнеров и больших двоичных объектов см. в статье [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

В этом разделе вы создадите экземпляры объектов и контейнер, а затем зададите для контейнера разрешения на общий доступ к BLOB-объектам. Контейнер называется **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Отправка BLOB-объектов в контейнер

Хранилище BLOB-объектов поддерживает блочные, добавочные и страничные BLOB-объекты. Чаще всего используются блочные BLOB-объекты. Именно этот тип представлен в этом кратком руководстве.  

Чтобы отправить файл в BLOB-объект, получите полный путь к файлу, соединив имя каталога и имя файла на локальном диске. Затем вы можете передать файл по указанному пути, используя метод **create\_blob\_from\_path**. 

Этот пример кода создает локальный файл, который будет использоваться для отправки и скачивания. Он сохраняет отправляемый файл в переменной **file\_path\_to\_file**, а имя BLOB-объекта — в переменной **local\_file\_name**. В приведенном ниже примере файл отправляется в контейнер с именем **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Существует несколько методов отправки, которые можно использовать с хранилищем BLOB-объектов. Например, если у вас есть поток в памяти, можно применить метод **create\_blob\_from\_stream** вместо **create\_blob\_from\_path**. 

Блочные BLOB-объекты могут иметь размер 4,7 ТБ и представлять собой любые объекты, начиная от электронных таблиц Excel до больших видеофайлов. Страничные BLOB-объекты в основном используются для файлов виртуального жесткого диска, применяемых для поддержки виртуальных машин IaaS. Добавочные BLOB-объекты используются для ведения журнала, например если требуется выполнить запись в файл и затем добавлять дополнительные сведения. Большинство объектов, находящихся в хранилище BLOB-объектов, представляют собой блочные BLOB-объекты.

### <a name="list-the-blobs-in-a-container"></a>Перечисление BLOB-объектов в контейнере

Список файлов в контейнере можно получить с помощью метода **list_blobs**. Этот метод возвращает генератор. Следующий код извлекает список BLOB-объектов и поочередно перебирает их, отображая имена найденных в контейнере BLOB-объектов.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Скачивание больших двоичных объектов

Чтобы скачать BLOB-объекты на локальный диск, воспользуйтесь методом **get\_blob\_to\_path**. Следующий код скачивает BLOB-объект, который мы передали в предыдущем разделе. К имени BLOB-объекта добавляется суффикс _DOWNLOADED, чтобы на локальном диске сохранились оба файла. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Очистка ресурсов
Если вам больше не нужны BLOB-объекты, отправленные при работе с этим руководством, удалите весь контейнер с помощью метода **delete\_container**. Если вам не нужны созданные файлы, удалите их с помощью метода **delete\_blob**.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Ресурсы для разработки приложений Python с большими двоичными объектами

Ознакомьтесь со следующими дополнительными ресурсами для разработки Python с использованием хранилища BLOB-объектов:

### <a name="binaries-and-source-code"></a>Двоичные файлы и исходный код

- Просматривайте, скачивайте и устанавливайте [исходный код клиентской библиотеки Python](https://github.com/Azure/azure-storage-python) для службы хранилища Azure в GitHub.

### <a name="client-library-reference-and-samples"></a>Справочник по клиентской библиотеке и примеры

- Подробные сведения о клиентской библиотеке Python см. в [справочнике по API-интерфейсу Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Изучите [примеры для хранилища BLOB-объектов](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob), написанные с использованием клиентской библиотеки Python.

## <a name="next-steps"></a>Дополнительная информация
 
Из этого краткого руководства вы узнали, как передавать файлы между локальным диском и хранилищем BLOB-объектов Azure с помощью Python. Дополнительные сведения о работе с хранилищем BLOB-объектов см. в соответствующем практическом руководстве.

> [!div class="nextstepaction"]
> [Практическое руководство по операциям в хранилище BLOB-объектов](./storage-python-how-to-use-blob-storage.md)
 
Дополнительные сведения об обозревателе объектов и BLOB-объектах см. в статье [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
