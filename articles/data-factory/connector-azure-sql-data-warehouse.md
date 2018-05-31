---
title: Копирование данных в хранилище данных SQL Azure и из него с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ в хранилище данных SQL Azure или из хранилища данных SQL в поддерживаемые хранилища-приемники с помощью фабрики данных.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/05/2018
ms.author: jingwang
ms.openlocfilehash: 9ba48a9072a85e7d8e6e9fb17957efbf27711df8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886856"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Копирование данных в хранилище данных Azure SQL и из него с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Версия 2 — предварительная](connector-azure-sql-data-warehouse.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из хранилища данных SQL и в него. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Copy data to or from Azure SQL Data Warehouse by using Azure Data Factory](v1/data-factory-azure-sql-data-warehouse-connector.md) (Копирование данных в хранилище данных SQL или из него с помощью фабрики данных Azure).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать в хранилище данных SQL Azure и из него в любой поддерживаемый приемник хранилища данных из любого исходного хранилища данных в хранилище данных SQL Azure. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель хранилища данных SQL Azure поддерживает:

- копирование данных с использованием **проверки подлинности SQL** и **проверки подлинности по маркерам приложения Azure Active Directory** с субъектом-службой или управляемым удостоверением службы (MSI);
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- загрузку данных с использованием **PolyBase** или массовой вставки (в качестве приемника). Для лучшей производительности копирования **рекомендуется** использовать первый вариант.

> [!IMPORTANT]
> Примечание. PolyBase поддерживают только проверку подлинности SQL, но не проверку подлинности Azure Active Directory.

> [!IMPORTANT]
> При копировании данных с использованием среды Azure Integration Runtime настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он [разрешал службам Azure получать доступ к серверу](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). При копировании данных с использованием локальной среды Integration Runtime настройте брандмауэр Azure SQL Server таким образом, чтобы он разрешал соответствующий диапазон IP-адресов, включая IP-адрес компьютера, используемый для подключения к базе данных SQL Azure.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю хранилища данных SQL Azure.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы хранилища данных SQL Azure поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureSqlDW** | Yes |
| connectionString |Укажите сведения, необходимые для подключения к экземпляру хранилища данных SQL Azure, для свойства connectionString. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
| servicePrincipalId | Укажите идентификатора клиента приложения. | Значение Yes при использовании проверки подлинности AAD на основе субъекта-службы. |
| servicePrincipalKey | Укажите ключ приложения. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Значение Yes при использовании проверки подлинности AAD на основе субъекта-службы. |
| tenant | Укажите сведения о клиенте (доменное имя или идентификатор клиента), в котором находится приложение. Эти сведения можно получить, наведя указатель мыши на правый верхний угол страницы портала Azure. | Значение Yes при использовании проверки подлинности AAD на основе субъекта-службы. |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать среду выполнения интеграции Azure или локальную среду IR (если хранилище данных расположено в частной сети). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

В разделах ниже описываются требования и приводятся примеры JSON для разных типов проверки подлинности:

