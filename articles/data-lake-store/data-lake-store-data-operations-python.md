---
title: Python. Операции файловой системы в Azure Data Lake Store | Документация Майкрософт
description: Узнайте, как использовать пакет SDK для Python для работы с файловой системой Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 6e92eed5e4083c9b8063318b2a9139e20f53de1a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-python"></a>Операции файловой системы в Azure Data Lake Store с использованием Python
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

В этой статье содержатся сведения об использовании пакета SDK для Python для выполнения операций файловой системы в Azure Data Lake Store. Дополнительные сведения о том, как выполнять операции управления учетными записями в Data Lake Store с помощью Python, см. в статье [Начало работы с Azure Data Lake Store с помощью Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>предварительным требованиям

* **Python**. Скачать Python можно [здесь](https://www.python.org/downloads/). В этой статье используется версия Python 3.6.2.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись хранилища озера данных Azure**. Следуйте инструкциям в разделе [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Установка модулей

Для работы с Data Lake Store с использованием Python необходимо установить три модуля.

* Модуль `azure-mgmt-resource`, который включает в себя модули Azure для Active Directory и т. д.
* Модуль `azure-mgmt-datalake-store`, который включает в себя операции по управлению учетной записью Azure Data Lake Store. Дополнительные сведения см. в [справочнике по модулю управления Azure Data Lake Store](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* Модуль `azure-datalake-store`, который включает в себя операции с файловой системой Azure Data Lake Store. Дополнительные сведения см. в [справочнике по модулю файловой системы Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Чтобы установить модули, используйте следующие команды.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Создание приложения Python

1. Для создания приложения Python используйте IDE по своему усмотрению, например **mysample.py**.

2. Чтобы импортировать необходимые модули, добавьте следующие строки.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Сохраните изменения в mysample.py.

## <a name="authentication"></a>Authentication

В этом разделе мы рассмотрим различные способы проверки подлинности в Azure AD. Доступны следующие варианты.

* Дополнительные сведения о проверке подлинности пользователей в приложении см. в статье [End-user authentication with Data Lake Store using Python](data-lake-store-end-user-authenticate-python.md) (Аутентификация пользователей в Data Lake Store с использованием Python).
* Дополнительные сведения о проверке подлинности между службами в приложении см. в статье [Service-to-service authentication with Data Lake Store using Python](data-lake-store-service-to-service-authenticate-python.md) (Аутентификация между службами в Data Lake Store с использованием Python).

## <a name="create-filesystem-client"></a>Создание файловой системы клиента

Следующий фрагмент кода сначала создает клиент учетной записи Data Lake Store. Затем он использует объект клиента для создания учетной записи Data Lake Store. И наконец, он создает объект клиента файловой системы.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Создайте каталог

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Отправить файл.


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Скачивание файла

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Удаление каталога

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Дополнительная информация
* [Начало работы с Azure Data Lake Store с помощью Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>См. также
* [Справочник по Python Azure Data Lake Store. Управление учетными записями](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Справочник по Python Azure Data Lake Store. Файловая система](http://azure-datalake-store.readthedocs.io/en/latest)
* [Приложения больших данных с открытым исходным кодом, которые работают с хранилищем озера данных Azure](data-lake-store-compatible-oss-other-applications.md)
