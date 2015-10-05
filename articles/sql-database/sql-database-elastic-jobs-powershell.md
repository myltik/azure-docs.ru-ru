<properties 
	pageTitle="Создание заданий обработки эластичных баз данных и управление ими с помощью PowerShell" 
	description="Управление пулами базы данных SQL Azure с помощью PowerShell" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="ddove; sidneyh" />

# Создание заданий обработки эластичных баз данных для Базы данных SQL и управление им с помощью PowerShell (предварительная версия)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)

## Обзор

Компонент **Задания обработки эластичных баз данных** (предварительная версия) обеспечивает надежное выполнение сценария Transact-SQL (T-SQL) или применение DACPAC, включая пользовательскую коллекцию баз данных, все базы данных в [пуле эластичных баз данных (предварительная версия)](sql-database-elastic-pool.md) или набор сегментов (созданный с помощью [клиентской библиотеки эластичных баз данных](sql-database-elastic-database-client-library.md)). В предварительной версии **Задания обработки эластичных баз данных** являются размещаемой у клиента облачной службой Azure, которая позволяет выполнять динамические и запланированные административные задачи, которые называются заданиями. С помощью этого компонента можно без проблем управлять большими количествами баз данных SQL Azure, запуская сценарии Transact-SQL для выполнения таких административных задач, как смена схем, управление учетными данными, обновления ссылочных данных, сбор данных о производительности и сбор телеметрии клиентов. Дополнительные сведения о заданиях обработки эластичных баз данных см. в статье [Обзор службы заданий обработки эластичных баз данных](sql-database-elastic-jobs-overview.md).

С помощью API-интерфейсов PowerShell для **заданий обработки эластичных баз данных** вы можете определить, какие группы баз данных, для которых будут выполняться сценарии. В настоящий момент функции **заданий обработки эластичных баз данных** на портале Azure имеют ограниченные возможности и работают только в пулах эластичных баз данных.

**Задания обработки эластичных баз данных** (предварительная версия) используют несколько компонентов Azure, чтобы определять выполняемые задания и время их выполнения, выполнять задания, отслеживать успешное или неудачное выполнение заданий и при необходимости указывать назначение результатов запросов. Так как API-интерфейсы Powershell, включенные в эту предварительную версию, содержат больше функций, чем исходная предварительная версия на портале, рекомендуется установить компоненты **заданий обработки эластичных баз данных** последних версий. Если они уже установлены, вы можете просто обновить компоненты **заданий обработки эластичных баз данных**. Дополнительные сведения об установке с помощью [Nuget](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.Jobs) см. в статье [Установка компонентов заданий обработки эластичных баз данных](sql-database-elastic-jobs-service-installation.md).

В этой статье рассказывается, как подготовить все необходимое для создания **заданий обработки эластичных баз данных** и управления ими (кроме подписки Azure). Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку "БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ". Оформив подписку, вернитесь к этой статье. В этом разделе более подробным образом рассматривается пример из статьи [Приступая к работе с инструментами эластичной базы данных](sql-database-elastic-scale-get-started.md). По завершении вы узнаете, как создавать и настраивать задания для выполнения административных операций в группе общих баз данных, определенной **набором сегментов**, или пользовательской коллекции баз данных.

