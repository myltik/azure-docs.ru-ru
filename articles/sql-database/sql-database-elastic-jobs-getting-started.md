---
title: Начало работы с заданиями обработки эластичных баз данных | Документация Майкрософт
description: Использование заданий эластичной базы данных для выполнения скриптов T-SQL в нескольких базах данных.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4f12c3353ca4949b3c1c031420ec5a0b8fdb2dbf
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649158"
---
# <a name="getting-started-with-elastic-database-jobs"></a>Начало работы с заданиями обработки эластичных баз данных
Задания обработки эластичных баз данных (предварительная версия) для службы "База данных SQL Azure" позволяют надежно выполнять сценарии T-SQL, относящиеся к нескольким базам данных, автоматически повторяя попытки и обеспечивая гарантии успешного завершения. Дополнительные сведения о заданиях обработки эластичных баз данных см. в статье [Управление масштабируемыми облачными базами данных](sql-database-elastic-jobs-overview.md).

В этой статье мы продолжим работу с примером из статьи [Приступая к работе с инструментами эластичных баз данных](sql-database-elastic-scale-get-started.md). Выполнив наши инструкции, вы научитесь создавать и администрировать задания, которые управляют группой связанных баз данных. Чтобы воспользоваться преимуществами заданий обработки эластичных БД, не обязательно использовать инструменты эластичного масштабирования.

