<properties 
    pageTitle="Создание и экспорт BACPAC-файла базы данных SQL Azure с помощью PowerShell" 
    description="Создание и экспорт BACPAC-файла базы данных SQL Azure с помощью PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Создание и экспорт BACPAC-файла базы данных SQL Azure с помощью PowerShell

**Отдельная база данных**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


В этой статье приведены указания по экспорту BACPAC-файла базы данных SQL Azure с помощью PowerShell.

[BACPAC-файл](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) — это файл с расширением BACPAC, который содержит схему базы данных и данные. Основной сценарий использования BACPAC-файлов — перемещение базы данных с одного сервера на другой с целью [переноса локальной базы данных в облако](sql-database-cloud-migrate.md), а также для архивации существующих баз данных в открытом формате.

> [AZURE.NOTE]BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статье [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md).


BACPAC-файл экспортируется в контейнер больших двоичных объектов хранилища Azure, который можно загрузить после успешного завершения операции.


Для работы с этой статьей необходимо следующее:

- Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **БЕСПЛАТНАЯ ПРОБНАЯ ВЕРСИЯ**. Оформив подписку, вернитесь к этой статье.
- База данных Azure SQL. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
- [Учетная запись хранения Azure](storage-create-storage-account.md) с контейнером больших двоичных объектов для хранения BACPAC-файла. В данный момент для учетной записи хранения необходимо использовать классическую модель развертывания, поэтому выберите **Классическая** при создании учетной записи хранения.
- Azure PowerShell. Вы можете скачать и установить модуль Azure PowerShell, запустив [установщик веб-платформы Майкрософт](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Дополнительные сведения можно узнать в статье [Установка и настройка Azure PowerShell](powershell-install-configure.md).



## Настройка учетных данных и выбор подписки

Для начала установите доступ к учетной записи Azure. Для этого запустите PowerShell и выполните указанный ниже командлет. На экране входа в систему укажите те же адрес электронной почты и пароль, которые используются для входа на портал Azure.

	Add-AzureAccount

После успешного входа на экране будут отображаться некоторые сведения, включая идентификатор, под которым вы вошли в систему, и подписки Azure, к которым у вас есть доступ.


### Выбор подписки Azure

Для выбора подписки вам понадобится идентификатор или имя подписки (**-SubscriptionName**). Идентификатор подписки можно скопировать из данных, которые были показаны на предыдущем этапе. Если у вас несколько подписок и вам нужны дополнительные данные, выполните командлет **Get-AzureSubscription** и скопируйте нужные данные из полученных результатов. Если у вас есть подписка, запустите следующий командлет:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

После успешного выполнения командлета **Select-AzureSubscription** вы вернетесь в командную строку PowerShell. Если подписок несколько, выполните командлет **Get-AzureSubscription** и убедитесь в том, что в подписке, которую необходимо использовать, отображается **IsCurrent: True**.


## Настройка переменных для конкретной среды

В некоторых переменных приведенные для примера значения необходимо заменить на значения, соответствующие вашей базе данных и учетной записи хранения.

Замените имена сервера и базы данных на имена сервера и базы данных вашей учетной записи. В качестве имени большого двоичного объекта введите имя BACPAC-файла, который будет создан. Имя файла может быть любым, но необходимо указать расширение .bacpac.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetoexport"
    $BlobName = "filename.bacpac"

Чтобы получить эти значения, перейдите к учетной записи хранения на [портале Azure](https://portal.azure.com). Чтобы найти первичный ключ доступа, последовательно выберите пункты **Все параметры** и **Ключи** в колонке учетной записи хранения.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Создание указателя на сервер и учетную запись хранения

После выполнения командлета **Get-Credential** откроется окно с запросом имени пользователя и пароля. Введите имя пользователя и пароль администратора SQL Server (это не имя пользователя и пароль учетной записи Azure).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Экспорт базы данных

Эта команда отправляет в службу запрос об экспорте базы данных. Операция экспорта может занять некоторое время в зависимости от размера базы данных.

> [AZURE.IMPORTANT]Для получения транзакционно согласованного BACPAC-файла сначала [создайте копию базы данных](sql-database-copy-powershell.md), а затем экспортируйте эту копию.


    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Отслеживание хода выполнения операции экспорта

Состояние запроса можно проверить, выполнив командлет **Start-AzureSqlDatabaseExport**. Обычно при проверке состояния сразу же после отправки запроса возвращается результат **Состояние: ожидание** или **Состояние: выполняется**, поэтому результат **Состояние: завершено** может появиться не с первого раза.

Для выполнения этой команды потребуется пароль. Введите имя пользователя и пароль администратора для сервера SQL.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Экспорт базы данных SQL с помощью сценария PowerShell


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Дальнейшие действия

- [Импорт базы данных SQL Azure](sql-database-import-powershell.md)


## Дополнительные ресурсы

- [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
- [Отработка аварийного восстановления](sql-database-disaster-recovery-drills.md)
- [База данных SQL — документация](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->