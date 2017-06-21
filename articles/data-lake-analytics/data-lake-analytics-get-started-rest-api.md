---
title: "Начало работы с Data Lake Analytics с помощью интерфейсов REST API | Документация Майкрософт"
description: "Использование интерфейсов REST API WebHDFS для выполнения операций в Data Lake Analytics"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: 332d7af2539eea8890745005104ac5b0921c2b7f
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Начало работы с Azure Data Lake Analytics с помощью интерфейсов REST API
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как использовать интерфейсы REST API WebHDFS и Data Lake Analytics, чтобы управлять учетными записями, заданиями и каталогом Data Lake Analytics. 

## <a name="prerequisites"></a>Предварительные требования
* **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Создание приложения Azure Active Directory**. Это приложение будет использоваться для проверки подлинности приложения Data Lake Analytics в Azure AD. Есть разные способы проверки подлинности приложения с помощью Azure AD: **проверка подлинности пользователя** и **проверка подлинности со взаимодействием между службами**. Инструкции и дополнительные сведения о проверке подлинности см. в статье [Аутентификация в Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).
* [cURL](http://curl.haxx.se/). В этой статье для демонстрации вызовов REST API к учетной записи Data Lake Analytics используется cURL.

## <a name="authenticate-with-azure-active-directory"></a>Проверка подлинности с помощью Azure Active Directory
Существует два метода проверки подлинности с помощью Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Проверка подлинности пользователя (интерактивная)
Если используется этот метод, в приложении пользователю предлагается войти в систему, и все операции выполняются в контексте пользователя. 

Выполните приведенные ниже действия, чтобы использовать интерактивную проверку подлинности.

1. В приложении перенаправьте пользователя на следующий URL-адрес.
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<<REDIRECT-URI> должен быть закодирован для использования в URL-адресе. Поэтому для https://localhost используйте `https%3A%2F%2Flocalhost`.
   > 
   > 
   
    В целях обучения можно заменить значения заполнителей в URL-адресе выше и вставить его в адресную строку веб-браузера. Вы перейдете на страницу аутентификации с помощью учетной записи Azure. После входа в систему вы увидите ответ в адресной строке браузера. Ответ имеет следующий формат.
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Запишите код авторизации из ответа. В этом учебнике вы можете скопировать код авторизации из адресной строки веб-браузера и передать его в запрос POST к конечной точке маркера, как показано ниже.
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > В этом случае кодировать \<REDIRECT-URI> не нужно.
   > 
   > 
3. Ответ является объектом JSON, содержащим маркер доступа (например, `"access_token": "<ACCESS_TOKEN>"`) и маркер обновления (например, `"refresh_token": "<REFRESH_TOKEN>"`). Приложение использует маркер доступа для обращения к хранилищу озера данных Azure, а маркер обновления — для получения другого маркера доступа, когда срок действия текущего маркера доступа истечет.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. По истечении срока действия маркера доступа можно запросить новый маркер доступа, используя маркер обновления, как показано ниже.
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

Дополнительные сведения об интерактивной проверке подлинности пользователей см. в статье [Авторизация доступа к веб-приложениям с помощью OAuth 2.0 и Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Проверка подлинности с взаимодействием между службами (неинтерактивная)
Если используется этот метод, приложение предоставляет свои собственные учетные данные для выполнения операций. В этом случае необходимо отправить запрос POST, аналогичный показанному ниже. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Выходные данные этого запроса будут содержать маркер авторизации (обозначен `access-token` в приведенных ниже выходных данных) для дальнейшей передачи в вызовах REST API. Сохраните этот маркер в текстовый файл. Он потребуется в данной статье позднее.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

В этой статье используется **неинтерактивный** подход. Дополнительные сведения о неинтерактивном подходе (вызовы между службами) см. в [этой статье](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных
Перед тем как создать учетную запись Data Lake Analytics, необходимо создать группу ресурсов Azure и учетную запись Data Lake Store.  Дополнительные сведения см. в разделе [Создание учетной записи Data Lake Store](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

Чтобы создать учетную запись, используйте следующую команду cURL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Замените \<`REDACTED`\> маркером авторизации, \<`AzureSubscriptionID`\> — идентификатором подписки, \<`AzureResourceGroupName`\> — именем существующей группы ресурсов Azure, а \<`NewAzureDataLakeAnalyticsAccountName`\> — новым именем учетной записи Data Lake Analytics. Полезные данные запроса для этой команды находятся в файле **CreateDatalakeAnalyticsAccountRequest.json**, предоставленном для приведенного выше параметра `-d`. Содержимое файла input.json выглядит следующим образом:

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Список учетных записей Data Lake Analytics в подписке
Чтобы получить список учетных записей в подписке, используйте следующую команду cURL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Замените \<`REDACTED`\> маркером авторизации, а \<`AzureSubscriptionID`\> — идентификатором подписки. Выходные данные должны быть следующего вида.

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Получение сведений об учетной записи Data Lake Analytics
Чтобы получить сведения об учетной записи, используйте следующую команду cURL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Замените \<`REDACTED`\> маркером авторизации, \<`AzureSubscriptionID`\> — идентификатором подписки, \<`AzureResourceGroupName`\> — именем существующей группы ресурсов Azure, а \<`DataLakeAnalyticsAccountName`\> — именем существующей учетной записи Data Lake Analytics. Выходные данные должны быть следующего вида.

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Список хранилищ Data Lake Store учетной записи Data Lake Analytics
Чтобы получить список хранилищ Data Lake Store учетной записи, используйте следующую команду cURL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Замените \<`REDACTED`\> маркером авторизации, \<`AzureSubscriptionID`\> — идентификатором подписки, \<`AzureResourceGroupName`\> — именем существующей группы ресурсов Azure, а \<`DataLakeAnalyticsAccountName`\> — именем существующей учетной записи Data Lake Analytics. Выходные данные должны быть следующего вида.

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Отправка заданий U-SQL
Чтобы отправить задание U-SQL, используйте следующую команду cURL:

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Замените \<`REDACTED`\> маркером авторизации, а \<`DataLakeAnalyticsAccountName`\> — именем существующей учетной записи Data Lake Analytics. Полезные данные запроса для этой команды находятся в файле **SubmitADLAJob.json**, предоставленном для приведенного выше параметра `-d`. Содержимое файла input.json выглядит следующим образом:

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

Выходные данные должны быть следующего вида.

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Получение списка заданий U-SQL
Чтобы получить список заданий U-SQL, используйте следующую команду cURL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Замените \<`REDACTED`\> маркером авторизации, а \<`DataLakeAnalyticsAccountName`\> — именем существующей учетной записи Data Lake Analytics. 

Выходные данные должны быть следующего вида.

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Получение элементов каталога
Чтобы получить базы данных из каталога, используйте следующую команду cURL:

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

Выходные данные должны быть следующего вида.

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Дополнительные материалы
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
* Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
* Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).
* Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.


