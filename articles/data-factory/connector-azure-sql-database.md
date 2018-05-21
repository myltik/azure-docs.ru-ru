---
title: Копирование данных в базу данных SQL и из нее с помощью фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из поддерживаемых исходных хранилищ данных в базу данных SQL Azure или из базы данных SQL в поддерживаемые хранилища данных-приемники с помощью фабрики данных.
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
ms.openlocfilehash: 0503b355089fe6bbcc7632ac93fd21e71f268032
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-azure-sql-connector.md)
> * [Версия 2 — предварительная](connector-azure-sql-database.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные в базу данных SQL и из нее. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

> [!NOTE]
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если используется служба фабрики данных версии 1, которая является общедоступной версией, ознакомьтесь со статьей [Копирование данных в базу данных SQL Azure или из нее с помощью фабрики данных Azure](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные можно скопировать в базу данных SQL Azure и из нее в любое поддерживаемое хранилище данных-приемник, а также из любого исходного хранилища данных в базу данных SQL Azure. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Этот соединитель базы данных SQL Azure поддерживает:

- копирование данных с использованием **проверки подлинности SQL** и **проверки подлинности по маркерам приложения Azure Active Directory** с субъектом-службой или управляемым удостоверением службы (MSI);
- извлечение данных с использованием SQL-запроса или хранимой процедуры (в качестве источника);
- добавление данных в целевую таблицу или вызов хранимой процедуры во время копирования с помощью пользовательской логики (в качестве приемника).

> [!IMPORTANT]
> При копировании данных с использованием среды Azure Integration Runtime настройте [брандмауэр Azure SQL Server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) таким образом, чтобы он [разрешал службам Azure получать доступ к серверу](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). При копировании данных с использованием локальной среды Integration Runtime настройте брандмауэр Azure SQL Server таким образом, чтобы он разрешал соответствующий диапазон IP-адресов, включая IP-адрес компьютера, используемый для подключения к базе данных SQL Azure.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах, которые используются для определения сущностей фабрики данных, характерных для соединителя базы данных SQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы базы данных SQL Azure поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **AzureSqlDatabase** | Yes |
| connectionString |В свойстве connectionString указываются сведения, необходимые для подключения к экземпляру базы данных SQL Azure. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). |Yes |
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
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

2. **[Подготовьте администратор Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для сервера Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратор AAD должен быть пользователем AAD или группой AAD, но не может быть субъектом-службой. Этот шаг нужен, чтобы затем можно было использовать удостоверение AAD для создания пользователя автономной базы данных для субъекта-службы.

3. **Создайте пользователя автономной базы данных для субъекта-службы**. Для этого подключитесь к базе данных, из которой или в которую требуется скопировать данные, с помощью таких средств, как среда SSMS, используя удостоверение AAD по крайней мере с разрешением ALTER ANY USER, и выполните следующий сценарий T-SQL. Дополнительные сведения о пользователе автономной базы данных см. [здесь](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте субъекту-службе необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL. Например, выполните эту команду:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Настройте в ADF связанную службу базы данных SQL Azure.


**Пример использования аутентификации на основе субъекта-службы в связанной службе**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Фабрика данных может быть связана с [управляемым удостоверением службы (MSI)](data-factory-service-identity.md), которое представляет это решение. Это удостоверение службы можно использовать для проверки подлинности базы данных SQL Azure, что позволяет этой назначенной фабрике получать доступ к базе данных и копировать данные из нее и в нее.

Чтобы использовать проверку подлинности по маркеру приложения AAD на основе управляемого удостоверения службы, выполните следующие действия:

1. **Создайте группу в Azure AD и сделайте MSI фабрики ее участником**.

    a. Найдите удостоверение службы фабрики данных на портале Azure. Перейдите к фабрике данных, выберите "Свойства" и скопируйте **идентификатор удостоверения службы**.

    Б. Установите модуль [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), войдите в него с помощью команды `Connect-AzureAD` и выполните следующие команды, чтобы создать группу и добавить MSI фабрики данных в качестве ее участника.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Подготовьте администратор Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** для сервера Azure SQL Server на портале Azure (если вы этого еще не сделали). Администратор AAD может быть пользователем AAD или группой AAD. При предоставлении группе с MSI роли администратора пропустите шаги 3 и 4, так как администратор будет иметь полный доступ к базе данных.

3. **Создайте пользователя автономной базы данных для группы AAD**. Для этого подключитесь к базе данных, из которой или в которую требуется скопировать данные, с помощью таких средств, как среда SSMS, используя удостоверение AAD по крайней мере с разрешением ALTER ANY USER, и выполните следующий сценарий T-SQL. Дополнительные сведения о пользователе автономной базы данных см. [здесь](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Предоставьте группе AAD необходимые разрешения** точно так же, как вы предоставляете разрешения пользователям SQL. Например, выполните эту команду:

    ```sql
    EXEC sp_addrolemember [role name], [your AAD group name];
    ```

5. Настройте в ADF связанную службу базы данных SQL Azure.

**Пример использования проверки подлинности на основе MSI в связанной службе:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. Этот раздел содержит список свойств, поддерживаемых набором данных базы данных SQL Azure.

Чтобы скопировать данные в базу данных SQL Azure или из нее, задайте для свойства type набора данных значение **AzureSqlTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение: **AzureSqlTable**. | Yes |
| tableName |Имя таблицы или представления в экземпляре базы данных SQL Azure, на которое ссылается связанная служба. | Yes |

**Пример.**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). Этот раздел содержит список свойств, поддерживаемых источником и приемником базы данных SQL Azure.

### <a name="azure-sql-database-as-source"></a>База данных SQL Azure в качестве источника

Чтобы скопировать данные из базы данных SQL Azure, задайте тип источника **SqlSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **SqlSource**. | Yes |
| SqlReaderQuery |Используйте пользовательский SQL-запрос для чтения данных. Пример: `select * from MyTable`. |Нет  |
| sqlReaderStoredProcedureName |Имя хранимой процедуры, которая считывает данные из исходной таблицы. Последней инструкцией SQL должна быть инструкция SELECT в хранимой процедуре. |Нет  |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет  |

**Примечания:**

- Если для SqlSource указано **sqlReaderQuery** , то действие копирования выполняет этот запрос для источника базы данных SQL Azure для получения данных. Кроме того, можно создать хранимую процедуру, указав **sqlReaderStoredProcedureName** и **storedProcedureParameters** (если хранимая процедура принимает параметры).
- Если не указать sqlReaderQuery или sqlReaderStoredProcedureName, то для создания запроса (`select column1, column2 from mytable`) к базе данных SQL Azure будут использованы столбцы, определенные в разделе структуры набора данных JSON. Если в определении набора данных нет раздела structure, выбираются все столбцы из таблицы.
- При использовании **sqlReaderStoredProcedureName** по-прежнему необходимо указать фиктивное свойство **tableName** в наборе данных JSON.

**Пример. Использование SQL-запроса**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "SqlSource",
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

### <a name="azure-sql-database-as-sink"></a>База данных SQL Azure в качестве приемника

Чтобы скопировать данные в базу данных SQL Azure, задайте тип приемника **SqlSink** в действии копирования. В разделе **sink** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type приемника действия копирования должно иметь значение **SqlSink**. | Yes |
| writeBatchSize |Вставляет данные в таблицу SQL, когда размер буфера достигает значения writeBatchSize.<br/>Допустимые значения: целое число (количество строк). |Нет (значение по умолчанию — 10 000) |
| writeBatchTimeout |Время ожидания до выполнения операции пакетной вставки, пока не завершится срок ее действия.<br/>Допустимые значения: промежуток времени. Пример: 00:30:00 (30 минут). |Нет  |
| preCopyScript |Укажите SQL-запрос для действия копирования, выполняемый перед записью данных в базе данных SQL Azure. Он будет однократно вызываться при каждом запуске копирования. Это свойство можно использовать для очистки предварительно загруженных данных. |Нет  |
| sqlWriterStoredProcedureName |Имя хранимой процедуры, в которой определяется, как применить исходные данные в целевой таблице. Например, можно определить выполнение операций upsert или преобразований с помощью вашей собственной бизнес-логики. <br/><br/>Обратите внимание, что эта хранимая процедура будет **вызываться для каждого пакета**. Чтобы однократно выполнить операцию, в которой не используются исходные данные, например удаление или усечение, примените свойство `preCopyScript`. |Нет  |
| storedProcedureParameters |Параметры для хранимой процедуры.<br/>Допустимые значения: пары "имя — значение". Имена и регистр параметров должны совпадать с именами и регистром параметров хранимой процедуры. |Нет  |
| sqlWriterTableType |Укажите имя типа таблицы для использования в хранимой процедуре. Действие копирования делает перемещаемые данные доступными во временной таблице этого типа. Код хранимой процедуры затем можно использовать для объединения копируемых и существующих данных. |Нет  |

> [!TIP]
> Когда вы копируете данные в базу данных SQL, действие копирования по умолчанию добавляет данные в таблицу приемника. Для выполнения команды UPSERT или дополнительной бизнес-логики используйте хранимую процедуру в SqlSink. Дополнительные сведения см. в разделе [Вызов хранимой процедуры для приемника SQL](#invoking-stored-procedure-for-sql-sink).

**Пример 1. Добавление данных**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Пример 2. Вызов хранимой процедуры во время копирования для выполнения операции Upsert**

Дополнительные сведения см. в разделе [Вызов хранимой процедуры для приемника SQL](#invoking-stored-procedure-for-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>Столбцы идентификаторов в целевой базе данных

В этом разделе приведен пример копирования данных из исходной таблицы без столбца идентификаторов в целевую таблицу со столбцом идентификаторов.

**Исходная таблица:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Целевая таблица:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Обратите внимание, что в целевой таблице есть столбец идентификаторов.

**Определение JSON исходного набора данных**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Определение JSON целевого набора данных**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Обратите внимание, что у исходной и целевой таблиц разные схемы (в целевой есть дополнительный столбец с идентификаторами). В этом случае необходимо указать свойство **structure** в определении целевого набора данных, которое не включает в себя столбец идентификаторов.

## <a name="invoking-stored-procedure-for-sql-sink"></a> Вызов хранимой процедуры из приемника SQL

Для копирования данных в базу данных SQL Azure можно настроить и вызвать указанную пользователем хранимую процедуру с дополнительными параметрами.

Хранимую процедуру можно использовать, когда встроенные механизмы копирования не подходят. Обычно хранимая процедура используется, когда операцию Upsert (вставка и обновление) или дополнительную обработку (объединение столбцов, поиск дополнительных значений, вставка данных в несколько таблиц и т. д.) необходимо выполнить до окончательной вставки данных источника в целевую таблицу.

В следующем примере показано, как использовать хранимую процедуру для выполнения операции Upsert в таблице базы данных SQL Azure. Предположим, что и входные данные, и таблица Marketing приемника состоят из трех столбцов: ProfileID, State и Category. Выполните операцию Upsert на основе данных столбца ProfileID только для определенной категории.

**Выходной набор данных**

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Определите раздел SqlSink в действии копирования следующим образом.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

В своей базе данных определите хранимую процедуру с тем же именем, что и SqlWriterStoredProcedureName. Она обрабатывает входные данные из указанного источника и выполняет их слияние в выходную таблицу. Обратите внимание, что имя параметра хранимой процедуры должно совпадать с именем tableName, заданным в наборе данных.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category)
END
```

В своей базе данных определите тип таблицы с тем же именем, что и SqlWriterTableType. Обратите внимание, что схема типа таблицы должны быть той же, что и схема, возвращаемая входными данными.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

Функциональность хранимой процедуры использует преимущества [параметров с табличным значением](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Сопоставление типов данных для базы данных SQL Azure

При копировании данных в базу данных SQL Azure или из нее используются следующие сопоставления между типами данных базы данных SQL Azure и временными типами данных фабрики данных Azure. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных базы данных SQL Azure | Тип промежуточных данных фабрики данных |
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