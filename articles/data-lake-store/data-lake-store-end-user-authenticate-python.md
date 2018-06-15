---
title: Аутентификация пользователей в Data Lake Store с помощью Azure Active Directory и Python | Документация Майкрософт
description: Узнайте, как реализовать аутентификацию пользователей в Data Lake Store с помощью Azure Active Directory и Python
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
ms.openlocfilehash: 60cdcc2c12272b48d61de0afcdd3c361f1795f37
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624120"
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Аутентификация пользователей в Data Lake Store с помощью Python
> [!div class="op_single_selector"]
> * [С использованием Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Использование пакета SDK для .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Использование Python](data-lake-store-end-user-authenticate-python.md)
> * [Использование REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

В этой статье описывается, как использовать пакет SDK для Python для аутентификации пользователей с помощью Azure Data Lake Store. Аутентификация пользователей бывает двух типов:

* Аутентификация пользователей без многофакторной проверки подлинности.
* Аутентификация пользователей с многофакторной проверкой подлинности.

В этой статье рассматриваются оба типа. См. дополнительные сведения [аутентификации между службами с помощью Data Lake Store и Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>предварительным требованиям

* **Python**. Скачать Python можно [здесь](https://www.python.org/downloads/). В этой статье используется версия Python 3.6.2.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Создание собственного приложения Active Directory** Вам нужно выполнить инструкции по [аутентификации пользователей в Data Lake Store с помощью Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

1. Для создания приложения Python используйте любую IDE, например **mysample.py**.

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
    import logging, pprint, uuid, time
    ```

3. Сохраните изменения в mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Аутентификация пользователей с многофакторной проверкой подлинности.

### <a name="for-account-management"></a>Управление учетными записями

Используйте следующий фрагмент кода для аутентификации в Azure AD и выполнения операций управления учетными записями в Data Lake Store. Следующий фрагмент кода можно использовать для проверки подлинности приложения с помощью многофакторной идентификации. Укажите следующие значения для существующего **собственного** приложения Azure AD.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)

### <a name="for-filesystem-operations"></a>Операции файловой системы

Используйте для аутентификации в Azure AD и выполнения операций файловой системы в учетной записи Data Lake Store. Следующий фрагмент кода можно использовать для проверки подлинности приложения с помощью многофакторной идентификации. Укажите следующие значения для существующего **собственного** приложения Azure AD.

    adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Аутентификация пользователей без многофакторной проверки подлинности.

Мы не рекомендуем использовать этот метод. Дополнительные сведения см. в руководстве по [аутентификации в Azure с помощью пакета SDK для Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Дополнительная информация
В этой статье описывается, как использовать аутентификацию пользователей, чтобы реализовать аутентификацию с помощью Azure Data Lake Store и Python. Дополнительные сведения об использовании Python для работы с Azure Data Lake Store см. в следующих статьях.

* [Операции управления учетными записями в Azure Data Lake Store с использованием Python](data-lake-store-get-started-python.md)
* [Операции файловой системы в Azure Data Lake Store с использованием REST API](data-lake-store-data-operations-python.md)

