---
title: "Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью Python | Документация Майкрософт"
description: "Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью Python"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: f6e325f70a37200552832b9c0ac38f6c56471f2c
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью Python
В этой статье описывается получение списка, отправка и скачивание больших двоичных объектов с помощью API Python. API на языке Python, предоставляемый с пакетом SDK для Azure, обеспечивает следующие возможности:

* Создание контейнера
* Отправка BLOB-объекта в контейнер
* Скачивание больших двоичных объектов
* Перечисление BLOB-объектов в контейнере
* Удаление большого двоичного объекта

Дополнительные сведения об использовании Python API см. в статье [Использование хранилища BLOB-объектов Azure из Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Если используется виртуальная машина, созданная с помощью скриптов, предоставленных [виртуальными машинами для обработки и анализа данных в Azure](virtual-machines.md), то программа AzCopy уже установлена на виртуальной машине.
> 
> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ.

* Сведения о настройке подписки Azure см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и получению сведений об учетной записи и ключах см. в статье [Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md).

## <a name="upload-data-to-blob"></a>Отправка данных в большой двоичный объект
Добавьте следующий фрагмент кода в начало любого кода Python, из которого планируется получать доступ к службе хранилища Azure программным способом:

    from azure.storage.blob import BlobService

Объект **BlobService** позволяет работать с контейнерами и BLOB-объектами. Следующий код создает объект BlobService, используя имя и ключ учетной записи хранения. Замените имя учетной записи и ее ключ фактическими значениями.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Используйте следующие методы для отправки данных в большой двоичный объект:

1. put\_block\_blob\_from\_path (отправка содержимого файла из указанного пути).
2. put\_block_blob\_from\_file (отправка содержимого открытого файла или потока).
3. put\_block\_blob\_from\_bytes (отправка массива байтов).
4. put\_block\_blob\_from\_text (отправка указанного текстового значения с использованием указанной кодировки).

Следующий пример кода отправляет локальный файл в контейнер:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Следующий пример кода отправляет все файлы (за исключением каталогов) в локальном каталоге в хранилище больших двоичных объектов:

    from azure.storage.blob import BlobService
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


## <a name="download-data-from-blob"></a>Скачивание данных из большого двоичного объекта
Чтобы скачать данные из большого двоичного объекта, используйте следующие методы:

1. get\_blob\_to\_path.
2. get\_blob\_to\_file.
3. get\_blob\_to\_bytes.
4. get\_blob\_to\_text.

Это методы, которые выполняют необходимое фрагментирование данных, если их размер превышает 64 МБ.

Следующий пример кода скачивает содержимое большого двоичного объекта в контейнере в локальный файл:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Следующий пример кода скачивает все большие двоичные объекты в контейнере. Он использует метод list\_blobs для получения списка больших двоичных объектов, доступных в контейнере, и скачивает их в локальный каталог.

    from azure.storage.blob import BlobService
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
