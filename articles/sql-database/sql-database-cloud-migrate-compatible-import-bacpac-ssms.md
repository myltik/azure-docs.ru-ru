<properties
   pageTitle="Перенос базы данных SQL Server в базу данных SQL Azure"
   description="База данных SQL Microsoft Azure, развертывание базы данных, миграция базы данных, импорт базы данных, экспорт базы данных, мастер миграции"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Импорт из BACPAC-файла в Базу данных SQL с помощью SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Портал Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

В этой статье показано, как импортировать данные в Базу данных SQL Server из [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файла, используя мастер экспорта приложений на уровне данных в среде SQL Server Management Studio.

> [AZURE.NOTE] При выполнении указанных ниже действий предполагается, что вы уже подготовили логический экземпляр SQL Azure и у вас есть сведения о подключении.

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.

	 > [AZURE.IMPORTANT] Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Подключитесь к серверу базы данных SQL Azure, щелкните правой кнопкой мыши папку **Databases** и выберите команду **Импорт приложения уровня данных…**.

    ![Импорт пункта меню приложения уровня данных](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.	В мастере импорта импортируйте BACPAC-файл с локального диска или выберите учетную запись хранения Azure и контейнер, в который был передан BACPAC-файл, чтобы создать новую базу данных в базе данных SQL Azure.

    ![Импорт параметров](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

    ***Важно***. При импорте BACPAC-файла из хранилища BLOB-объектов используйте хранилище уровня "Стандартный". Импорт BACPAC-файла из хранилища уровня "Премиум" не поддерживается.

4.	Укажите **Имя новой базы данных** для базы данных в Базе данных SQL Azure, установите параметры **Выпуск Базы данных SQL Microsoft Azure** (уровень службы), **Максимальный размер базы данных** и **Цель обслуживания** (уровень производительности).

    ![Параметры базы данных](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.	Нажмите кнопку **Далее**, а затем кнопку **Готово** для импорта BACPAC-файла в новую базу данных на сервере Базы данных SQL Azure.

6. В обозревателе объектов подключитесь к перенесенной базе данных на сервере базы данных SQL Azure.

6.	На портале Azure просмотрите базу данных и ее свойства.

## Дальнейшие действия

- [Последняя версия SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Последняя версия SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Подробнее

- [База данных SQL версии 12](sql-database-v12-whats-new.md).
- [Частично или полностью неподдерживаемые функции Transact-SQL](sql-database-transact-sql-information.md).
- [Migrate non-SQL Server databases using SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/) (Миграция баз данных не на основе SQL Server с помощью помощника по миграции SQL Server).

<!---HONumber=AcomDC_0601_2016-->