## <a name="prerequisites"></a>предварительным требованиям
Загрузите и запустите пример [Приступая к работе с инструментами эластичной базы данных](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Создание диспетчера сопоставления сегментов с помощью примера приложения 
Вы создадите диспетчер сопоставления сегментов и несколько сегментов, а затем вставите данные в эти сегменты. Если вы уже настроили сегменты с помощью сегментированных данных, описанные ниже действия можно пропустить и перейти к следующему разделу.

1. Постройте и запустите пример приложения **Приступая к работе с инструментами эластичной базы данных** . Следуйте инструкциям до шага 7 в разделе [Загрузка и запуск примера приложения](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). В конце шага 7 появится следующее окно командной строки:

   ![окно командной строки.](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. В окне команд введите "1" и нажмите клавишу **ВВОД**. Это позволит создать диспетчер сопоставления сегментов и добавить два сегмента на сервер. Затем введите 3 и нажмите клавишу **ВВОД**. Повторите это действие четыре раза. Это позволит вставить строки демонстрационных данных в свои сегменты.
3. На [портале Azure](https://portal.azure.com) должны появиться три новые базы данных.

   ![Подтверждение Visual Studio](./media/sql-database-elastic-query-getting-started/portal.png)

   На этом этапе мы создадим семейство баз данных, которое включает все базы данных в карте сегментов. Это позволит нам создать и выполнить задание, которое добавит новую таблицу во все сегменты.

Здесь обычно создается целевая карта сегментов с помощью командлета **New-AzureSqlJobTarget** . Для этого необходимо указать управляющую базу данных карт сегментов как целевую базу данных, а затем указать в качестве цели конкретную карту сегментов. Вместо этого мы перечислим все базы данных на сервере и добавим базы данных в новое семейство, за исключением базы данных master.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Создание пользовательской коллекции и добавление всех баз данных на сервере в пользовательскую целевую коллекцию за исключением базы данных master
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Создание сценария T-SQL для выполнения в нескольких базах данных
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Создание задания для выполнения сценария в пользовательской группе баз данных

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Выполнение задания
Следующий сценарий PowerShell позволяет выполнить существующее задание:

Измените следующую переменную в соответствии с именем выполняемого задания:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Получение состояния выполнения одного задания
Чтобы узнать состояние выполнения дочерних заданий, в частности состояние каждого задания в каждой целевой базе данных, используйте тот же командлет **Get-AzureSqlJobExecution** с параметром **IncludeChildren**.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Просмотр состояния выполнения нескольких заданий
У командлета **Get-AzureSqlJobExecution** есть несколько необязательных параметров, позволяющих увидеть ход выполнения нескольких заданий, отфильтрованных по указанным параметрам. Ниже показаны некоторые из возможных способов использования командлета Get-AzureSqlJobExecution:

Получение сведений о выполнении всех активных заданий верхнего уровня:

   ```
    Get-AzureSqlJobExecution
   ```

Получение сведений о выполнении всех заданий верхнего уровня, включая неактивные задания:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Получение состояния выполнения всех дочерних заданий, включая неактивные, по указанному идентификатору выполнения задания:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Получение состояния выполнения всех заданий, созданных с помощью сочетания расписания и задания, включая неактивные:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Получение сведения обо всех заданиях в указанной карте сегментов, включая неактивные:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Получение сведения обо всех заданиях в указанном пользовательском семействе, включая неактивные:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Получение списка выполнения задач в определенном задании:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Получение сведений о выполнении задачи:

Следующий сценарий PowerShell позволяет просмотреть сведения о выполнении задачи, что особенно полезно при отладке сбоев выполнения.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Получение ошибок при выполнении задач
Объект JobTaskExecution включает свойство Lifecycle для жизненного цикла задачи и свойство Message. Если выполнение какой-либо задачи задания завершилось сбоем, свойство Lifecycle принимает значение *Failed*, а в свойстве Message сохраняется сообщение об исключении и стек исключения. Если задание завершилось неудачно, важно просмотреть сведения задачах, которые не были выполнены в определенном задании.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>Ожидание завершения выполнения задания
Следующий сценарий PowerShell позволяет дождаться завершения задачи:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Создание пользовательской политики выполнения
Задания обработки эластичных баз данных поддерживают создание пользовательских политик выполнения, которые можно применять при запуске заданий.

В настоящий момент политики выполнения позволяют задать следующее:

* Имя: идентификатор политики выполнения.
* Время ожидания задания: общее время, по прошествии которого служба заданий обработки эластичных баз данных отменит это задание.
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

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Изменение пользовательской политики выполнения
Измените нужную политику выполнения:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Отмена задания
Задания обработки эластичных баз данных поддерживают запросы на отмену заданий.  Если служба заданий обработки эластичных баз данных обнаруживает запрос на отмену выполняемого задания, она пытается остановить это задание.

Служба заданий обработки эластичных баз данных может выполнить отмену двумя разными способами:

1. Отмена выполняющихся задач: если отмена обнаружена во время выполнения задачи, попытка отмены применяется к текущему аспекту задачи.  Например, если при попытке отмены выполняется длительный запрос, служба пытается отменить этот запрос.
2. Отмена попыток выполнения задачи. Если управляющий поток обнаружит отмену до начала выполнения задачи, он не будет запускать задачу и объявит запрос отмененным.

Если поступает запрос на отмену родительского задания, он выполняется для самого родительского задания и всех его дочерних заданий.

Чтобы отправить запрос на отмену, используйте командлет **Stop-AzureSqlJobExecution** с параметром **JobExecutionId**.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Удаление задания по имени и журналу
Задания обработки эластичных баз данных поддерживают асинхронное удаление заданий. Задания можно отметить для удаления, и тогда система удалит такое задание и весь журнал этого задания, как только завершатся все операции выполнения этого задания. Система не отменяет активные операции выполнения заданий.  

Вместо этого необходимо вызвать командлет Stop-AzureSqlJobExecution, чтобы отменить активные выполнения задания.

Чтобы запустить удаление задания, выполните командлет **Remove-AzureSqlJob** с параметром **JobName**.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Создание пользовательской целевой базы данных
В заданиях обработки эластичных баз данных можно задать пользовательские целевые базы данных, которые можно использовать либо для непосредственного выполнения, либо для включения в пользовательскую группу баз данных. Так как **эластичные пулы** еще не поддерживаются API-интерфейсами PowerShell напрямую, достаточно просто создать пользовательскую целевую коллекцию баз данных, охватив таким образом все базы данных в пуле.

Задайте следующие переменные в соответствии с нужными сведениями о базе данных:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Создание пользовательского целевого семейства баз данных
Вы можете указать собственное целевое семейство в нескольких целевых базах данных. После создания группы баз данных можно связывать их с пользовательским целевым семейством.

Задайте следующие переменные в соответствии с нужной конфигурацией пользовательского семейства:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Добавление баз данных в пользовательское целевое семейство баз данных
Целевые базы данных можно связывать с целевыми пользовательскими семействами для создания группы баз данных. Когда создается задание, целевым объектом которого является пользовательская коллекция баз данных, оно расширяется на все базы данных, связанные с этой группой во время выполнения.

Добавление нужной базы данных в определенное пользовательское семейство:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Просмотр баз данных в целевом пользовательском семействе баз данных
Используйте командлет **Get-AzureSqlJobTarget** , чтобы получить дочерние базы данных в пользовательском семействе.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Создание задания для выполнения сценария в пользовательском семействе баз данных
Используйте командлет **New-AzureSqlJob** , чтобы создать задание в группе баз данных, определенной пользовательским семейством баз данных. Задания обработки эластичных баз данных распространяют задание на несколько дочерних заданий, каждое из которых соответствует определенной целевой пользовательской коллекции баз данных, и обеспечивают выполнение скрипта для каждой базы данных. Как и ранее, важно, чтобы сценарии были идемпотентными и защищенными от повторных попыток.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Сбор данных между базами данных
**Задания обработки эластичных баз данных** поддерживают выполнение запроса для группы баз данных и отправку результатов в таблицу указанной базы данных. Позже можно отправить таблице запрос, чтобы увидеть результаты запроса из каждой базы данных. Это обеспечивает асинхронный механизм выполнения запроса для множества баз данных. Аварийные ситуации, например временная недоступность одной из баз данных, обрабатываются автоматически с помощью повторных попыток.

Указанная целевая таблица создается автоматически (если она еще не существует), в соответствии со схемой возвращенного результирующего набора. Если выполнение скрипта возвращает несколько наборов результатов, задания обработки эластичных баз данных отправляют в указанную целевую таблицу только первый из них.

Следующий сценарий PowerShell можно использовать для выполнения сценария и сбора результатов в указанную таблицу. В этом сценарии предполагается, что были созданы сценарий T-SQL, который выводит один набор результатов, и целевое пользовательское семейство баз данных.

Задайте следующие переменные в соответствии с нужными сценарием, учетными данными и целью выполнения:

   ```
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
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Создание и запуск задания для сбора данных
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Создание расписания выполнения заданий с помощью триггера
Следующий сценарий PowerShell позволяет создать повторяющееся расписание. В этом сценарии используется интервал в одну минуту, но командлет New-AzureSqlJobSchedule также поддерживает параметры -DayInterval, -HourInterval, -MonthInterval и -WeekInterval. Расписания, которые выполняются только один раз, можно создавать с помощью параметра -OneTime.

Создание нового расписания
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Создание триггера для выполнения задания по расписанию
Вы можете определить триггер задания, который будет выполнять задание согласно расписанию. Следующий сценарий PowerShell позволяет создать триггер задания.

Задайте следующие переменные в соответствии с нужными заданием и расписанием:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Удаление запланированной связи для остановки выполнения задания по расписанию
Чтобы остановить регулярное выполнение задания с помощью триггера, можно удалить триггер.
Чтобы удалить триггер и остановить выполнение задания по расписанию, используйте командлет **Remove-AzureSqlJobTrigger** .

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Импорт в Excel результатов запроса к эластичной базе данных
 Результаты запроса можно импортировать в файл Excel.

1. Запустите Excel 2013.
2. Перейдите на ленту **Данные** .
3. Щелкните **Из других источников**, а затем — **Из SQL Server**.

   ![Импорт в Excel из других источников](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. В **мастере подключения к данным** введите имя сервера и учетные данные для входа. Нажмите кнопку **Далее**.
5. В диалоговом окне **Выберите базу данных, содержащую нужные сведения** выберите базу данных **ElasticDBQuery**.
6. Выберите таблицу **Клиенты** в представлении списка и нажмите кнопку **Далее**. Нажмите кнопку **Готово**.
7. В форме **Импорт данных** в разделе **Выберите, как следует просматривать эти данные в книге** выберите **Таблица** и нажмите кнопку **ОК**.

Все строки из таблицы **Клиенты**, хранящиеся в различных сегментах, попадают на лист Excel.

## <a name="next-steps"></a>Дополнительная информация
Теперь можно использовать функции обработки данных Excel. Для подключения к эластичной базе данных своих инструментов бизнес-аналитики и интеграции данных можно использовать строку подключения с именем сервера, именем базы данных и учетными данными. Убедитесь, что в качестве источника данных для вашего инструмента поддерживается SQL Server. На запрос к эластичной базе данных и внешние таблицы можно ссылаться таким же образом, как и на любую другую базу данных SQL Server и таблицы SQL Server, которые вы хотите подключить с помощью своего инструмента.

### <a name="cost"></a>Стоимость
Дополнительная плата за использование функции запросов к эластичной базе данных отсутствует. Но сейчас эта функция доступна только в базах данных и эластичных пулах уровня "Премиум" и "Критически важный для бизнеса" (предварительная версия) как конечная точка, а сегменты могут находиться на любом уровне служб.

Сведения о ценах см. на [странице с ценами на базу данных SQL](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
