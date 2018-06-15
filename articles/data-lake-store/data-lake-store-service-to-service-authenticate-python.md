---
title: Аутентификация между службами в Data Lake Store с помощью Azure Active Directory и Python | Документация Майкрософт
description: Узнайте, как реализовать аутентификацию между службами в Data Lake Store с помощью Azure Active Directory и Python
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: f2ebd9228b9fa7861527dab20277ceb09f61b544
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626238"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Аутентификация между службами в Data Lake Store с помощью Python
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Использование пакета SDK для .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Использование REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

В этой статье описывается, как использовать пакет SDK для Python для аутентификации между службами с помощью Azure Data Lake Store. Дополнительные сведения об аутентификации пользователей с помощью Data Lake Store и Python см. в [этой статье](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>предварительным требованиям

* **Python**. Скачать Python можно [здесь](https://www.python.org/downloads/). В этой статье используется версия Python 3.6.2.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создайте веб-приложение Azure Active Directory**. Вам нужно выполнить инструкции по [аутентификации между службами в Data Lake Store с помощью Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

2. Чтобы импортировать необходимые модули, добавьте следующий фрагмент кода.

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Сохраните изменения в mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Проверки подлинности через секрет клиента со взаимодействием между службами для управления учетными записями

Используйте этот фрагмент кода для проверки подлинности в Azure AD для операций управления учетными записями в Data Lake Store, таких как создание или удаление учетной записи Data Lake Store. Следующий фрагмент можно использовать для проверки подлинности приложения в неинтерактивном режиме с помощью секрета клиента для субъекта-приложения или субъекта-службы в существующем веб-приложении Azure AD.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    RESOURCE = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(RESOURCE, client_id, client_secret)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource=RESOURCE)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Проверка подлинности между службами через секрет клиента для операций файловой системы

Следующий фрагмент используется для проверки подлинности в Azure AD для операций файловой системы в Data Lake Store (создание папки, отправка файла и т. д.). Следующий фрагмент можно использовать для проверки подлинности приложения в неинтерактивном режиме с помощью секрета клиента или субъекта-службы. Примените этот фрагмент кода к существующему веб-приложению Azure AD.

    tenant = '<TENANT>'
    RESOURCE = 'https://datalake.azure.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'
    
    adlCreds = lib.auth(tenant_id = tenant,
                    client_secret = client_secret,
                    client_id = client_id,
                    resource = RESOURCE)

<!-- ## Service-to-service authentication with certificate for account management

Use this snippet to authenticate with Azure AD for account management operations on Data Lake Store such as create Data Lake Store account, delete Data Lake Store account, etc. The following snippet can be used to authenticate your application non-interactively, using the certificate of an existing Azure AD "Web App" application. For instructions on how to create an Azure AD application, see [Create service principal with certificates](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id) -->

## <a name="next-steps"></a>Дополнительная информация
В этой статье описывается, как использовать аутентификацию между службами, чтобы реализовать аутентификацию с помощью Azure Data Lake Store и Python. Дополнительные сведения об использовании Python для работы с Azure Data Lake Store см. в следующих статьях.

* [Операции управления учетными записями в Azure Data Lake Store с использованием Python](data-lake-store-get-started-python.md)
* [Операции файловой системы в Azure Data Lake Store с использованием REST API](data-lake-store-data-operations-python.md)


