---
title: "REST API. Операции файловой системы в Azure Data Lake Store | Документация Майкрософт"
description: "Использование интерфейсов REST API WebHDFS для выполнения операций файловой системы в Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/28/2017
ms.author: nitinme
ms.openlocfilehash: 62cfc713c3b7ce8f7c0a7ff4dd5e80802f1843b7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2017
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-rest-api"></a>Операции файловой системы в Azure Data Lake Store с использованием REST API
> [!div class="op_single_selector"]
> * [ПАКЕТ SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
> * [ИНТЕРФЕЙС REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

В этой статье содержатся сведения об использовании интерфейсов REST API WebHDFS и REST API Data Lake Store для выполнения операций файловой системы в Azure Data Lake Store. Дополнительные сведения о том, как выполнять операции управления учетными записями в Data Lake Store с помощью REST API, см. в статье [Начало работы с Azure Data Lake Store с помощью интерфейсов REST API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Учетная запись хранилища озера данных Azure**. Следуйте инструкциям в разделе [Начало работы с Azure Data Lake Store с помощью портала Azure](data-lake-store-get-started-portal.md).

* **[cURL](http://curl.haxx.se/)**. В этой статье для демонстрации вызовов REST API к учетной записи хранения озера данных используется cURL.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Как выполнить аутентификацию с помощью Azure Active Directory?
Существует два способа проверки подлинности с помощью Azure Active Directory.

* Дополнительные сведения о проверке подлинности пользователей в приложении (интерактивно) см. в статье [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-rest-api.md) (Аутентификация пользователей в Data Lake Store с использованием пакета SDK для .NET).
* Дополнительные сведения о проверке подлинности между службами в приложении (интерактивно) см. в статье [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md) (Аутентификация между службами в Data Lake Store с использованием пакета SDK для .NET).


## <a name="create-folders"></a>Создайте папки:
Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

В приведенной выше команде замените \<`REDACTED`\> полученным ранее маркером авторизации. Эта команда создает каталог с именем **mytempdir** в корневой папке учетной записи Data Lake Store.

Если работа приложения завершится успешно, отобразится результат, подобный приведенному ниже фрагменту кода:

    {"boolean":true}

## <a name="list-folders"></a>Вывод списка папок
Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

В приведенной выше команде замените \<`REDACTED`\> полученным ранее маркером авторизации.

Если работа приложения завершится успешно, отобразится результат, подобный приведенному ниже фрагменту кода:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Отправка данных
Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

В предыдущем синтаксисе параметр **-T** представляет расположение отправляемого файла.

Вывод аналогичен приведенному ниже фрагменту кода:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Считывание данных
Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Процесс чтения данных из учетной записи хранения озера данных состоит из двух этапов.

* Сначала следует отправить запрос GET к конечной точке `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Будет возвращено расположение для отправки следующего запроса GET.
* Затем нужно отправить запрос GET к конечной точке `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Будет отображено содержимое файла.

Однако поскольку на первом и втором этапе применяются одинаковые входные параметры, для отправки первого запроса можно использовать параметр `-L`. `-L` фактически объединяет два запроса в один, а также позволяет cURL повторно отправить запрос к новому расположению. И наконец, отображаются выходные данные всех вызовов запросов, как показано в следующем фрагменте кода. Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Должен отобразиться результат, аналогичный приведенному ниже фрагменту кода.

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Переименование файла
Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Чтобы переименовать файл, используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Должен отобразиться результат, аналогичный приведенному ниже фрагменту кода.

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Удаление файла
Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Чтобы удалить файл, используйте следующую команду cURL: Замените **\<yourstorename>** именем своего хранилища Data Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Вы должны увидеть подобные выходные данные:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Дальнейшие действия
* [Начало работы с Azure Data Lake Store с помощью интерфейсов REST API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>См. также
* [Справочник по REST API для Azure Data Lake Store](https://docs.microsoft.com/rest/api/datalakestore/)
* [Приложения больших данных с открытым исходным кодом, которые работают с хранилищем озера данных Azure](data-lake-store-compatible-oss-other-applications.md)

