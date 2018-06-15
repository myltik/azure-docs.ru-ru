---
title: Код файла событий XEvent для базы данных SQL | Документация Майкрософт
description: Содержит сценарии PowerShell и Transact-SQL для примера двухэтапного кода, демонстрирующего целевой файл событий в расширенном событии в Базе данных SQL Azure. Обязательной частью данного сценария является хранилище Azure.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: b905c921ae967d7f755f084bd6b9b30de34f76b4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649641"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Код целевого файла событий для расширенных событий в Базе данных SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Вам нужен полный образец надежного кода для регистрации и сообщения сведений о расширенных событиях.

В Microsoft SQL Server [целевой файл событий](http://msdn.microsoft.com/library/ff878115.aspx) используется для сохранения выходных данных событий в файл на локальном жестком диске. Однако такие файлы недоступны для Базы данных SQL Azure. Вместо нее для поддержки целевого файла событий мы используем хранилище Azure.

В этом разделе представлен пример двухэтапного кода.

* PowerShell: создание контейнера хранилища Azure в облаке.
* Transact-SQL:
  
  * назначение контейнера хранилища Azure для целевого файла событий;
  * создание и запуск сеанса событий и т. д.

## <a name="prerequisites"></a>предварительным требованиям

* Учетная запись и подписка Azure. Вы можете зарегистрироваться, чтобы получить [бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/).
* Любая база данных, позволяющая создать таблицу.
  
  * При необходимости вы можете быстро [создать демонстрационную базу данных **AdventureWorksLT**](sql-database-get-started.md).
* SQL Server Management Studio (ssms.exe), в идеале — последняя ежемесячная версия обновления. 
  Ресурсы для загрузки последней версии файла ssms.exe:
  
  * Статья [Скачивание SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Прямая ссылка на загрузку.](http://go.microsoft.com/fwlink/?linkid=616025)
* Установленные [модули Azure PowerShell](http://go.microsoft.com/?linkid=9811175) .
  
  * Модули предоставляют такие команды, как **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Этап 1. Код PowerShell для контейнера хранилища Azure

На первом этапе примера двухэтапного кода используется PowerShell.

Сценарий начинается с команд, предназначенных для очистки данных после его предыдущего выполнения, и является многоразовым.

1. Вставьте сценарий PowerShell в простой текстовый редактор, например Notepad.exe, и сохраните его в виде файла с расширением **PS1**.
2. Запустите PowerShell ISE от имени администратора.
3. В командной строке введите<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>и нажмите клавишу ВВОД.
4. В PowerShell ISE откройте свой файл **PS1** . Выполните скрипт.
5. Сначала сценарий запускает новое окно, в котором необходимо войти в Azure.
   
   * При повторном выполнении сценария без прерывания работы сеанса появляется удобная функция комментирования команды **Add-AzureAccount** .

![PowerShell ISE с установленным модулем Azure, готовый к выполнению сценария.][30_powershell_ise]

### <a name="powershell-code"></a>Код PowerShell

В этом сценарии PowerShell предполагается, что командлет Import-Module для модуля AzureRm уже запущен. Справочную документацию см. на странице [браузера модуля PowerShell](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzureRmAccount (or  Connect-AzureRmAccount), just one time.';
#Connect-AzureRmAccount;   # Same as  Connect-AzureRmAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'West US';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzureRmStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Обратите внимание на ряд именованных значений, которые выводятся на экран по завершении сценария PowerShell. Эти значения нужно будет внести в сценарий Transact-SQL, работа с которым составляет второй этап.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Этап 2. Код Transact-SQL, использующий контейнер хранилища Azure

* На первом этапе работы с примером кода вы выполнили сценарий PowerShell, создающий контейнер службы хранилища Azure.
* На втором этапе этот контейнер используется в сценарии Transact-SQL.

Сценарий начинается с команд, предназначенных для очистки данных после его предыдущего выполнения, и является многоразовым.

После выполнения сценария PowerShell на экран выводится ряд именованных значений. Эти значения необходимо включить в сценарий Transact-SQL. Чтобы определить места, в которые нужно внести изменения, найдите в сценарии Transact-SQL параметр **TODO** .

1. Откройте SQL Server Management Studio (ssms.exe).
2. Подключитесь к Базе данных SQL Azure.
3. Щелкните, чтобы открыть новую область запроса.
4. В область запроса вставьте указанный ниже сценарий Transact-SQL.
5. Найти в сценарии все случаи использования параметра **TODO** и внесите соответствующие изменения.
6. Сохраните и выполните сценарий.


> [!WARNING]
> Значение ключа SAS, созданное предыдущим сценарием PowerShell, может начинаться с вопросительного знака ("?"). При использовании ключа SAS в приведенном ниже сценарии T-SQL *этот вопросительный знак нужно удалить*. В противном случае система безопасности может заблокировать ваши действия.


### <a name="transact-sql-code"></a>Код Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Если при выполнении сценария целевой объект не подхватывается, остановите и перезапустите сеанс события:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Выходные данные

После того как сценарий Transact-SQL будет выполнен, щелкните ячейку под заголовком столбца **event_data_XML**. Один из отображенных элементов **<event>** содержит инструкцию UPDATE.

Ниже приведен один из элементов **<event>** , сформированных в процессе тестирования.


```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```


В приведенном выше сценарии Transact-SQL для чтения event_file была использована следующая системная функция.

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Описание дополнительных параметров для просмотра данных из расширенных событий доступно в следующей статье.

* [Advanced Viewing of Target Data from Extended Events in SQL Server (Дополнительные параметры просмотра целевых данных из расширенных событий в SQL Server)](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Преобразование примера кода для выполнения на SQL Server

Предположим, что приведенный выше пример сценария Transact-SQL нужно выполнить на Microsoft SQL Server.

* Проще всего будет полностью заменить контейнер службы хранилища Azure простым файлом, например **C:\myeventdata.xel**. Этот файл будет записан на локальный жесткий диск компьютера, где находится SQL Server.
* Инструкции Transact-SQL **CREATE MASTER KEY** и **CREATE CREDENTIAL** в этом случае не понадобятся.
* В предложении **ADD TARGET** оператора **CREATE EVENT SESSION** измените значение Http, заменив **filename=** на полную строку пути, например **C:\myfile.xel**.
  
  * Учетная запись хранения Azure при этом не нужна.

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения об учетных записях и контейнерах в службе хранилища Azure см. в следующих статьях:

* [Использование хранилища BLOB-объектов из .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Работа с корневым контейнером](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Урок 1. Создание хранимой политики доступа и подписанного URL-адреса для контейнера Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Урок 2. Создание учетных данных SQL Server с использованием подписанного URL-адреса](http://msdn.microsoft.com/library/dn466435.aspx)
* [Расширенные события](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

