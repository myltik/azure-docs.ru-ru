
<properties
   pageTitle="Экспорт базы данных SQL Server в BACPAC-файл с помощью SSMS"
   description="База данных SQL Microsoft Azure, миграция базы данных, экспорт базы данных, экспорт BACPAC-файла, мастер экспорта приложений на уровне данных "
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Экспорт базы данных SQL Server в BACPAC-файл с помощью SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
В этой статье показано, как экспортировать базу данных SQL Server в [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-файл, используя мастер экспорта приложений на уровне данных в среде SQL Server Management Studio.

1. Убедитесь, что у вас установлена последняя версия среды SQL Server Management Studio. Новые версии среды Management Studio обновляются ежемесячно для поддержания синхронизации с обновлениями портала Azure.

	 > [AZURE.IMPORTANT] Чтобы обеспечить синхронизацию с обновлениями Microsoft Azure и Базой данных SQL, рекомендуется всегда использовать последнюю версию Management Studio. [Обновите среду SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Откройте среду Management Studio и подключитесь к исходной базе данных в обозревателе объектов.

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. В обозревателе объектов щелкните правой кнопкой мыши исходную базу данных, наведите указатель мыши на пункт **Задачи** и выберите команду **Экспорт приложения уровня данных...**.

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. В окне мастера экспорта настройте сохранение BACPAC-файла на локальный диск или в большой двоичный объект Azure. Экспортированный файл BACPAC всегда содержит полную схему базы данных и по умолчанию данные из всех таблиц. Воспользуйтесь вкладкой "Дополнительно", если необходимо исключить данные из некоторых или всех таблиц. Например, можно экспортировать только данные для ссылочных таблиц, а не для всех таблиц.

	![Параметры экспорта](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## Следующий шаг: импорт из BACPAC-файла в Базу данных SQL

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Портал Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_0316_2016-->