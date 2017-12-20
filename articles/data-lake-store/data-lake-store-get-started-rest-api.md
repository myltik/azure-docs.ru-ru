---
title: "REST API. Операции управления учетными записями в Azure Data Lake Store | Документация Майкрософт"
description: "Используйте Azure Data Lake Store и REST API WebHDFS, чтобы выполнять операции управления учетными записями в Data Lake Store."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 6c43f2b341280731707e486ba6f22f11560102c6
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Операции управления учетными записями в Azure Data Lake Store с использованием REST API
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

В этой статье содержатся сведения о выполнении операций управления учетными записями в Data Lake Store с использованием REST API. Операции управления учетными записями включают в себя создание учетной записи Data Lake Store, удаление учетной записи Data Lake Store и т. д. Дополнительные сведения о том, как выполнять операции файловой системы в Data Lake Store с помощью REST API, см. в [этой статье](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. В этой статье для демонстрации вызовов REST API к учетной записи хранения озера данных используется cURL.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Как выполнить аутентификацию с помощью Azure Active Directory?
Существует два способа проверки подлинности с помощью Azure Active Directory.

* Дополнительные сведения о проверке подлинности пользователей в приложении (интерактивно) см. в статье [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-rest-api.md) (Аутентификация пользователей в Data Lake Store с использованием пакета SDK для .NET).
* Дополнительные сведения о проверке подлинности между службами в приложении (интерактивно) см. в статье [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md) (Аутентификация между службами в Data Lake Store с использованием пакета SDK для .NET).


## <a name="create-a-data-lake-store-account"></a>Создание учетной записи хранения озера данных
Эта операция основана на вызове REST API, определенном [здесь](https://msdn.microsoft.com/library/mt694078.aspx).

Используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

В приведенной выше команде замените \<`REDACTED`\> полученным ранее маркером авторизации. Полезные данные запроса для этой команды находятся в файле **input.json**, предоставленном для параметра `-d` выше. Содержимое файла input.json выглядит как следующий фрагмент кода:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Удаление учетной записи хранения озера данных
Эта операция основана на вызове REST API, определенном [здесь](https://msdn.microsoft.com/library/mt694075.aspx).

Чтобы удалить учетную запись хранилища озера данных, используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Вы должны увидеть выходные данные, подобные следующему фрагменту кода:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Дальнейшие действия
* [Операции файловой системы в Azure Data Lake Store с использованием REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>См. также
* [Справочник по REST API для Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Приложения больших данных с открытым исходным кодом, которые работают с хранилищем озера данных Azure](data-lake-store-compatible-oss-other-applications.md)

