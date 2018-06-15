---
title: Создание заданий обработки эластичных баз данных и управление ими с помощью PowerShell | Документация Майкрософт
description: Управление пулами базы данных SQL Azure с помощью PowerShell
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: d1869dd689ef090978c3835e0a16fec82dfb5c05
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648692"
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Создание заданий обработки эластичных баз данных базы данных SQL и управление ими с помощью PowerShell (предварительная версия)

С помощью API-интерфейсов PowerShell для **заданий обработки эластичных баз данных** (в предварительной версии) вы можете определить группу баз данных, для которых будут выполняться скрипты. В этой статье рассказывается, как создавать **задания обработки эластичных баз данных** и управлять ими с помощью командлетов PowerShell. См. статью [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>предварительным требованиям
* Подписка Azure. Зарегистрироваться в пробной версии, которая доступна бесплатно в течение одного месяца, можно [здесь](https://azure.microsoft.com/pricing/free-trial/).
* Набор баз данных, созданных с помощью инструментов эластичных баз данных. См. статью [Приступая к работе с инструментами эластичных баз данных](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **Задания обработки эластичных баз данных.** Пакет PowerShell. См. статью [Обзор установки заданий обработки эластичных баз данных](sql-database-elastic-jobs-service-installation.md).

### <a name="select-your-azure-subscription"></a>Выбор подписки Azure
Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Если у вас несколько подписок, то можно выполнить командлет **Get-AzureRmSubscription** и скопировать необходимые сведения о подписке из результирующего набора. Получив сведения о подписке, выполните следующий командлет, чтобы сделать ее подпиской по умолчанию, то есть целью для создания заданий и управления ими.

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

Для написания и выполнения сценариев PowerShell в рамках заданий обработки эластичных баз данных рекомендуется использовать [интегрированную среду сценариев PowerShell](https://technet.microsoft.com/library/dd315244.aspx) .

## <a name="elastic-database-jobs-objects"></a>Объекты заданий обработки эластичных баз данных
В следующей таблице перечислены все типы объектов **заданий обработки эластичных баз данных** , а также приведены их описания и связанные API PowerShell.

<table style="width:100%">
  <tr>
    <th>Тип объекта</th>
    <th>ОПИСАНИЕ</th>
    <th>Связанные API-интерфейсы PowerShell</th>
  </tr>
  <tr>
    <td>Учетные данные</td>
    <td>Имя пользователя и пароль, используемые при подключении к базам данных для выполнения сценариев или применением файлов DACPAC. <p>Пароль зашифровывается перед отправкой в базу данных заданий обработки эластичных баз данных и хранения в ней.  Пароль шифруется службой заданий обработки эластичных баз данных с помощью учетных данных, созданных и отправленных из сценария установки.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Скрипт</td>
    <td>Сценарий Transact-SQL для выполнения между базами данных.  Сценарий должен быть создан идемпотентным, так как служба будет повторять попытку выполнения сценария в случае сбоя.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td>Пакет <a href="https://msdn.microsoft.com/library/ee210546.aspx">приложения уровня данных</a>, применяемый между базами данных.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>New-AzureSqlJobContent</p>
    <p>Set-AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Целевая база данных</td>
    <td>База данных и имя сервера, указывающие на Базу данных SQL Azure.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Целевая карта сегментов</td>
    <td>Сочетание целевой базы данных и учетных данных, используемое для определения сведений, хранящихся в карте сегментов эластичных баз данных.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    <p>Set-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Целевая пользовательская коллекция</td>
    <td>Группа баз данных, заданная для коллективного выполнения.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>New-AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Целевая дочерняя пользовательская коллекция</td>
    <td>Целевая база данных, на которую ссылается пользовательская коллекция.</td>
    <td>
    <p>Add-AzureSqlJobChildTarget</p>
    <p>Remove-AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Задание</td>
    <td>
    <p>Определение параметров для задания, которое можно использовать для выполнения или исполнения расписания.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>New-AzureSqlJob</p>
    <p>Set-AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Выполнение заданий</td>
    <td>
    <p>Контейнер заданий, которые необходимо выполнить с помощью сценария или применения файла DACPAC к цели с помощью учетных данных для подключений к базам данных с обработкой сбоев в соответствии с политикой выполнения.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Выполнение задач</td>
    <td>
    <p>Одна единица работы для выполнения задания.</p>
    <p>Если задаче не удается успешно выполниться, соответствующее сообщение об исключении будет записано в журнал и будет создана новая идентичная задача, которая выполняется в соответствии с указанной политикой выполнения.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Wait-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Политика выполнения заданий</td>
    <td>
    <p>Управляет временем ожидания выполнения заданий, максимальными количествами повторных попыток и интервалами между ними.</p>
    <p>Задания обработки эластичных баз данных включают стандартную политику выполнения, которая вызывает практически неограниченное число повторных попыток в случае сбоя задач с экспоненциально растущим интервалом между ними.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>New-AzureSqlJobExecutionPolicy</p>
    <p>Set-AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Расписание</td>
    <td>
    <p>Выбор выполнения с определенным интервалом или один раз.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>New-AzureSqlJobSchedule</p>
    <p>Set-AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Триггеры заданий</td>
    <td>
    <p>Сопоставление для выполнения задания по расписанию.</p>
    </td>
    <td>
    <p>New-AzureSqlJobTrigger</p>
    <p>Remove-AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Поддерживаемые типы групп заданий обработки эластичных баз данных
Задания позволяют выполнять сценарии Transact-SQL (T-SQL) или применять файлы DACPAC в группе баз данных. Когда задание отправляется на выполнение в группе баз данных, оно «разбивается» на дочерние задания, каждое из которых выполняет нужную задачу в отдельной базе данных из группы. 

Вы можете создать группу одного из двух типов. 

* Группа [Карта сегментов](sql-database-elastic-scale-shard-map-management.md). Когда задание отправляется для обработки карты сегментов, оно сначала отправляет карте сегментов запрос, чтобы определить текущий набор сегментов, а затем создает дочерние задания для каждого сегмента в карте.
* Группа «Пользовательская коллекция»: определяемый пользователем набор баз данных. Задание, целью которого является пользовательская коллекция, создает дочерние задания для каждой базы данных в пользовательской коллекции.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Настройка подключения к заданиям обработки эластичных баз данных
Чтобы использовать API-интерфейсы заданий, сначала нужно настроить подключение к *управляющей базе данных* заданий. При вызове приведенного ниже командлета открывается окно ввода учетных данных, где пользователь должен указать имя и пароль, предоставленные во время установки заданий обработки эластичных баз данных. Во всех примерах из этого раздела предполагается, что первый этап уже выполнен.

Откройте подключение к заданиям обработки эластичных баз данных:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Зашифрованные учетные данные в заданиях обработки эластичных баз данных
Учетные данные баз данных можно вставлять в *управляющую базу данных* заданий с зашифрованным паролем. Такая возможность полезна для хранения учетных данных, что позволяет выполнять задания позже (с использованием расписаний заданий).

Шифрование работает с помощью сертификата, созданного сценарием установки. Сценарий установки создает и отправляет сертификат в облачную службу Azure для шифрования сохраненных паролей шифрования. Затем облачная служба Azure сохраняет открытый ключ в *управляющей базе данных* заданий. Э позволяет API-интерфейсу PowerShell или интерфейсу портала Azure шифровать предоставленный пароль без локально установленного сертификата.

Пароли учетных данных шифруются и защищаются от пользователей, которые могут только читать объекты заданий обработки эластичных баз данных. Однако злоумышленник, имеющий права читать и записывать объекты заданий обработки эластичных баз данных, вероятно, сможет извлечь пароль. Учетные данные рассчитаны на повторное использования при разных запусках задания. Учетные данные передаются целевым базам данных при установке соединений. В настоящее время не существует ограничений для целевых баз данных, используемых для каждого набора учетных данных, и злоумышленник может добавлять целевые объекты для базы данных, которой он управляет. Пользователь может впоследствии запустить задание для этой базы данных, чтобы получить пароль для входа в учетную запись.

При работе с заданиями обработки эластичных баз данных мы рекомендуем использовать такие меры безопасности.

* Предоставляйте доступ к API-интерфейсам только доверенным лицам.
* Учетные данные должны иметь только те права, которые необходимы для выполнения задания.  Дополнительные сведения см. на сайте MSDN в статье [Проверка прав доступа и разрешений в SQL Server](https://msdn.microsoft.com/library/bb669084.aspx).

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Создание зашифрованных учетных данных для выполнения заданий в нескольких базах данных
Чтобы создать новые зашифрованные учетные данные, командлет [**Get-Credential**](https://technet.microsoft.com/library/hh849815.aspx) запрашивает имя пользователя и пароль, которые можно передать в командлет [**New-AzureSqlJobCredential**](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Изменение учетных данных
При изменении пароля используйте командлет [**Set-AzureSqlJobCredential**](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) с параметром **CredentialName**.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Определение целевого объекта карты сегментов эластичных баз данных
Чтобы выполнить задание во всех базах данных в карте сегментов (созданной с помощью [клиентской библиотеки эластичных баз данных](sql-database-elastic-database-client-library.md)), используйте в качестве целевого объекта базы данных карту сегментов. Для этого примера требуется сегментированное приложение, созданное с помощью клиентской библиотеки эластичных баз данных. См. статью [Приступая к работе с инструментами эластичных баз данных](sql-database-elastic-scale-get-started.md).

Для этого базу данных диспетчера карт сегментов необходимо указать как целевой объект базы данных, а затем в качестве целевого объекта указать конкретную карту сегментов.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Создание сценария T-SQL для выполнения в нескольких базах данных
При создании сценариев T-SQL мы настоятельно рекомендуется делать их [идемпотентными](https://en.wikipedia.org/wiki/Idempotence) и защищенными от сбоев. Задания обработки эластичных баз данных повторяют попытку выполнения сценария в случае сбоя, независимо от типа ошибки.

С помощью командлета [**New-AzureSqlJobContent**](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent), указав параметры **-ContentName** и **-CommandText**, создайте и сохраните нужный скрипт.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Создание нового сценария из файла
Если сценарий T-SQL определен в файле, импортируйте сценарий с помощью следующего кода:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Изменение сценария T-SQL, который должен выполняться в нескольких базах данных
Приведенный ниже сценарий PowerShell изменяет текст команды T-SQL для существующего сценария.

Задайте следующие переменные в соответствии с нужным определением сценария:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Изменение определения в соответствии с существующим сценарием
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Создание задания, которое будет выполнять сценарий в карте сегментов
Приведенный ниже сценарий PowerShell запускает задание, которое выполнит сценарий в каждом сегменте карты с динамическим масштабированием.

Задайте следующие переменные в соответствии с нужными сценарием и целью:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Выполнение задания
Приведенный ниже сценарий PowerShell выполняет существующее задание.

Измените следующую переменную в соответствии с именем выполняемого задания:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Получение сведений о состоянии выполнения одного задания
Чтобы просмотреть сведения о состоянии выполнения задания, выполните командлет [**Get-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) с параметром **JobExecutionId**.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Чтобы узнать состояние выполнения дочерних заданий, в частности состояние каждого задания в каждой целевой базе данных, используйте тот же командлет **Get-AzureSqlJobExecution** с параметром **IncludeChildren**.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Просмотр сведений о состоянии выполнения нескольких заданий
У командлета [**Get-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/new-azuresqljob) есть несколько необязательных параметров, позволяющих увидеть ход выполнения нескольких заданий, отфильтрованных по указанным параметрам. Ниже показаны некоторые из возможных способов использования командлета Get-AzureSqlJobExecution:

Получение сведений о выполнении всех активных заданий верхнего уровня:

    Get-AzureSqlJobExecution

Получение сведений о выполнении всех заданий верхнего уровня, включая неактивные задания:

    Get-AzureSqlJobExecution -IncludeInactive

Получение состояния выполнения всех дочерних заданий, включая неактивные, по указанному идентификатору выполнения задания:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Получение состояния выполнения всех заданий, созданных с помощью сочетания расписания и задания, включая неактивные:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Получение сведения обо всех заданиях в указанной карте сегментов, включая неактивные:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Получение сведения обо всех заданиях в указанном пользовательском семействе, включая неактивные:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Получение списка выполнения задач в определенном задании:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Получение сведений о выполнении задачи:

Следующий сценарий PowerShell позволяет просмотреть сведения о выполнении задачи, что особенно полезно при отладке сбоев выполнения.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Получение ошибок при выполнении задач
Объект **JobTaskExecution** содержит свойство жизненного цикла задачи (Lifecycle) и свойство сообщения (Message). Если выполнение какой-либо задачи задания завершилось сбоем, свойство Lifecycle принимает значение *Failed* , а свойство Message — сообщение об исключении и его стек. Если задание завершилось неудачно, важно просмотреть сведения задачах, которые не были выполнены в определенном задании.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Ожидание завершения выполнения задания
Следующий сценарий PowerShell позволяет дождаться завершения задачи:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Создание пользовательской политики выполнения
Задания обработки эластичных баз данных поддерживают создание пользовательских политик выполнения, которые можно применять при запуске заданий.

В настоящий момент политики выполнения позволяют задать следующее:

* Имя: идентификатор политики выполнения.
* Время ожидания задания: общее время до отмены задания службой заданий обработки эластичных баз данных.
* Исходный интервал повторных попыток: интервал ожидания до первой повторной попытки.
* Максимальный интервал повторных попыток: предел используемых интервалов повторных попыток.
* Коэффициент роста интервала повторных попыток: коэффициент, используемый для вычисления следующего интервала между повторными попытками.  Используется следующая формула: (Исходный интервал повторных попыток) * Math.pow((Коэффициент роста интервала), (Число повторных попыток) - 2). 
* Максимальное число попыток: максимальное число повторных попыток, выполняемых в задании.

В политике выполнения по умолчанию используются следующие значения:

* Имя: политика выполнения по умолчанию
* Время ожидания задания: 1 неделя
* Исходный интервал повторных попыток: 100 миллисекунд.
* Максимальный интервал повторных попыток: 30 минут
* Коэффициент интервала повторных попыток: 2
* Максимальное число попыток: 2 147 483 647

Создайте нужную политику выполнения:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Изменение пользовательской политики выполнения
Измените нужную политику выполнения:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Отмена задания
Задания обработки эластичных баз данных поддерживают запросы на отмену заданий.  Если служба заданий обработки эластичных баз данных обнаружат запрос на отмену выполняемого задания, она попытается остановить задачу.

Служба заданий обработки эластичных баз данных может выполнить отмену двумя разными способами:

1. Отмена выполняющихся задач. Если отмена обнаружена во время выполнения задачи, система пытается отменить выполняющийся аспект задачи.  Например: если при попытке отмены выполняется длинный запрос, произойдет попытка его отмены.
2. Отмена повторных попыток выполнения задачи. Если управляющий поток обнаружит отмену до начала выполнения задачи, он не будет запускать задачу и объявит запрос отмененным.

В случае запроса отмены родительского задания этот запрос будет выполнен для родительского задания и всех его дочерних заданий.

Чтобы отправить запрос на отмену, используйте командлет [**Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) с параметром **JobExecutionId**.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Асинхронное удаление задания и журнала заданий
Задания обработки эластичных баз данных поддерживают асинхронное удаление заданий. Задания могут быть отмечены для удаления, а система удалит задание и весь его журнал после завершения всех выполнений задания. Система не будет автоматически отменять активные выполнения заданий.  

Чтобы отменить уже выполняющиеся задания, выполните командлет [**Stop-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution).

Чтобы запустить удаление задания, выполните командлет [**Remove-AzureSqlJob**](/powershell/module/elasticdatabasejobs/remove-azuresqljob) с параметром **JobName**.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Создание целевого объекта пользовательской базы данных
Вы можете определять целевые объекты пользовательской базы данных для выполнения напрямую или для включения в группу пользовательских баз данных. Например, так как **пулы эластичных баз данных** пока нельзя использовать напрямую через API-интерфейсы PowerShell, вы можете создать пользовательское целевое семейство баз данных, охватив, таким образом, все базы данных в пуле.

Задайте следующие переменные в соответствии с нужными сведениями о базе данных:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Создание целевого объекта пользовательской коллекции баз данных
Чтобы определить целевой объект пользовательской коллекции баз данных и разрешить выполнение задания в нескольких определенных целевых объектах баз данных, используйте командлет [**New-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget). После создания группы баз данных их можно связать с целевым объектом пользовательской коллекции.

Задайте следующие переменные в соответствии с нужной конфигурацией пользовательского семейства:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Добавление баз данных в целевой объект пользовательской коллекции баз данных
Чтобы добавить базу данных в определенную пользовательскую коллекцию, используйте командлет [**Add-AzureSqlJobChildTarget**](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget).

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Просмотр баз данных в целевом объекте пользовательской коллекции баз данных
Используйте командлет [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget), чтобы получить дочерние базы данных в пользовательском семействе. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Создание задания для выполнения сценария в пользовательском семействе баз данных
Используйте командлет [**New-AzureSqlJob**](/powershell/module/elasticdatabasejobs/new-azuresqljob), чтобы создать задание в группе баз данных, определенной пользовательским семейством баз данных. Задания обработки эластичных баз данных расширят задание до нескольких дочерних заданий, каждое из которых соответствует базе данных, связанной с целевым пользовательским семейством, и обеспечивают выполнение сценария для каждой базы данных. Как и ранее, важно, чтобы сценарии были идемпотентными и защищенными от повторных попыток.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Сбор данных из нескольких баз данных
С помощью задания вы можете выполнить запрос в группе баз данных и отправить результаты в специальную таблицу. Позже можно отправить таблице запрос, чтобы увидеть результаты запроса из каждой базы данных. Это обеспечивает асинхронный механизм выполнения запроса для множества баз данных. После неудачной попытки автоматически выполняется повторная попытка.

Указанная целевая таблица создается автоматически (если она еще не создана). Новая таблица соответствует схеме возвращенного результирующего набора. Если сценарий возвращает несколько наборов результатов, задания обработки эластичных баз данных отправляют в указанную целевую таблицу только первый набор.

Приведенный ниже сценарий PowerShell выполняет сценарий и собирает результаты в указанную таблицу. В этом сценарии предполагается, что у вас уже есть сценарий T-SQL, который возвращает один набор результатов, и целевой объект пользовательской коллекции баз данных.

В этом скрипте используется командлет [**Get-AzureSqlJobTarget**](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget). Укажите параметры для сценария, учетные данные и целевой объект задания.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Создание и запуск задания для сбора данных
В этом скрипте используется командлет [**Start-AzureSqlJobExecution**](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution).

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Создание расписания для задания
Приведенный ниже сценарий PowerShell можно использовать для создания повторяющегося расписания. В этом скрипте используется интервал в минутах, но командлет [**New-AzureSqlJobSchedule**](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) также поддерживает параметры -DayInterval, -HourInterval, -MonthInterval и -WeekInterval. Расписания, которые выполняются только один раз, можно создавать с помощью параметра -OneTime.

Создание нового расписания

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Активация задания по расписанию
Вы можете определить триггер задания, который будет выполнять задание согласно расписанию. Следующий сценарий PowerShell позволяет создать триггер задания.

Используйте командлет [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) и задайте переменные в соответствии с нужными заданием и расписанием.

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Удаление расписания задания
Чтобы остановить регулярное выполнение задания с помощью триггера, можно удалить триггер. Чтобы удалить триггер и остановить выполнение задания по расписанию, используйте командлет [**Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Получение триггеров заданий, связанных с расписанием
Следующий сценарий PowerShell позволяет получить и вывести на экран триггеры заданий, зарегистрированных с определенным расписанием.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Получение триггеров, связанных с заданием
Чтобы получить и отобразить расписания, содержащие зарегистрированное задание, используйте командлет [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) .

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Создание приложения уровня данных (DACPAC) для выполнения в нескольких базах данных
Сведения о создании пакета DACPAC см. в статье [Приложения уровня данных](https://msdn.microsoft.com/library/ee210546.aspx). Чтобы развернуть пакет DACPAC, используйте командлет [New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). Пакет DACPAC должен быть доступен для службы. Созданный пакет DACPAC рекомендуется отправить в хранилище Azure и создать для него [подписанный URL-адрес](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Изменение приложения уровня данных (DACPAC) для его выполнения в нескольких базах данных
Имеющиеся файлы DACPAC, зарегистрированные в заданиях обработки эластичных баз данных, можно изменить, чтобы они указывали на новые URI. Чтобы изменить URI имеющегося зарегистрированного пакета DACPAC, используйте командлет [**Set-AzureSqlJobContentDefinition**](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition).

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Создание задания для применения пакета DACPAC в нескольких базах данных
После создания DACPAC в службе заданий обработки эластичных баз данных можно создать задание для применения DACPAC к группе баз данных. Следующий сценарий PowerShell позволяет создать задание DACPAC в пользовательском семействе баз данных:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
