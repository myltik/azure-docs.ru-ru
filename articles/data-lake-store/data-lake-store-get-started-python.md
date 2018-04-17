---
title: Python. Операции управления учетными записями в Azure Data Lake Store | Документация Майкрософт
description: Узнайте, как использовать пакет SDK для Python для работы с операциями управления учетными записями Data Lake Store.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5f060dfd061af07f220576f17110afccd01fdae5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>Операции управления учетными записями в Azure Data Lake Store с использованием Python
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Узнайте, как использовать пакет SDK для Python для Azure Data Lake Store для выполнения основных операций управления учетными записям, таких как создание учетной записи Data Lake Store, перечисление учетных записей Data Lake Store и т. д. Дополнительные сведения о том, как выполнять операции файловой системы в Data Lake Store с помощью Python, см. в [этой статье](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>предварительным требованиям

* **Python**. Скачать Python можно [здесь](https://www.python.org/downloads/). В этой статье используется версия Python 3.6.2.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Группа ресурсов Azure.** Инструкции см. в статье [Управление ресурсами Azure через портал](../azure-resource-manager/resource-group-portal.md).

## <a name="install-the-modules"></a>Установка модулей

Для работы с Data Lake Store с использованием Python необходимо установить три модуля.

* Модуль `azure-mgmt-resource`, который включает в себя модули Azure для Active Directory и т. д.
* Модуль `azure-mgmt-datalake-store`, который включает в себя операции по управлению учетной записью Azure Data Lake Store. Дополнительные сведения см. в [справочнике по модулю управления Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/sample_azure-mgmt-datalake-store.html).
* Модуль `azure-datalake-store`, который включает в себя операции с файловой системой Azure Data Lake Store. Дополнительные сведения см. в [справочнике по модулю файловой системы Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Чтобы установить модули, используйте следующие команды.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Создание приложения Python

1. Для создания приложения Python используйте IDE по своему усмотрению, например **mysample.py**.

2. Чтобы импортировать необходимые модули, добавьте следующий фрагмент кода.

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

## <a name="create-client-and-data-lake-store-account"></a>Создание клиента и учетной записи Data Lake Store

Следующий фрагмент кода сначала создает клиент учетной записи Data Lake Store. Затем он использует объект клиента для создания учетной записи Data Lake Store. И наконец, он создает объект клиента файловой системы.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>Получение списка учетных записей Data Lake Store

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>Удаление учетной записи Data Lake Store

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Дополнительная информация
* [Операции файловой системы в Data Lake Store с помощью пакета SDK для Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>См. также
* [Справочник по Python Azure Data Lake Store. Управление учетными записями](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Справочник по Python Azure Data Lake Store. Файловая система](http://azure-datalake-store.readthedocs.io/en/latest)
* [Приложения больших данных с открытым исходным кодом, которые работают с хранилищем озера данных Azure](data-lake-store-compatible-oss-other-applications.md)