## Предварительные требования
* Подписка Azure. Зарегистрироваться в пробной версии, которая доступна бесплатно в течение одного месяца, можно [здесь](http://azure.microsoft.com/pricing/free-trial/).
* Для начала необходимо скачать и импортировать пакет PowerShell **Задания обработки эластичных баз данных**, а также установить компоненты заданий обработки эластичных баз данных. Выполните действия из раздела [Установка службы заданий обработки эластичных баз данных](sql-database-elastic-jobs-service-installation.md).
* Версия Azure PowerShell не ниже 0.8.16. Установите последнюю версию (0.9.5) с помощью [установщика веб-платформы](http://go.microsoft.com/fwlink/p/?linkid=320376). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).

### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор (**-SubscriptionId**) или имя подписки (**-SubscriptionName**). Если у вас несколько подписок, запустить командлет **Get-AzureSubscription** и скопировать необходимые сведения о подписке из набора результатов. Получив сведения о подписке, выполните следующий командлет, чтобы сделать ее подпиской по умолчанию, в частности целью для создания заданий и управления ими:

	Select-AzureSubscription -SubscriptionId {SubscriptionID}

Для создания и выполнения сценариев PowerShell в заданиях обработки эластичных баз данных рекомендуется использовать [интегрированную среду сценариев PowerShell ](https://technet.microsoft.com/library/dd315244.aspx).

## Объекты заданий обработки эластичных баз данных

В следующей таблице перечислены все типы объектов **заданий обработки эластичных баз данных**, а также их описания и связанные API-интерфейсы PowerShell.

<table style="width:100%">
  <tr>
    <th>Тип объекта</th>
    <th>Описание</th>
    <th>Связанные API-интерфейсы PowerShell</th>
  </tr>
  <tr>
    <td>Учетные данные</td>
    <td>Имя пользователя и пароль, используемые при подключении к базам данных для выполнения сценариев или применением файлов DACPAC. <p>Пароль зашифровывается перед отправкой в базу данных заданий обработки эластичных баз данных и хранения в ней. Пароль шифруется службой заданий обработки эластичных баз данных с помощью учетных данных, созданных и отправленных из сценария установки.</td>
	<td><p>Get-AzureSqlJobCredential</p>
	<p>New-AzureSqlJobCredential</p><p>Set-AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Скрипт</td>
    <td>Сценарий Transact-SQL для выполнения между базами данных. Сценарий должен быть создан идемпотентным, так как служба будет повторять попытку выполнения сценария в случае сбоя.
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
    <td>Job</td>
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

## Поддерживаемые типы групп заданий обработки эластичных баз данных
**Задания обработки эластичных баз данных** позволяют выполнять сценарии Transact-SQL (T-SQL) или применять файлы DACPAC в группе баз данных. Когда задание отправляется на выполнение в группе баз данных, задания обработки эластичных баз данных "развернут" задание в дочерние задания, каждое из которых выполняет нужную задачу в отдельной базе данных из группы.
 
Ниже приводится список поддерживаемых в данный момент типов групп:

* [Карта сегментов](sql-database-elastic-scale-shard-map-management.md): при отправке задания с целевой картой сегментов оно сначала отправляет карте сегментов запрос, чтобы определить текущий набор сегментов, а затем создает дочерние задания для каждого сегмента в карте.
* Пользовательское семейство: указывает на определяемый пользователем набор баз данных. При отправке задания с целевым пользовательским семейством оно создает дочерние задания для каждой базы данных, определенной в этом семействе.

## Настройка подключения к заданиям обработки эластичных баз данных
После загрузки модуля PowerShell необходимо настроить подключение к *базе данных управления* заданиями обработки эластичных баз данных, чтобы использовать API-интерфейсы заданий. При вызове этого командлета открывается окно учетных данных, где пользователь должен указать имя и пароль, предоставленные при установке заданий обработки эластичных баз данных. Во всех примерах из этого раздела предполагается, что первый этап уже выполнен.

Откройте подключение к заданиям обработки эластичных баз данных:

	Use-AzureSqlJobConnection -CurrentAzureSubscription 

## Зашифрованные учетные данные в заданиях обработки эластичных баз данных
Учетные данные баз данных можно вставлять в *базу данных управления* заданиями обработки эластичных баз данных с зашифрованным паролем. Это необходимо для хранения учетных данных, что позволяет выполнять задания позже, в том числе использовать расписания заданий.
 
Шифрование работает с помощью сертификата, созданного сценарием установки. Сценарий установки создает и отправляет сертификат в облачную службу Azure для шифрования сохраненных паролей шифрования. Позже облачная служба Azure сохраняет общедоступный ключ в *базу данных управления* заданиями обработки эластичных баз данных, которая позволяет API PowerShell или интерфейсу портала Azure для шифрования предоставленного пароля без необходимости локальной установки сертификата.
 
Хотя пароли учетных записей зашифрованы и защищены от пользователей с правами только на чтение объектов заданий обработки эластичных баз данных, злоумышленник с правами на чтение и запись этих объектов может извлечь пароль. Учетные данные рассчитаны на повторное использования при разных запусках задания. Учетные данные передаются целевым базам данных при установке соединений. На данный момент на целевых базах данные, используемых для каждой учетной записи, отсутствуют ограничения, поэтому злоумышленник может добавить цель для базы данных, которой он может управлять, а затем запустить задание, нацеленное на эту базу данных, чтобы собрать сведения о пароле учетной записи.

Для заданий обработки эластичных баз данных существуют рекомендации:

* Предоставляйте доступ к API-интерфейсам только доверенным лицам.
* Учетные данные должны иметь меньше всего прав, необходимых для выполнения задания. Дополнительные сведения можно найти в статье по SQL Server [Авторизация и разрешения](https://msdn.microsoft.com/library/bb669084.aspx) на сайте MSDN.

### Создание зашифрованных учетных данных для выполнения заданий между базами данных
Чтобы создать новые зашифрованные учетные данные, командлет Get-Credential запросит имя пользователя и пароль, которые можно передать командлету **New-AzureSqlJobCredential**.

	$credentialName = "{Credential Name}"
	$databaseCredential = Get-Credential
	$credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
	Write-Output $credential

### Обновление учетных данных
Чтобы обновить существующую учетную запись в случае изменения пароля, используйте командлет **Set-AzureSqlJobCredential** и укажите параметр **CredentialName**.

	$credentialName = "{Credential Name}"
	Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## Определение целевой карты сегментов эластичных баз данных
Выполните задание на всех базах данных в наборе сегментов (созданном с помощью [клиентской библиотеки эластичных баз данных](sql-database-elastic-database-client-library.md)), указав карту сегментов как целевую базу данных. Для этого примера необходимо создать сегментированное приложение с помощью клиентской библиотеки эластичных баз данных. Скачайте и запустите пример [Начало работы с инструментами для эластичных баз данных](sql-database-elastic-scale-get-started.md).

###Создание диспетчера сопоставления сегментов с помощью примера приложения

Здесь мы создадим диспетчер сопоставления сегментов вместе с несколькими сегментами, а затем выполним вставку данных в сегменты. Если вы уже знакомы с настройкой сегментов, то вы можете пропустить описанные ниже действия и перейти к следующему разделу.

1. Постройте и запустите пример приложения **Начало работы с инструментами для эластичных баз данных**. Следуйте инструкциям до шага 7 в разделе [Загрузка и запуск примера приложения](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). В конце шага 7 появится следующее окно командной строки:

	![окно командной строки.][1]

2.  В окне командной строки введите "1" и нажмите клавишу **ВВОД**. Это позволит создать диспетчер сопоставления сегментов и добавить два сегмента на сервер. Затем введите "3" и нажмите клавишу **ВВОД**. Повторите это действие четыре раза. Это позволит вставить строки демонстрационных данных в свои сегменты.
  
3.  На [портале предварительной версии Azure](https://portal.azure.com) должны появиться три новые базы данных на сервере v12:

	![Подтверждение Visual Studio][2]

Теперь создайте целевую карту сегментов с помощью командлета **New-AzureSqlJobTarget**. Для этого необходимо указать управляющую базу данных карт сегментов как целевую базу данных, а затем указать в качестве цели конкретную карту сегментов.

	$shardMapCredentialName = "{Credential Name}"
	$shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
	$shardMapDatabaseServerName = "{ShardMapServerName}"
	$shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
	$shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
	$shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
	Write-Output $shardMapTarget

## Создание сценария T-SQL для выполнения между базами данных

При создании сценариев T-SQL для выполнения настоятельно рекомендуется создавать их идемпотентными и защищенными от сбоев. Задания обработки эластичных баз данных повторяют попытку выполнения сценария в случае сбоя, независимо от типа ошибки.

Создайте и сохраните сценарий для выполнения с помощью командлета **New-AzureSqlJobContent**, указав параметры **-ContentName** и **-CommandText**.

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

### Создание нового сценария из файла
Если в файле определен сценарий T-SQL, для его импорта можно использовать следующий сценарий:

	$scriptName = "My Script Imported from a File"
	$scriptPath = "{Path to SQL File}"
	$scriptCommandText = Get-Content -Path $scriptPath
	$script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
	Write-Output $script

### Обновление сценария T-SQL для выполнения между базами данных  

Приведенный ниже сценарий PowerShell можно использовать для обновления командного текста T-SQL для существующего сценария.

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

### Обновите определение существующего сценария

	Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

##Создайте задание для выполнения скрипта в карте сегментов

Следующий сценарий PowerShell позволяет запустить задание для выполнения сценария в каждом сегменте на карте сегментов динамического масштабирования.

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

##Выполнение задания 

Следующий сценарий PowerShell позволяет выполнить существующее задание:

Измените следующую переменную в соответствии с именем выполняемого задания:

	$jobName = "{Job Name}"
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
	Write-Output $jobExecution
 
## Получение состояния выполнения одного задания

Используйте командлеты **Get-AzureSqlJobExecution** с параметром **JobExecutionId**, чтобы просмотреть состояние выполнения задания.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
	Write-Output $jobExecution

Используйте такой же командлет **Get-AzureSqlJobExecution** с параметром **IncludeChildren**, чтобы просмотреть состояние выполнения дочерних заданий, в том числе конкретное состояние выполнения каждого задания в каждой целевой базе данных задания.

	$jobExecutionId = "{Job Execution Id}"
	$jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
	Write-Output $jobExecutions 

## Просмотр состояния выполнения нескольких заданий

У командлета **Get-AzureSqlJobExecution** есть несколько необязательных параметров, позволяющих увидеть ход выполнения нескольких заданий, отфильтрованных по указанным параметрам. Ниже показаны некоторые из возможных способов использования командлета Get-AzureSqlJobExecution:

Получение сведений о выполнении всех активных заданий верхнего уровня:

	Get-AzureSqlJobExecution

Получение сведений о выполнении всех заданий верхнего уровня, включая неактивные задания:

	Get-AzureSqlJobExecution -IncludeInactive

Получение состояния выполнения всех дочерних заданий, включая неактивные, по указанному идентификатору выполнения задания:

	$parentJobExecutionId = "{Job Execution Id}"
	Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Получение состояния выполнения всех заданий, созданных с помощью сочетания расписания и задания, включая неактивные:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Получение сведения обо всех заданиях в указанной карте сегментов, включая неактивные:

	$shardMapServerName = "{Shard Map Server Name}"
	$shardMapDatabaseName = "{Shard Map Database Name}"
	$shardMapName = "{Shard Map Name}"
	$target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Получение сведения обо всех заданиях в указанном пользовательском семействе, включая неактивные:

	$customCollectionName = "{Custom Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Получение списка выполнения задач в определенном задании:

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Write-Output $jobTaskExecutions 

Получение сведений о выполнении задачи:

Следующий сценарий PowerShell позволяет просмотреть сведения о выполнении задачи, что особенно полезно при отладке сбоев выполнения.

	$jobTaskExecutionId = "{Job Task Execution Id}"
	$jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
	Write-Output $jobTaskExecution

## Получение ошибок при выполнении задач

Объект JobTaskExecution включает свойство Lifecycle для жизненного цикла задачи и свойство Message. В случае сбоя при выполнении задачи свойство Lifecycle принимает значение *Failed*, а свойство Message содержит сообщение об исключении и его стек. Если задание завершилось неудачно, важно просмотреть сведения задачах, которые не были выполнены в определенном задании.

	$jobExecutionId = "{Job Execution Id}"
	$jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
	Foreach($jobTaskExecution in $jobTaskExecutions) 
		{
		if($jobTaskExecution.Lifecycle -ne 'Succeeded')
    		{
        	Write-Output $jobTaskExecution
    		}
		}

## Ожидание завершения выполнения задания

Следующий сценарий PowerShell позволяет дождаться завершения задачи:

	$jobExecutionId = "{Job Execution Id}"
	Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## Создание пользовательской политики выполнения

Задания обработки эластичных баз данных поддерживают создание пользовательских политик выполнения, которые можно применять при запуске заданий.
  
В настоящий момент политики выполнения позволяют задать следующее:

* Имя: идентификатор политики выполнения.
* Время ожидания задания: общее время до отмены задания службой заданий обработки эластичных баз данных.
* Исходный интервал повторных попыток: интервал ожидания до первой повторной попытки.
* Максимальный интервал повторных попыток: предел используемых интервалов повторных попыток.
* Коэффициент роста интервала повторных попыток: коэффициент, используемый для вычисления следующего интервала между повторными попытками. Используется следующая формула: (Исходный интервал повторных попыток) * Math.pow((Коэффициент роста интервала), (Число повторных попыток) - 2). 
* Максимальное число попыток: максимальное число повторных попыток, выполняемых в задании.

В политике выполнения по умолчанию используются следующие значения:

* Имя: политика выполнения по умолчанию
* Время ожидания задания: 1 неделя
* Исходный интервал повторных попыток: 100 миллисекунд
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
	$executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $executionPolicy

### Изменение пользовательской политики выполнения

Измените нужную политику выполнения:

	$executionPolicyName = "{Execution Policy Name}"
	$initialRetryInterval = New-TimeSpan -Seconds 15
	$jobTimeout = New-TimeSpan -Minutes 30
	$maximumAttempts = 999999
	$maximumRetryInterval = New-TimeSpan -Minutes 1
	$retryIntervalBackoffCoefficient = 1.5
	$updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
	Write-Output $updatedExecutionPolicy
 
## Отмена задания

Задания обработки эластичных баз данных поддерживают запросы на отмену заданий. Если служба заданий обработки эластичных баз данных обнаружат запрос на отмену выполняемого задания, она попытается остановить задачу.

Служба заданий обработки эластичных баз данных может выполнить отмену двумя разными способами:

1. Отмена выполняющихся заданий: если отмена обнаружена во время выполнения задачи, произойдет попытка отмены в существующем аспекте задачи. Например: если при попытке отмены выполняются длинный запрос, произойдет попытка его отмены.
2. Отмена повторных попыток задач: если поток управления обнаружит отмену, он не будет запускать задачу и объявит запрос отмененным.

В случае запроса отмены родительского задания этот запрос будет выполнен для родительского задания и всех его дочерних заданий.
 
Чтобы отправить запрос на отмену, используйте командлет **Stop-AzureSqlJobExecution** с параметром **JobExecutionId**.

	$jobExecutionId = "{Job Execution Id}"
	Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## Удаление задания по имени и журналу

Задания обработки эластичных баз данных поддерживают асинхронное удаление заданий. Задания могут быть отмечены для удаления, а система удалит задание и весь его журнал после завершения всех выполнений задания. Система не будет автоматически отменять активные выполнения заданий.

Вместо этого необходимо вызвать командлет Stop-AzureSqlJobExecution, чтобы отменить активные выполнения задания.

Чтобы запустить удаление задания, используйте командлет **Remove-AzureSqlJob** с параметром **JobName**.

	$jobName = "{Job Name}"
	Remove-AzureSqlJob -JobName $jobName
 
## Создание пользовательской целевой базы данных
В заданиях обработки эластичных баз данных можно задать пользовательские целевые базы данных, которые можно использовать либо для непосредственного выполнения, либо для включения в пользовательскую группу баз данных. Так как **задания обработки эластичных баз данных** еще не поддерживаются API-интерфейсами PowerShell напрямую, достаточно создать пользовательские целевые базу данных и семейство, которое включает все базы данных в пуле.

Задайте следующие переменные в соответствии с нужными сведениями о базе данных:

	$databaseName = "{Database Name}"
	$databaseServerName = "{Server Name}"
	New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## Создание пользовательского целевого семейства баз данных
Вы можете указать собственное целевое семейство в нескольких целевых базах данных. После создания группы баз данных можно связывать их с пользовательским целевым семейством.

Задайте следующие переменные в соответствии с нужной конфигурацией пользовательского семейства:

	$customCollectionName = "{Custom Database Collection Name}"
	New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### Добавление баз данных в пользовательское целевое семейство баз данных

Целевые базы данных можно связывать с целевыми пользовательскими семействами для создания группы баз данных. При создании задания с целевым пользовательским семейством баз данных оно будет расширено до баз данных, связанных с группой во время выполнения.

Добавление нужной базы данных в определенное пользовательское семейство:

	$serverName = "{Database Server Name}"
	$databaseName = "{Database Name}"
	$customCollectionName = "{Custom Database Collection Name}"
	Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### Просмотр баз данных в целевом пользовательском семействе баз данных

Используйте командлет **Get-AzureSqlJobTarget**, чтобы получить дочерние базы данных в пользовательском семействе.
 
	$customCollectionName = "{Custom Database Collection Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
	Write-Output $childTargets

### Создание задания для выполнения сценария в пользовательском семействе баз данных

Используйте командлет **New-AzureSqlJob**, чтобы создать задание в группе баз данных, определенной пользовательским семейством баз данных. Задания обработки эластичных баз данных расширят задание до нескольких дочерних заданий, каждое из которых соответствует базе данных, связанной с целевым пользовательским семейством, и обеспечивают выполнение сценария для каждой базы данных. Как и ранее, важно, чтобы сценарии были идемпотентными и защищенными от повторных попыток.

	$jobName = "{Job Name}"
	$scriptName = "{Script Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
	Write-Output $job

## Сбор данных между базами данных

**Задания обработки эластичных баз данных** поддерживают выполнение запроса для группы баз данных и отправку результатов в таблицу указанной базы данных. Позже можно отправить таблице запрос, чтобы увидеть результаты запроса из каждой базы данных. Это обеспечивает асинхронный механизм выполнения запроса для множества баз данных. Аварийные ситуации, например временная недоступность одной из баз данных, обрабатываются автоматически с помощью повторных попыток.

Указанная целевая таблица будет автоматически создана (если ее еще не существует) в соответствии со схемой возвращенного набора результатов. Если после выполнения сценария возвращается несколько наборов результатов, задания обработки эластичных баз данных будут отправлять в указанную целевую таблицу только первый из них.

Следующий сценарий PowerShell можно использовать для выполнения сценария и сбора результатов в указанную таблицу. В этом сценарии предполагается, что были созданы сценарий T-SQL, который выводит один набор результатов, и целевое пользовательское семейство баз данных.

Задайте следующие переменные в соответствии с нужными сценарием, учетными данными и целью выполнения:

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

### Создание и запуск задания для сбора данных
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
	Write-Output $job
	$jobExecution = Start-AzureSqlJobExecution -JobName $jobName
	Write-Output $jobExecution

## Создание расписания выполнения заданий с помощью триггера

Следующий сценарий PowerShell позволяет создать повторяющееся расписание. В этом сценарии используется интервал в минутах, но командлет New-AzureSqlJobSchedule также поддерживает параметры -DayInterval, -HourInterval, -MonthInterval и -WeekInterval. Расписания, которые выполняются только один раз, можно создавать с помощью параметра -OneTime.

Создание нового расписания

	$scheduleName = "Every one minute"
	$minuteInterval = 1
	$startTime = (Get-Date).ToUniversalTime()
	$schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
	Write-Output $schedule

### Создание триггера для выполнения задания по расписанию

Вы можете определить триггер задания, который будет выполнять задание согласно расписанию. Следующий сценарий PowerShell позволяет создать триггер задания.

Задайте следующие переменные в соответствии с нужными заданием и расписанием:

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	$jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
	Write-Output $jobTrigger

### Удаление запланированной связи для остановки выполнения задания по расписанию

Чтобы остановить регулярное выполнение задания с помощью триггера, можно удалить триггер. Чтобы удалить триггер и остановить выполнение задания по расписанию, используйте командлет **Remove-AzureSqlJobTrigger**.

	$jobName = "{Job Name}"
	$scheduleName = "{Schedule Name}"
	Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName

### Получение триггеров заданий, связанных с расписанием

Следующий сценарий PowerShell позволяет получить и вывести на экран триггеры заданий, зарегистрированных с определенным расписанием.

	$scheduleName = "{Schedule Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
	Write-Output $jobTriggers

### Получение триггеров, связанных с заданием 

Следующий сценарий PowerShell позволяет получить и вывести на экран расписания, содержащие зарегистрированное задание.

	$jobName = "{Job Name}"
	$jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
	Write-Output $jobTriggers

## Создание развертывания приложения на уровне данных (DACPAC) для выполнения в базах данных

Задания обработки эластичных баз данных позволяют развернуть приложение на уровне данных (DACPAC) в группе баз данных Указания по созданию DACPAC см. в данной документации. Чтобы задания обработки эластичных баз данных могли развернуть DACPAC в группе баз данных, служба должна иметь доступ к DACPAC. Рекомендуется отправить созданный файл DACPAC в хранилище Azure и создать подписанный URI для DACPAC.

Следующий сценарий PowerShell позволяет вставить файл DACPAC в задания обработки эластичных баз данных:

	$dacpacUri = "{Uri}"
	$dacpacName = "{Dacpac Name}"
	$dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
	Write-Output $dacpac

### Изменение развертывания приложения на уровне данных (DACPAC) для выполнения в базах данных

Имеющиеся файлы DACPAC, зарегистрированные в заданиях обработки эластичных баз данных, можно изменить, чтобы они указывали на новые URI. Приведенный ниже сценарий PowerShell можно использовать для обновления командного текста URI в файле DACPAC в имеющемся зарегистрированном развертывании.

	$dacpacName = "{Dacpac Name}"
	$newDacpacUri = "{Uri}"
	$updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
	Write-Output $updatedDacpac

## Создание задания для применения развертывания приложения на уровне данных (DACPAC) в базах данных

После создания DACPAC в службе заданий обработки эластичных баз данных можно создать задание для применения DACPAC к группе баз данных. Следующий сценарий PowerShell позволяет создать задание DACPAC в пользовательском семействе баз данных:

	$jobName = "{Job Name}"
	$dacpacName = "{Dacpac Name}"
	$customCollectionName = "{Custom Collection Name}"
	$credentialName = "{Credential Name}"
	$target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
	$job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $dacpacName -TargetId $target.TargetId
	Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->

<!---HONumber=Sept15_HO4-->