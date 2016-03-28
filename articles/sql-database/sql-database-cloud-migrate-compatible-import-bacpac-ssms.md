<properties
   pageTitle="Перенос базы данных SQL Server в базу данных SQL Azure"
   description="База данных SQL Microsoft Azure, развертывание базы данных, миграция базы данных, импорт базы данных, экспорт базы данных, мастер миграции"
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

2. Откройте среду Management Studio и подключитесь к исходной базе данных в обозревателе объектов.

	![Экспорт приложения уровня данных из меню "Задачи"](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. После создания BACPAC-файла подключитесь к серверу Базы данных SQL Azure, щелкните правой кнопкой мыши папку **базы данных** и выберите команду **Импорт приложения уровня данных...**.

    ![Импорт пункта меню приложения уровня данных](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	В мастере импорта выберите файл BACPAC-файл, который вы экспортировали, для создания новой базы данных в Базе данных SQL Azure.

    ![Импорт параметров](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Укажите **Имя новой базы данных** для базы данных в Базе данных SQL Azure, установите параметры **Выпуск Базы данных SQL Microsoft Azure** (уровень службы), **Максимальный размер базы данных** и **Цель обслуживания** (уровень производительности).

    ![Параметры базы данных](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	Нажмите кнопку **Далее**, а затем кнопку **Готово** для импорта BACPAC-файла в новую базу данных на сервере Базы данных SQL Azure.

7. В обозревателе объектов подключитесь к перенесенной базе данных на сервере базы данных SQL Azure.

8.	На портале Azure просмотрите базу данных и ее свойства.

<!---HONumber=AcomDC_0316_2016-->