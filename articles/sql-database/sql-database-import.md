---
title: Импорт BACPAC-файла для создания базы данных SQL Azure | Документация Майкрософт
description: Создайте базу данных SQL Azure, импортировав BACPAC-файл.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/10/2018
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: bd9554a18775cf98f4415ebd5d4b0d52edc53718
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894197"
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Импорт BACPAC-файла в новую базу данных SQL Azure

При необходимости импортировать базу данных из архива или при миграции с другой платформы можно импортировать схему и данные базы данных из [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файла. BACPAC-файл — это ZIP-файл с расширением BACPAC, содержащий метаданные и данные из базы данных SQL Server. BACPAC-файл можно импортировать из хранилища BLOB-объектов Azure (только уровня "Стандартный") или из локального хранилища. Чтобы обеспечить максимальную скорость импорта, рекомендуется указать более высокий уровень служб и производительности, например P6, и затем, после успешного импорта, понизить его до необходимой емкости. Кроме того, уровень совместимости базы данных после импорта основан на уровне совместимости базы данных-источника. 

> [!IMPORTANT] 
> После переноса базы данных в базу данных SQL Azure вы можете работать с ней при текущем уровне совместимости (уровень 100 для базы данных AdventureWorks2008R2) или при более высоком уровне. Дополнительные сведения о последствиях и параметрах работы базы данных с определенным уровнем совместимости см. в разделе [ALTER DATABASE (Transact-SQL) Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Уровень совместимости ALTER DATABASE (Transact-SQL)). Кроме того, в статье [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) вы можете получить сведения о дополнительных настройках уровня базы данных, связанных с уровнем совместимости.   >

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Импорт из BACPAC-файл с помощью портала Azure

Эта статья содержит указания по созданию базы данных SQL Azure из BACPAC-файла, расположенного в хранилище BLOB-объектов Azure, с помощью [портала Azure](https://portal.azure.com). С помощью портала Azure можно импортировать только BACPAC-файл из хранилища BLOB-объектов Azure.

Чтобы импортировать базу данных с помощью портала Azure, откройте страницу сервера, который нужно связать с базой данных, и щелкните **Импорт** на панели инструментов. Укажите учетную запись хранения и контейнер и выберите BACPAC-файл, который нужно импортировать. Выберите размер новой базы данных (обычно он такой же, как и у источника) и укажите учетные данные для целевого SQL Server.  

   ![Импорт базы данных](./media/sql-database-import/import.png)

Чтобы отслеживать ход выполнения операции импорта, откройте страницу логического сервера, содержащего импортируемую базу данных. Прокрутите ее вниз до раздела **Операции** и щелкните журнал **Импорт и экспорт**.

> [!NOTE]
> [Управляемый экземпляр Базы данных SQL Azure](sql-database-managed-instance.md) поддерживает импорт из файла BACPAC и с помощью других методов, указанных в этой статье. Миграция с помощью портала Azure сейчас не поддерживается.

### <a name="monitor-the-progress-of-an-import-operation"></a>Отслеживание операции импорта

Чтобы отслеживать ход выполнения операции импорта, откройте страницу логического сервера, на который импортируется база данных. Прокрутите ее вниз до раздела **Операции** и щелкните журнал **Импорт и экспорт**.
   
   ![импорт](./media/sql-database-import/import-history.png) ![состояние импорта](./media/sql-database-import/import-status.png)

Щелкните **Базы данных SQL**, чтобы проверить активность базы данных на сервере, и убедитесь, что для новой базы данных задано состояние **В сети**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>Импорт из BACPAC-файла с помощью SQLPackage

Импорт базы данных SQL с помощью программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) описывается в разделе о [параметрах и свойствах операции импорта](https://msdn.microsoft.com/library/hh550080.aspx#Import Parameters and Properties). Служебная программа SqlPackage поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

Рекомендуется использовать служебную программу SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Ниже приведена команда SQLPackage для примера сценария, демонстрирующего импорт базы данных **AdventureWorks2008R2** из локального хранилища на логический сервер базы данных SQL Azure, который называется **mynewserver20170403**. Этот сценарий показывает создание базы данных **myMigratedDatabase** с уровнем служб **Премиум** и целью обслуживания **P6**. Подставьте соответствующие значения для своей среды.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Логический сервер базы данных SQL Azure прослушивает порт 1433. Чтобы подключиться к логическому серверу базы данных SQL Azure из среды, ограничиваемой корпоративным брандмауэром, этот порт должен быть открыт в брандмауэре.
>

В этом примере показано, как импортировать базу данных с помощью SqlPackage.exe, используя универсальную аутентификацию Active Directory.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>Импорт из BACPAC-файла с помощью PowerShell

Используйте командлет [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport), чтобы отправить запрос на импорт базы данных к службе Базы данных SQL Azure. Операция импорта может занять некоторое время в зависимости от размера базы данных.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Чтобы проверить состояние запроса на импорт, используйте командлет [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что импорт завершен.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Еще один пример сценария приведен в разделе [Импорт базы данных из BACPAC-файла](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="import-using-other-methods"></a>Импорт с использованием других методов

Импорт также можно выполнить с помощью следующих мастеров:

- [мастер импорта приложения уровня данных в SQL Server Management Studio](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard);
- [мастер импорта и экспорта SQL Server](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Дополнительная информация
* Чтобы научиться подключаться к импортированной базе данных SQL и отправлять к ней запросы, ознакомьтесь со статьей [Подключение к базе данных SQL с помощью SQL Server Management Studio и выполнение пробного запроса T-SQL](sql-database-connect-query-ssms.md).
* Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Описание процесса миграции базы данных SQL Server в базу данных SQL Azure, включая рекомендации по его использованию, см. в [этой статье](sql-database-cloud-migrate.md).
* Чтобы узнать, как безопасно управлять ключами к хранилищу данных и	подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide). 


  

