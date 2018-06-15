---
title: Экспорт базы данных SQL Azure в BACPAC-файл | Документация Майкрософт
description: Экспорт базы данных SQL Azure в BACPAC-файл с помощью портала Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: conceptual
ms.openlocfilehash: c2f29d8c660e3d39f91bcdd97209d2e88f5ae864
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647958"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Экспорт базы данных SQL Azure в BACPAC-файл

Если нужно экспортировать базу данных для создания архива или для перехода на другую платформу, можно экспортировать схему и данные базы данных в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл. BACPAC-файл — это ZIP-файл с расширением BACPAC, содержащий метаданные и данные из базы данных SQL Server. BACPAC-файл можно сохранить в хранилище BLOB-объектов Azure или локальном хранилище, а затем импортировать обратно в базу данных SQL Azure или локальный экземпляр SQL Server. 

> [!IMPORTANT] 
> Автоматический экспорт базы данных SQL Azure не используется с 1 марта 2017 года. Вы можете использовать [долгосрочное хранение резервных копий](sql-database-long-term-retention.md
) или [службу автоматизации Azure](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md), чтобы периодически архивировать базы данных SQL с помощью PowerShell согласно выбранному расписанию. Скачайте [пример сценария PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) из репозитория Github.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Рекомендации при экспорте базы данных SQL Azure

* Чтобы экспорт был транзакционно согласованным, необходимо обеспечить отсутствие операций записи во время экспорта или экспортировать данные из [транзакционно согласованной копии](sql-database-copy.md) базы данных SQL Azure.
* Максимальный размер BACPAC-файла при экспорте в хранилище BLOB-объектов составляет 200 ГБ. Для архивации BACPAC-файла большего размера выполняйте экспорт в локальное хранилище.
* Экспорт BACPAC-файла в службу хранилища Azure уровня "Премиум" с использованием методов, описанных в этой статье, не поддерживается.
* Если операция экспорта из базы данных SQL Azure длится более 20 часов, ее можно отменить. Для повышения производительности во время экспорта можно сделать следующее.
  * Временно повысить уровень служб.
  * Прекратить все операции чтения и записи во время экспорта.
  * Используйте для всех больших таблиц [кластеризованный индекс](https://msdn.microsoft.com/library/ms190457.aspx) со значениями, отличными от NULL. Без кластеризованных индексов экспорт может завершиться ошибкой, если он длится больше 6–12 часов. Это обусловлено тем, что службам экспорта требуется выполнить проверку таблицы, чтобы экспортировать всю таблицу. Хороший способ определить, оптимизированы ли таблицы для экспорта, — выполнить **DBCC SHOW_STATISTICS** и убедиться, что значение *RANGE_HI_KEY* не равно NULL и имеет хорошее распределение. Дополнительную информацию см. в разделе [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPAC-файлы не предназначены для операций службы архивации и восстановления. База данных SQL Azure автоматически создает резервные копии для каждой пользовательской базы данных. Дополнительные сведения см. в статьях [Обзор обеспечения непрерывности бизнес-процессов с помощью базы данных SQL Azure](sql-database-business-continuity.md) и[Подробнее о резервном копировании базы данных SQL](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Экспорт в BACPAC-файл с помощью портала Azure

Чтобы экспортировать базу данных с помощью [портала Azure](https://portal.azure.com), откройте страницу для базы данных и щелкните **Экспорт** на панели инструментов. Введите имя BACPAC-файла, укажите учетную запись хранения Azure и контейнер для экспорта, а также учетные данные для подключения к базе данных-источнику.  

![Экспорт базы данных](./media/sql-database-export/database-export.png)

Чтобы отслеживать ход выполнения операции экспорта, откройте страницу логического сервера, содержащего экспортируемую базу данных. Прокрутите ее вниз до раздела **Операции** и щелкните журнал **Импорт и экспорт**.

![журнал экспорта](./media/sql-database-export/export-history.png)
![состояние журнала экспорта](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Экспорт в BACPAC-файл с помощью служебной программы SQLPackage

Экспорт базы данных SQL с помощью программы командной строки [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) описывается в разделе о [параметрах и свойствах операции экспорта](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). Служебная программа SqlPackage поставляется вместе с последними версиями [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) и [SQL Server Data Tools для Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Кроме того, вы можете скачать последнюю версию [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) непосредственно из Центра загрузки Майкрософт.

Рекомендуется использовать служебную программу SqlPackage для масштабирования и обеспечения производительности в большинстве рабочих сред. Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

В этом примере показано, как экспортировать базу данных с помощью SqlPackage.exe, используя универсальную аутентификацию Active Directory.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Экспорт в BACPAC-файл с помощью SQL Server Management Studio (SSMS)

Самые последние версии SQL Server Management Studio также содержат мастер для экспорта базы данных SQL Azure в BACPAC-файл. Ознакомьтесь с разделом [Export a Data-tier Application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) (Экспорт приложения уровня данных).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Экспорт в BACPAC-файл с помощью PowerShell

Используйте командлет [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport), чтобы отправить запрос на экспорт базы данных в службу "База данных SQL Azure". Операция экспорта может занять некоторое время в зависимости от размера базы данных.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Чтобы проверить состояние запроса на экспорт, используйте командлет [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus). Если выполнить его немедленно после запроса, то обычно возвращается сообщение **Состояние: выполняется**. Отображение сообщения **Состояние: выполнен** означает, что экспорт завершен.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")   
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о долгосрочном хранении резервных копий базы данных SQL Azure как альтернативе экспорту базы данных для создания архива приведены в статье [Хранение резервных копий базы данных SQL Azure до 10 лет](sql-database-long-term-retention.md).
- Сведения о миграции из SQL Server в базу данных SQL Azure с использованием BACPAC-файлов см. в [блоге группы консультирования клиентов SQL Server](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* Чтобы узнать об импорте BACPAC-файла в базу данных SQL Server, ознакомьтесь с разделом [Импорт файла BACPAC для создания новой пользовательской базы данных](https://msdn.microsoft.com/library/hh710052.aspx).
* Сведения об экспорте BACPAC-файла из базы данных SQL Server см. в разделах [Export a Data-tier Application](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) (Экспорт приложения уровня данных) и [Migrate your SQL Server database to Azure SQL Database](sql-database-migrate-your-sql-server-database.md) (Перенос базы данных SQL Server в базу данных SQL Azure).
* Если вы экспортируете данные из SQL Server, чтобы потом перенести их в базу данных SQL Azure, см. в статью [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md) (Миграция базы данных SQL Server в базу данных SQL).
* Чтобы узнать, как безопасно управлять ключами к хранилищу данных и	подписанными URL-адресами и совместно их использовать, ознакомьтесь с [руководством по безопасности службы хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
