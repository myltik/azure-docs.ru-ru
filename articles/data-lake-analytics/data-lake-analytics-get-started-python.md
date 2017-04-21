---
title: "Начало работы с Azure Data Lake Analytics с помощью Python | Документация Майкрософт"
description: "Сведения о том, как создать учетную запись Data Lake Store и отправлять задания с помощью Python. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4
ms.lasthandoff: 11/23/2016


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>Руководство. Начало работы с Azure Data Lake Analytics с использованием Python
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Узнайте, как с помощью Python создавать учетные записи Azure Data Lake Analytics, определять задания Data Lake Analytics в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять задания в учетные записи Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

В этом руководстве вам предстоит разработать задание, которое считывает файл с разделителями-табуляциями (TSV) и преобразует его в файл с разделителями-запятыми (CSV). Для навигации по учебнику с помощью других поддерживаемых средств используйте вкладки в верхней части этого раздела.

##<a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством необходимо иметь следующее:

- **Подписка Azure**. Ознакомьтесь с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Приложение Azure Active Directory.** Это приложение будет использоваться для проверки подлинности приложения Data Lake Store в Azure AD. Есть разные способы проверки подлинности приложения с помощью Azure AD: проверка подлинности пользователя и проверка подлинности со взаимодействием между службами. Инструкции и дополнительные сведения о проверке подлинности см. в статье, посвященной [проверке подлинности приложений Data Lake Store с помощью Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).
- **[Python.](https://www.python.org/downloads/)** Необходимо использовать 64-разрядную версию. В этой статье используется версия Python 3.5.2.


## <a name="install-azure-python-sdk"></a>Установка пакета Azure SDK для Python

Для работы с Data Lake Store с использованием Python необходимо установить три модуля.

Модуль azure-mgmt-datalake-store содержит операции по управлению учетной записью Azure Data Lake Store. Модуль azure-mgmt-resource содержит другие модули Azure для Active Directory и др. Модуль azure-datalake-store содержит операции файловой системы Azure Data Lake Store. Модуль azure-datalake-analytics содержит операции Azure Data Lake Analytics. Чтобы установить модули, используйте следующие команды.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Создание приложения Python

1. Для создания приложения Python используйте IDE по своему усмотрению, например mysample.py.

2. Чтобы импортировать необходимые модули, добавьте следующие строки.

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Сохраните изменения в файле приложения Python.

## <a name="authentication"></a>Аутентификация

Для проверки подлинности используйте один из указанных ниже методов.

### <a name="end-user-authentication-for-account-management"></a>Проверка подлинности пользователя для управления учетными записями

Используйте этот метод проверки подлинности в Azure AD для выполнения операций управления учетными записями (создание, удаление учетной записи Data Lake Store и т. п.). Необходимо указать имя пользователя и пароль для Azure AD. Для учетной записи пользователя не должна быть настроена многофакторная идентификация. Кроме того, учетная запись пользователя не должна быть учетной записью Майкрософт или Live ID (например, @outlook.com, и @live.com.).

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Проверки подлинности через секрет клиента со взаимодействием между службами для управления учетными записями

Используйте этот метод проверки подлинности в Azure AD для выполнения операций управления учетными записями (создание, удаление учетной записи Data Lake Store и т. п.). Следующий фрагмент можно использовать для проверки подлинности приложения в неинтерактивном режиме с помощью секрета клиента или субъекта-службы. Примените этот фрагмент кода к существующему веб-приложению Azure AD.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>Создание группы управления ресурсами

Чтобы создать группу ресурсов Azure, используйте следующий фрагмент кода.

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Создание учетной записи Data Lake Store

Для каждой учетной записи Data Lake Analytics необходима учетная запись Data Lake Store. Инструкции см. в разделе [Создание учетной записи хранения Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account).



## <a name="create-data-lake-analytics-account"></a>Создание учетной записи аналитики озера данных

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>Отправка заданий аналитики озера данных

Задания аналитики озера данных пишутся на языке U-SQL. Дополнительные сведения о языке U-SQL см. в статье о [начале работы с языком U-SQL](data-lake-analytics-u-sql-get-started.md) и в [справочнике по языку U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>Дальнейшие действия

- Для просмотра учебника с помощью других средств используйте вкладки-селекторы в верхней части страницы.
- Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
- Чтобы приступить к разработке приложений U-SQL, ознакомьтесь со статьей [Разработка скриптов U-SQL с помощью средств озера данных для Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Для знакомства с U-SQL см. статью о [начале работы с языком U-SQL для Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Задачи управления описываются в руководстве по [управлению Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-manage-use-portal.md).
- Общие сведения об Azure Data Lake Analytics см. в [этой статье](data-lake-analytics-overview.md).