- [Использование проверки подлинности SQL](#using-sql-authentication)
- [Использование проверки подлинности по маркеру приложения AAD — субъект-служба](#using-service-principal-authentication)
- [Использование проверки подлинности по маркеру приложения AAD — управляемое удостоверение службы](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Использование проверки подлинности SQL

**Пример использования проверки подлинности SQL в связанной службе:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-principal-authentication"></a>Использование аутентификации на основе субъекта-службы

Чтобы использовать проверку подлинности по маркеру приложения AAD на основе субъекта-службы, выполните следующие действия:

1. **[Создайте приложение Azure Active Directory на портале Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Запишите имя приложения и следующие значения, которые используются для определения связанной службы:

    - Идентификатор приложения
    - Ключ приложения
    - Tenant ID

2. **[Подготовьте администратор Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для сервера Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратор AAD может быть пользователем AAD или группой AAD. При предоставлении группе с MSI роли администратора пропустите шаги 3 и 4, так как администратор будет иметь полный доступ к базе данных.

3. **Создайте пользователя автономной базы данных для субъекта-службы**. Для этого подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные, с помощью таких средств, как среда SSMS, используя идентификатор AAD по крайней мере с разрешением ALTER ANY USER, и выполните следующий сценарий T-SQL. Дополнительные сведения о пользователе автономной базы данных см. [здесь](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL. Например, выполните эту команду:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Настройте в ADF связанную службу хранилища данных SQL Azure.


**Пример использования аутентификации на основе субъекта-службы в связанной службе**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-managed-service-identity-authentication"></a>Использование аутентификации на основе управляемого удостоверения службы

Фабрика данных может быть связана с [управляемым удостоверением службы (MSI)](data-factory-service-identity.md), которое представляет это решение. Это удостоверение службы можно использовать для проверки подлинности хранилища данных SQL Azure, которое позволяет этой назначенной фабрике получать доступ к хранилищу данных и копировать данные из него и в него.

> [!IMPORTANT]
> Обратите внимание, что в настоящее время PolyBase не поддерживается для проверки подлинности по MSI.

Чтобы использовать проверку подлинности по маркеру приложения AAD на основе управляемого удостоверения службы, выполните следующие действия:

1. **Создайте группу в Azure AD и сделайте MSI фабрики ее участником**.

    a. Найдите удостоверение службы фабрики данных на портале Azure. Перейдите к фабрике данных, выберите "Свойства" и скопируйте **идентификатор удостоверения службы**.

    Б. Установите модуль [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), войдите в него с помощью команды `Connect-AzureAD` и выполните следующие команды, чтобы создать группу и добавить MSI фабрики данных в качестве ее участника.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Подготовьте администратор Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для сервера Azure SQL Server на портале Azure (если вы этого еще не сделали).

3. **Создайте пользователя автономной базы данных для группы AAD**. Для этого подключитесь к хранилищу данных, из которого или в которое требуется скопировать данные, с помощью таких средств, как среда SSMS, используя идентификатор AAD по крайней мере с разрешением ALTER ANY USER, и выполните следующий сценарий T-SQL. Дополнительные сведения о пользователе автономной базы данных см. [здесь](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте группе AAD необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL. Например, выполните эту команду:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. Настройте в ADF связанную службу хранилища данных SQL Azure.

**Пример использования проверки подлинности на основе MSI в связанной службе:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных хранилища данных SQL Azure.

Чтобы скопировать данные в хранилище данных SQL Azure или из него, задайте для свойства type набора данных значение **AzureSqlDWTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение: **AzureSqlDWTable**. | Yes |
| tableName |Имя таблицы или представления в экземпляре хранилища данных SQL Azure, на которое ссылается связанная служба. | Yes |

**Пример.**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником хранилища данных SQL Azure.

### <a name="azure-sql-data-warehouse-as-source"></a>Хранилище данных SQL Azure в качестве источника

Чтобы скопировать данные из хранилища данных SQL Azure, задайте тип источника **SqlDWSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **SqlDWSource**. | Yes |
| SqlReaderQuery |Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. |Нет  |
| sqlReaderStoredProcedureName |Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |Нет  |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет  |

**Примечания:**

- Если для SqlSource указано **sqlReaderQuery**, то действие копирования выполняет этот запрос для хранилища данных SQL Azure с целью получения данных. Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).
- Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для создания запроса (`select column1, column2 from mytable`) к хранилищу данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.
- При использовании **sqlReaderStoredProcedureName** по-прежнему необходимо указать фиктивное свойство **tableName** в наборе данных JSON.

**Пример. Использование SQL-запроса**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Пример. Использование хранимой процедуры**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Определение хранимой процедуры:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a>Хранилище данных SQL Azure в качестве приемника

Чтобы скопировать данные в хранилище данных SQL Azure, задайте тип приемника **SqlDWSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **SqlDWSink**. | Yes |
| allowPolyBase |Указывает, следует ли использовать PolyBase (если применимо) вместо механизма BULKINSERT. <br/><br/> **Использование PolyBase — рекомендуемый способ загрузки данных в хранилище данных SQL.** Подробные сведения и ограничения приведены в разделе [Загрузка данных в хранилище данных SQL Azure с помощью PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>Допустимые значения: **true** (по умолчанию) и **false**.  |Нет  |
| polyBaseSettings |Группа свойств, которые можно задать, если свойство **allowPolybase** имеет значение **true**. |Нет  |
| rejectValue |Указывает количество или процент строк, которые могут быть отклонены, прежде чем запрос завершится с ошибкой.<br/><br/>Дополнительные сведения о параметрах отклонения PolyBase см. в подразделе **Аргументы** раздела [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>Допустимые значения: 0 (по умолчанию), 1, 2, ... |Нет  |
| rejectType |Указывает тип значения, заданного для параметра rejectValue: литеральное значение или процент.<br/><br/>Допустимые значения: **Значение** (по умолчанию) и **Процент**. |Нет  |
| rejectSampleValue |Определяет количество строк, которое PolyBase следует получить до повторного вычисления процента отклоненных строк.<br/><br/>Допустимые значения: 1, 2, … |Да, если **rejectType** имеет значение **percentage**. |
| useTypeDefault |Указывает способ обработки отсутствующих значений в текстовых файлах с разделителями, когда PolyBase получает данные из текстового файла.<br/><br/>Дополнительные сведения об этом свойстве см. в подразделе "Аргументы" раздела [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>Допустимые значения: **true**, **false** (по умолчанию). |Нет  |
| writeBatchSize |Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize. Применимо, только если не используется PolyBase.<br/><br/>Допустимые значения: целое число (количество строк). |Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия. Применимо, только если не используется PolyBase.<br/><br/>Допустимые значения: промежуток времени. Пример: 00:30:00 (30 минут). |Нет  |
| preCopyScript |Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в хранилище данных SQL Azure при каждом выполнении. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет  |(#repeatability-during-copy). |Инструкция запроса. |Нет  |

**Пример.**

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

В следующем разделе можно узнать о том, как использовать PolyBase для эффективной загрузки в хранилище данных SQL.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных SQL Azure с помощью PolyBase.

Применение **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** — это эффективный способ загрузки большого объема данных в хранилище данных SQL Azure с высокой пропускной способностью. Используя PolyBase вместо стандартного механизма BULKINSERT, можно значительно увеличить пропускную способность. Подробное сравнение см. в разделе [Базовые показатели производительности](copy-activity-performance.md#performance-reference). Пошаговое руководство и пример использования см. в статье [Загрузка 1 ТБ в хранилище данных SQL Azure с помощью фабрики данных Azure [мастер копирования] менее чем за 15 минут](connector-azure-sql-data-warehouse.md).

* Если исходные данные находятся в **хранилище BLOB-объектов Azure или в Azure Data Lake Store** и их формат соответствует требованиям PolyBase, то копирование можно выполнять напрямую в хранилище данных SQL Azure, используя PolyBase. Дополнительные сведения см. в разделе **[Прямое копирование с помощью PolyBase](#direct-copy-using-polybase)**.
* Если хранилище и формат исходных данных изначально не поддерживаются PolyBase, то можно использовать функцию **[промежуточного копирования с помощью PolyBase](#staged-copy-using-polybase)**. Она также обеспечивает лучшую пропускную способность за счет автоматического преобразования данных в формат, совместимый с PolyBase, и хранения данных в хранилище BLOB-объектов Azure. После этого данные загружаются в хранилище данных SQL.

> [!IMPORTANT]
> Обратите внимание, что в настоящее время PolyBase не поддерживается для проверки подлинности по маркерам приложения Azure Active Directory на основе управляемых удостоверений службы (MSI).

### <a name="direct-copy-using-polybase"></a>Прямое копирование с помощью PolyBase

PolyBase для хранилища данных SQL напрямую поддерживает хранилище BLOB-объектов Azure и Azure Data Lake Store (с использованием субъекта-службы) в качестве источника и с определенными требованиями к формату файлов. Если исходные данные соответствуют критериям, описанным в этом разделе, можно выполнять копирование напрямую из исходного хранилища данных в хранилище данных SQL Azure, используя PolyBase. В противном случае можно использовать [промежуточное копирование с помощью PolyBase](#staged-copy-using-polybase).

> [!TIP]
> Дополнительные сведения об эффективном копировании данных из Data Lake Store в хранилище данных SQL см. в записи блога [Фабрика данных Azure упрощает и делает более удобным анализ данных при использовании Data Lake Store с хранилищем данных SQL](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Если требования не выполняются, фабрика данных Azure проверяет параметры и автоматически возвращается к механизму перемещения данных BULKINSERT.

1. Тип **связанной службы источника** — **AzureStorage** или **AzureDataLakeStore** с проверкой подлинности на основе субъекта-службы.
2. Тип **входного набора данных** — **AzureBlob** или **AzureDataLakeStoreFile**, тип формата в свойствах `type` — **OrcFormat**, **ParquetFormat** или **TextFormat** со следующими конфигурациями.

   1. Параметр `rowDelimiter` должен иметь значение **\n**.
   2. Параметр `nullValue` должен быть **пустой строке** (""), или параметру `treatEmptyAsNull` присваивается значение **true**.
   3. Параметру `encodingName` присваивается значение **utf-8**, которое является значением **по умолчанию**.
   4. Параметры `escapeChar`, `quoteChar`, `firstRowAsHeader` и `skipLineCount` не указываются.
   5. Параметр `compression` может иметь значение **no compression**, **GZip** или **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

3. В разделе **BlobSource** или **AzureDataLakeStore** для действия копирования в конвейере отсутствует параметр `skipHeaderLineCount`.
4. В разделе **SqlDWSink** для действия копирования в конвейере отсутствует параметр `sliceIdentifierColumnName`. PolyBase гарантирует, что за один цикл выполнения либо все данные будут обновлены, либо ничего не будет обновлено. Чтобы обеспечить **воспроизводимость**, используйте свойство `sqlWriterCleanupScript`).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>промежуточное копирование с помощью PolyBase

Если исходные данные не соответствуют критериям, описанным в предыдущем разделе, вы можете включить копирование данных через промежуточное хранилище BLOB-объектов Azure (хранилище класса Premium не поддерживается). В таком случае в фабрике данных Azure данные будут преобразованы и приведены в соответствие с требованиями к формату PolyBase, переданы в хранилище данных SQL с помощью PolyBase, а затем временные данные будут очищены из хранилища BLOB-объектов. Подробные сведения о том, как работает копирование данных через промежуточное хранилище BLOB-объектов Azure, см. в разделе [Промежуточное копирование](copy-activity-performance.md#staged-copy).

Чтобы использовать эту функцию, создайте [связанную службу службы хранилища Azure](connector-azure-blob-storage.md#linked-service-properties), относящуюся к учетной записи хранения Azure, которая содержит это промежуточное хранилище BLOB-объектов. Затем укажите свойства `enableStaging` и `stagingSettings` для действия копирования, как показано в следующем коде:

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-when-using-polybase"></a>Рекомендации по использованию PolyBase

В следующих разделах приведены практические рекомендации дополнительно к указанным в разделе [Рекомендации по использованию хранилища данных SQL Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Необходимые разрешения базы данных

Чтобы отправить данные в хранилище данных SQL с помощью PolyBase, пользователь, используемый для этого процесса, должен иметь [разрешение CONTROL](https://msdn.microsoft.com/library/ms191291.aspx) в целевой базе данных. Это разрешение можно получить, добавив этого пользователя как участника роли db_owner. Дополнительные сведения см. в [этом разделе](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limitation"></a>Ограничение размера строки и типа данных

Нагрузки PolyBase ограничиваются загрузкой строк меньше **1 МБ** и не позволяют загружать данные типа VARCHAR(MAX), NVARCHAR(MAX) или VARBINARY(MAX). Дополнительные сведения см. [здесь](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Если источник данных содержит строки больше 1 МБ, вы можете разделить исходные таблицы по вертикали на несколько небольших таблиц, для каждой из которых максимальный размер строки не будет превышать это ограничение. Затем эти небольшие таблицы можно загрузить с помощью PolyBase и объединить в хранилище данных SQL Azure.

### <a name="sql-data-warehouse-resource-class"></a>Класс ресурсов хранилища данных SQL

Чтобы получить наилучшую пропускную способность, мы рекомендуем присвоить пользователю, используемому для отправки данных в хранилище данных SQL через PolyBase, более высокий класс ресурсов.

### <a name="tablename-in-azure-sql-data-warehouse"></a>Использование tableName в хранилище данных SQL Azure

В следующей таблице показаны правильные значения свойства **tableName** в наборе данных JSON для разных сочетаний имен таблиц и схем.

| Схема базы данных | Имя таблицы | Свойство tableName в JSON |
| --- | --- | --- |
| dbo |MyTable |MyTable или dbo.MyTable либо [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable или [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] или [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

Если вы увидите приведенное ниже сообщение об ошибке, это может указывать на неправильное значение свойства tableName. Правильные значения для свойства tableName в JSON см. в таблице выше.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Столбцы со значениями по умолчанию

Сейчас для PolyBase в фабрике данных требуется такое же количество столбцов, как и в целевой таблице. Предположим, у вас есть таблица с четырьмя столбцами и один из них определен со значением по умолчанию. Входные данные по-прежнему должны содержать четыре столбца. Если входной набор данных будет содержать 3 столбца, мы получим ошибку, похожую на следующую.

```
All columns of the table must be specified in the INSERT BULK statement.
```

Значение NULL рассматривается как вариант значения по умолчанию. Если столбец допускает значения NULL, входные значения (в большом двоичном объекте) для этого столбца могут быть пустыми (но не могут отсутствовать во входном наборе данных). Для пустых значений PolyBase будет использовать значение NULL в хранилище данных SQL Azure.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Сопоставление типов данных для хранилища данных SQL Azure

При копировании данных в хранилище данных SQL Azure или из него используются следующие сопоставления между типами данных хранилища данных SQL Azure и временными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных хранилища данных SQL Azure | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| bigint |Int64 |
| binary; |Byte[] |
| bit |Логическое |
| char; |String, Char[] |
| дата |Datetime |
| DateTime |Datetime |
| datetime2; |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| Атрибут FILESTREAM (varbinary(max)) |Byte[] |
| Float |Double |
| изображение |Byte[] |
| int |Int32 |
| money; |Decimal |
| nchar; |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar; |String, Char[] |
| real; |Single |
| rowversion |Byte[] |
| smalldatetime; |Datetime |
| smallint; |Int16 |
| smallmoney; |Decimal |
| sql_variant |Object * |
| текст |String, Char[] |
| Twitter в режиме реального |Интервал времени |
| timestamp |Byte[] |
| tinyint; |Byte |
| uniqueidentifier |Guid |
| varbinary; |Byte[] |
| varchar. |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md##supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.